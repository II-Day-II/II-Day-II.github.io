# [NMLE](https://ii-day-ii.github.io/nmle) (Anomaly)


## Motivation

This is what I wanted every time I googled "matrix transform calculator" - 
a tool for visualizing the transformation I would see when applying a series of transformation matrices in 2D.
But I never found anything that was easy to use and did exactly what I wanted.
I would try to set up something similar in GeoGebra or Desmos, 
but those aren't very good for working with matrices, 
and getting anything using homogeneous coordinates to work is non-trivial.
In the end, I'd just check my math manually one more time and move on.

Then I worked as a teaching assistant in a course titled *Introduciton to Visualization and Computer Graphics*
at KTH, and noticed a lot of students also had similar moments of frustration when checking their work.
So I decided to make the tool myself using WebGPU via `wgpu` in Rust, 
since I already had experience with it from the [voxel raytracer project](https://ii-day-ii.github.io/voxel_raytracer/),
and knew that it supported building for web.
There are plenty of math libraries that target GPU applications, and GPUs are well acquanited with matrix transformations, so the main challenges in this project would be: 

## Goals

1. Code structure. The voxel raytracer project was quite messy, so I had high ambitions for making something more readable this time.
2. Web support. Since I wanted anyone to be able to use this without having to download a sketchy executable, I wanted to target web, which I hadn't done before.
3. A grid shader. Desmos and GeoGebra have background grids that scale as you zoom in and out, and I wanted something similar. As it turns out, the math for fading in different levels of detail is quite interesting.

## Implementation

But I forgot about the project quite early on, as I had a lot going on at the time. 
Only now, ~1.5 years later, did I come back and finish it, though I did branch it off somewhere in between then and now to experiment with 2D radiance cascades.

### The early days

Back then I only got through point 1, and in hindsight, I didn't do it very well. 
While the project is structured neatly at a glance, once I started looking at how 
the modules interacted with one another, I realized past me had no idea what they 
were doing. I was very tempted to start over from the beginning, 
but held myself back and only changed what definitely wasn't working. 
Highlights include relying on hashmap iterators running in insertion order (they don't) to order descriptor sets for the shaders, which of course worked fine back then because I only ever had one entry in the hashmap. 🤦

But I also made the decision to write my shaders in glsl instead of wgsl, as my past experience with wgsl wasn't very positive. 

### Web support

Now I've also finished web support and the grid shader. 
Web support was relatively easy, but I did run into some trouble. 
Back when I started, WebGPU was only really available in dev builds of the major browsers, 
but I didn't concern myself with it then, as `wgpu` can target WebGL2 as well.
Now that WebGPU is available in Chrome and Firefox, I thought I might as well target it.
So I went and made a `wasm-bindgen` entry point, made some code conditionally  compile when targeting wasm, 
(like acquiring a GPU handle, 
which is async and requires me to use the browser's async runner instead of just blocking like I do in the native version;
and inserting a canvas inserting a canvas to draw to, 
which there are a bazillion ways to do and all the resources expect you to know enough about webdev that this is trivial) 
installed `wasm-pack` to generate the javascript glue that actually calls my rust code, and tested it out on localhost. 
And nothing. Apparently the WebGPU spec had changed since I started, 
so my old version of `wgpu` couldn't have produced a working WebGPU build anyway.

This was a crossroads: either target WebGL2 instead, or update to a newer version of `wgpu`.
I went with the latter. Which involved a brief trek through the outskirts of dependency hell.
I used `egui` for the user interface, allowing easy manipulation of the transform matrices and the order in which they're applied.
`egui` interops with `wgpu` through another library, `egui-wgpu`, which needed to be updated when `wgpu` was updated. This in turn required a newer version of `egui`, which also interacts with the windowing and event system library `winit` through `egui-winit`. Updating `egui` meant I needed a newer version of `egui-winit`, which culminated in needing to update `winit` from version 0.29 to version 0.30. 
That final `winit` update has some **MAJOR** breaking changes. 
A large chunk of my code was completely incompatible with `winit` 0.30, 
which meant a partial rewrite of my windowing, event handling, and frame timing code.
Sadly, that was probably the cleanest code I had left from the start of the project, so the code quality didn't even improve all that much. 
But! It now works in the browser!

### The grid shader
Making a grid shader might seem trivial, just go with something like:

```glsl
vec3 grid(vec2 world_position, float zoom, float thickness, float cell_size) {
    world_position += 0.5 * thickness / zoom; // offset by half a line width to center lines
    vec2 grid_pos = mod(world_position, cell_size); // repeat position every cell_size worldspace units
    vec2 grid_line = step(grid_pos, vec2(thickness / zoom)); // white when on the line, else black
    return vec3(min(1.0, grid_line.x + grid_line.y));
}
```

And you'd be right. This does generate a grid, and it will scale with zoom.
But now, say you also want a finer grid to fade in as you zoom in, and a coarser grid when you zoom out?

Let's say you know your zoom level will be within the range [0.001, 10]
and you want each grid level to be larger or bigger than its neighbors by a factor of 10. Keep in mind that less zoom means we see more of the world, meaning we want bigger cell sizes.
This corresponds to an inverse logarithmic mapping from zoom to cell size, but we'll also need to step to the nearest multiple of 10.
Assuming we only ever need to see two grid sizes at once (the others will be too large to fit any one cell on screen), we can simply draw two grids at two of these levels and interpolate between them based on how far between the selected zoom levels we are.

We get something like this: 
```glsl
vec3 fading_grids(vec2 world_position, float zoom) {
    const float CELL_SIZE_FACTOR = 10.0;
    const float LINE_THICKNESS = 0.010;
    const float LOG10_MIN_ZOOM = -3.0; // log10(0.001)
    float grid_level = log(zoom) / log(CELL_SIZE_FACTOR) + LOG10_MIN_ZOOM ; // log base 10 of our zoom offset to positive values, [0, 4]
    floor grid_floor = floor(grid_level); // the size index for the closest larger grid. We know the next level is this + 1
    float t = grid_level - grid_floor; // how far between the grid levels are we zoomed?
    // determine the cell sizes for our 2 grids
    float cell_size_large = pow(CELL_SIZE_FACTOR, -LOG10_MIN_ZOOM - grid_floor);
    float cell_size_small = cell_size_large / CELL_SIZE_FACTOR;
    // determine the line thickness for our 2 grids
    float thickness_large = (1.0 - t) * LINE_THICKNESS;
    float thickness_small = t * LINE_THICKNESS;

    // draw our 2 grids
    vec3 grid_large = grid(world_position, zoom, thickness_large, cell_size_large);
    vec3 grid_small = grid(world_position, zoom, thickness_small, cell_size_small);
    // determine the final color by interpolating
    return mix(grid_large, grid_small, t);
}
```