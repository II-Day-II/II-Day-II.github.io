# Portfolio

---
## About me

Hello! I am a computer science student at KTH Royal Institute of Technology
in Stockholm, Sweden, specializing in computer graphics, and am currently closing in on graduation at the time of writing. 

I enjoy many things, including video games, reading, music, photography, and programming. 
Below, you'll find a collection of links to projects I've worked on, and "finished," to some degree. 

Feel free to check out my GitHub for a peek at some of the ideas I haven't found as much time for!

---

## Recent projects (Since starting my master's programme)

Here are a few of the projects I've worked on during my time at KTH, within and outside the boundaries of my studies. Click the blue project titles for more information on the projects!

### [NMLE](/nmle.md) (Anomaly)

(The link above leads to some reflections and implementation details on the project)

![Screenshot of NMLE](/images/nmle_demo.png)

NMLE is a visualization tool for matrix transforms in 2D. 
It runs in WebGPU-enabled browsers! Click [here](https://ii-day-ii.github.io/nmle) to try it out! 
Use the mouse to pan and scroll to zoom, then transform the rainbow square using the transform stack by editing the values of the matrix. 
You can add more transformations, selecting from defaults such as rotation,
scale and translation, or with finer control by adding custom matrices. 
They will be applied from bottom to top and can be reordered by dragging their titles.

### Master's Thesis - Probe Placement Strategies for SPWI Radiance Cascades

Code for this project is available [here](https://github.com/II-Day-II/Capsaicin-mex/tree/rc).

For my Master's Thesis and Degree Project at KTH, I worked with Radiance Cascades with Screen-space Probes and World-space Intervals (SPWI). 
This is a novel approach to Global Illumination, where far-field information is gathered at a higher angular frequency than near-field, 
but a lower spatial frequency, 
to maintain a low memory footprint. 

I investigated methods for placing probes in screen-space to discern where to gather the lighting from. It has not yet been published, but keep a look out here!

I implemented SPWI Radiance Cascades from scratch in AMD's Capsaicin framework, which is based on DirectX 12,
which taught me a lot about graphics techniques. 
I also got to learn a lot more about the details of GPU programming from using NVIDIA Nsight to profile my application, and Microsoft PIX to debug my shader code.

In the end, SPWI isn't practical for real-time rendering at the state I got it to, 
as it uses too hardware ray-tracing, tracing too many rays per frame. Image quality isn't competitive with other concurrent methods either, but some more development work could improve that.
On the NVIDIA RTX 2070 I used for evaluation, hardware raytracing isn't very fast, but testing on an RTX 4080 Super revealed frame times an order of magnitude better.
But most computers don't have such expensive graphics cards, so that doesn't really matter. Maybe in a few years. 
Until then, a screenspace approach would be more reasonable - Path of Exile 2 already proved SPSI works well.

### [IT MUST BE FOUND](https://ii-day-ii.itch.io/it-must-be-found)

During summer, I decided to participate in the [JADS Game Jam 2025](https://itch.io/jam/jads-game-jam-2025), a one week game jam for teams of 1-4 members.

My goal was to see if I could design some gameplay mechanics around Radiance Cascades, the topic of my degree project, and to actually finish something, 
as my morale was diminishing after spending so long on the degree project without reaching a conclusion.

The game has 5 levels, unique graphics compared to the rest of the entries (largely because I worked alone and only know simple programmer art),
and music composed by me. 
This was my first time composing a full song, which was a lot of fun.

The gameplay is functional, but nothing special. I definitely made it too difficult
With the time I had to design the game, 
I wasn't able to think through the visibility-based gameplay I had initially thought of.
The limitations of screen-space 2D GI, where off-screen light sources aren't accounted for made for some jarring flashes when the camera moves.
I'd have had to have more time to iterate on the gameplay and graphics to achieve the original vision.


### [Pyzzle](https://github.com/II-Day-II/Pyzzle)

Working as a teaching assistant in a course for undergraduates who have never programmed before, 
I was in charge of exercises, or tutorials, where I taught python programming. 
One of the assignments the students had to solve was one where they were given lines of code on separate pieces of paper, 
and the goal was to put them together into a program that solves a given problem.

Several times, the teaching team found that puzzle pieces had gone missing, 
and we'd have to print them out and cut them up again,
so I took it upon myself to mitigate that problem by making a digital version.

It's written in basic python with no dependencies, 
and is intended to be able to be used as an example of how to write a GUI app with `tkinter`, 
as making a GUI app is required to get the highest grade in the course.

During development, I also found a [bug in Python!](https://github.com/python/cpython/issues/122071)

### [CoVRt & HoVRt](https://druskus20.github.io/covrt-web)
<!--<img src="images/dummy_thumbnail.jpg?raw=true"/>-->

As part of a course on advanced graphics and interaction, two group projects were carried out. 

The first was a 2-player asymmetrical VR+desktop computer game. 
My responsibilities included programming of visual effects for the desktop player, and a 3D fractal the VR player can explore in first person. I learned about raymarching and screenspace transformations, as well as some of the intricacies of working with VR in Unity.

The second was a single-player third-person hoverboarding game, using a real skateboard as a controller. 
Initially a live motion capture solution using a depth camera was intended to animate the player avatar, 
for which I was responsible, 
but due to issues with deprecated hardware SDKs and limited interest from other group members, this was scrapped before project completion. 
I also worked on interior mapping (mimicing a 3D room interior with only 2 triangles and shader magic) to explore methods of improving rendering performance. 

---
### [Voxel Raytracer](https://ii-day-ii.github.io/voxel_raytracer)
<iframe width="560" height="315" src="https://www.youtube.com/embed/K-DfGpbxgds" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen>Link to youtube video</iframe>

As part of a course on computer graphics and interaction, I implemented a stylized real-time raytracing solution for voxel scenes with per-voxel lighting. I learnt a lot about gpu programming and efficient traversal of uniform grids, and it was a lot of fun to implement a unique lighting system with reflecitons.

---
### [Fisher-Man](https://urchin-game.github.io/)
<!--<img src="images/dummy_thumbnail.jpg?raw=true"/>-->

An indie game was proposed and a small vertical slice of it was implemented as part of a course on game design. I implemented swinging physics for the grappling hook, and applied the wave function collapse algorithm for procedural generation of levels. I learnt a lot about communication and structuring and documentation of a game project.

---

## Older projects

- [Bachelor's thesis](https://kth.diva-portal.org/smash/record.jsf?dswid=876&pid=diva2%3A1702458) - detection of copy-move forgeries
- [IS1500](/is1500.md) - Pokémon game on embedded hardware

---

---
<!-- <p style="font-size:11px">Page template forked from <a href="https://github.com/evanca/quick-portfolio">evanca</a></p> -->
<!-- Remove above link if you don't want to attibute -->
