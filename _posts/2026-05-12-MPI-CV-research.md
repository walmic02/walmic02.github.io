---
layout: post
title: Synthetic Data Generation Pipeline for Training Computer Vision Magnetic Particle Inspection (MPI) Detection Systems
description: Research project
date: 2026-05-12 12:00 -0400
categories: [Research]
tags: [computer vision, Python, Blender]
math: true
image:
  path: assets/posts/MPI-CV-research/temp-cover-image.png
---


## Introduction

Magnetic Particle Inspection (MPI) is an non-destructive testing method used to detect surface and subsurface defects in ferromagnetic materials such as iron and steel, and is a widely used method in metal foundries to identify and remedy casting imperfections. MPI involves magnetizing a ferromagnetic material and then applying fine fluorescent ferromagnetic particles to the surface. Defects in the material, such as cracks or voids, disrupt the magnetic field and cause florescent particles to collect there. This forms an indication of a defect, which fluoresces under ultraviolet light [1]. However, this inspection process is reliant on operators visually identifying and recording indications, and thus is subject to human error. For this reason, automating the identification of MPI indications using a computer vision system is desirable, offering to save time and increase accuracy, and in turn increasing the safety of products.

Training a robust computer vision model for this usage requires a vast amount of labeled images, of which acquiring is difficult and tedious. Foundries have restrictions on recording their products (the intellectual property of their customers), and labeling a large dataset manually is time consuming. Consequently, automating the generation and labeling of images is advantageous and would speeding up training significantly.

## Pipeline Overview

Blender (v5.1), a 3D rendering software, is used to simulate the virtual inspection booth and generate the training images. The main goals of the Blender pipeline are as follows:

* Realistic image production (part appearance, lighting, glare, background, etc.)
* Large batch sizes
* Accurate labeling
* Consistent and controllable randomization
* Batch feedback (total time, average time per image, failed generation count)
* Simple customization (different parts, visual tweaks, etc.)

<div style="float:left; width:400px; margin-left:-15px; margin-right:20px; margin-bottom:10px;">
  <img src="assets/posts/MPI-CV-research/Figures/flowchart_v2.png" width="390">
  <p style="text-align: center; margin-top: 2px; font-size: 17px; color: #6d6c6c;">
    Figure 1: Flowchart for Python script.
  </p>
</div>

The entire workflow exists in a single .blend file, which contains four key parts:

1. 3D Blender environment containing a generic part model
2. Material used for the surface of part, which contains the indication generation
3. Compositor used to separate indication from image for labeling and to write renders to files
4. Python Script used to automate entire rendering and generation process

The flowchart in Figure 1 describes the function of the blender workflow. In the script, many parameters are specified; some significant parameters include: batch size, output resolution(s), minimum indication size, and maximum camera rotation along with other randomization parameters. When the script is executed, it begins by randomizing various elements of the Blender environment. The size, shape, and position of the indication are randomized through random noise nodes in the material, and the surface texture is varied in a similar way. The camera is also rotated randomly in the X, Y, and Z directions within a set range of degrees, and the HDRI background is randomly rotated around the Z axis. The render and two .png outputs are then created; one is the composite containing the full render, and the other contains only the indication.

After both images are created, the image containing only the indication is checked to ensure that an indication of sufficient size is present. If the render fails, the randomization and rendering process is repeated until a successful arrangement is created. When the render succeeds, a label is then created using the indication image and saved to a .json file, then the indication image is deleted. If multiple output resolutions were entered, then the rendering and labeling process is repeated for each subsequent resolution *without* randomizing any scene elements. Once all resolutions have been rendered, the whole process is repeated for the specified batch size, and when all batches are complete, various statistics are stored in a log file along with rendering information.

## Render Design Methodology

In order to make renders as realistic as possible, many iterations were created and references from various foundries were used to improve accuracy; however these references cannot be shared in this report due to privacy reasons.

Generation of the indication was conducted first. Noise nodes in the material tree were used to create random procedural generation. Initially the Voronoi Texture Node was used, but it yielded cracks that were too straight and interconnected. The 4D Noise Texture Node yielded much better results as seen in Figure 2, but had too much variation: the indications sometimes looped around on themselves and was difficult to control the location of the indication due to the random noise. Ultimately, the Noise Texture Node was combined with a linear Gradient Texture Node, which made the indication more straight and allowed for accurate location and rotation control (Figure 3). The indication was set to be an emissive texture, causing it to slightly glow and allowing it to be separated from the composite render into an image containing only the indication used for labeling.

<div style="display:flex; gap:16px; justify-content:center; align-items:flex-start; max-width:550px; margin:0 auto;">
  <div style="flex:1; text-align:center;">
    <img src="assets/posts/MPI-CV-research/Figures/renders/voronoi.png"
         style="max-height:280px; width:auto; max-width:100%; display:block; margin:auto;">
    <div style="margin-top:4px; font-size:16px; font-weight:600; color:#6d6c6c;">
      (a)
    </div>
  </div>
  <div style="flex:1; text-align:center;">
    <img src="assets/posts/MPI-CV-research/Figures/renders/noise.png"
         style="max-height:280px; width:auto; max-width:100%; display:block; margin:auto;">
    <div style="margin-top:4px; font-size:16px; font-weight:600; color:#6d6c6c;">
      (b)
    </div>
  </div>
</div>
<p style="text-align:center; margin-top:6px; font-size:17px; color:#6d6c6c;">
Figure 2: Comparison of (a) Voronoi and (b) Noise Texture Nodes for indication generation.
</p>

<div style="display: flex; gap: 16px; justify-content: center; align-items: flex-start; width: 100%; max-width: 600px; margin: 0 auto;">

  <div style="flex: 1;">
    <div style="display: grid; grid-template-columns: repeat(2,1fr); gap: 6px;">
      <img src="assets/posts/MPI-CV-research/Figures/renders/no_linear/render_4.png"
           style="width:100%; display:block;">
      <img src="assets/posts/MPI-CV-research/Figures/renders/no_linear/render_6.png"
           style="width:100%; display:block;">
      <img src="assets/posts/MPI-CV-research/Figures/renders/no_linear/render_7.png"
           style="width:100%; display:block;">
      <img src="assets/posts/MPI-CV-research/Figures/renders/no_linear/render_13.png"
           style="width:100%; display:block;">
    </div>
    <div style="text-align:center; font-size:16px; font-weight:600; margin-top:4px; color:#6d6c6c;">(a)</div>
  </div>

  <div style="flex: 1;">
    <div style="display:grid; grid-template-columns:repeat(2,1fr); gap:6px;">
      <img src="assets/posts/MPI-CV-research/Figures/renders/linear/render_11.png"
           style="width:100%; display:block;">
      <img src="assets/posts/MPI-CV-research/Figures/renders/linear/render_17.png"
           style="width:100%; display:block;">
      <img src="assets/posts/MPI-CV-research/Figures/renders/linear/render_22.png"
           style="width:100%; display:block;">
      <img src="assets/posts/MPI-CV-research/Figures/renders/linear/render_46.png"
           style="width:100%; display:block;">
    </div>
    <div style="text-align:center; font-size:16px; font-weight:600; margin-top:4px; color:#6d6c6c;">(b)</div>
  </div>
</div>
<p style="text-align: center; margin-top: 2px; font-size: 17px; color: #6d6c6c;">
  Figure 3: Comparison of indication generation without linear gradient texture (a) and with linear gradient texture (b).
</p>

In order to create a realistic replica of a metal casting, the indication is combined with a background material which represents the metal surface. The metal material component has various parameters to make it appear realistic, which are all applied through a Principled BSDF shader node. First, the gray Base Color is created, with variations from a Noise Texture output through a Color Ramp causing slight variations in gray surface color. The Roughness value is also randomized with a Noise Texture to add surface reflectivity variation. The surface Normal is randomized with a Noise Texture output into a Bump node to give the surface a random surface texture variation. Finally, there is a Coat layer used to replicate the MPI fluid on the parts, adding some surface glare. All of the Noise Texture nodes are 4D noise nodes, which adds an extra parameter used to change the noise for each render; this gives each render a completely unique surface texture.

The 3D Virtual Inspection Booth environment exists inside Blender, made up of three main components:

1. 3D part model
2. Camera/light object
3. HDRI background

The 3D part model can be any 3D model desired for rendering; in this case it is a simple, low-poly .stl file created in SOLIDWORKS, seen in Figure 4. The camera object is what is used by the renderer to capture an image, and the light object is bound to it so they move together; these can also be seen in Figure 4. The light is set to be a flourescent green similar to a UV light used in MPI testing. The HDRI (High Dynamic Range Imaging) background is a realistic 360 degree image used as the surrounding environment, providing realistic noise to the background and reflections on the part (Figure 5).

<div style="display:flex; gap:16px; justify-content:center; align-items:flex-start; max-width:800px; margin:0 auto;">
  <div style="flex:1; text-align:center;">
    <img src="assets/posts/MPI-CV-research/Figures/viewport_solid.png"
         style="width:auto; max-width:100%; display:block; margin:auto;">
    <div style="margin-top:4px; font-size:16px; font-weight:600; color:#6d6c6c;">
      (a)
    </div>
  </div>
  <div style="flex:1; text-align:center;">
    <img src="assets/posts/MPI-CV-research/Figures/viewport_rendered.png"
         style="width:auto; max-width:100%; display:block; margin:auto;">
    <div style="margin-top:4px; font-size:16px; font-weight:600; color:#6d6c6c;">
      (b)
    </div>
  </div>
</div>
<p style="text-align:center; margin-top:6px; font-size:17px; color:#6d6c6c;">
Figure 4: (a) The 3D object in the Blender 3D viewport. (b) The same 3D object with rendered textures enabled.
</p>

![The HDRI used, sourced from Poly Haven](assets/posts/MPI-CV-research/Figures/industrial_pipe_and_valve_01_screenshot.png)
*Figure 5: The HDRI used, sourced from Poly Haven [2]. Brightness was reduced significantly in renders.*

In order to export two layers of the render simultaneously, Blender's Compositing was used. There, the render is exported as both the composite render and the emissive layer, the former is the complete render and the latter contains only the indication and is used for labeling.

## Discussion
<table style="width: 100%; border-collapse: collapse;">
  <tr>
    <td style="text-align: center; width: 25%; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_1_1.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(a)</div></td>
    <td style="text-align: center; width: 25%; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_1_2.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(b)</div></td>
    <td style="text-align: center; width: 25%; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_1_3.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(c)</div></td>
    <td style="text-align: center; width: 25%; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_1_4.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(d)</div></td>
  </tr>
  <tr>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_2_1.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(e)</div></td>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_3_1.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(f)</div></td>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_3_2.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(g)</div></td>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_3_3.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(h)</div></td>
  </tr>
  <tr>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_3_4.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(i)</div></td>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_4_1.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(j)</div></td>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_4_2.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(k)</div></td>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_4_3.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(l)</div></td>
  </tr>
  <tr>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_4_4.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(m)</div></td>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_4_5.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(n)</div></td>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_5_1.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(o)</div></td>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_6_1.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(p)</div></td>
  </tr>
  <tr>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_6_2_alt.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(q)</div></td>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_6_2.png" width="85%"/><div style="font-size: 15px; font-weight: 600">(r)</div></td>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_6_3.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(s)</div></td>
    <td style="text-align: center; padding: 8px;"><img src="assets/posts/MPI-CV-research/Figures/renders/gen_timeline/gen_6_4.png" width="85%"/><div style="font-size: 15px; font-weight: 600;">(t)</div></td>
  </tr>
</table>

<p style="text-align: center; margin-top: 15px; font-size: 17px; color: #6d6c6c;">
  Figure 6: Chronological renders showing generation evolution.
</p>

Generation methodology evolved greatly over time; select examples are shown in Figure 6. All renders were done on the same *.st*l model, only changing where on the model they were generated. One factor which was continuously adjusted was the surface texture. In Figure 6a, the scale of the surface noise and topology variations are both low, while both were increased in 6b. Figure 6j shows another surface iteration, which was quickly discarded for being unrealistic. Another change involved lighting and color: up until 6o the source of the green color was the surface of the part and the light was just white, while afterwards the surface was changed to gray and the light was made green. This method required more fine tuning to achieve realistic results but ended up proving superior. Additionally, all renders before Figure 6p utilized a high-scale Voronoi noise node to add small dots of bright green onto the darker green base color; these specks were added to replicate the surfaces of some reference images, but it was later discovered that this could be better achieved through the surface noise alone. Figure 6k had the Voronoi specks scaled to be incredibly small which created the appearance of the MPI particles, however most reference images did not match this appearance so it was not continued. Another change that began with Figure 6p was the addition of a reflective surface coat, which was intended to replicate the liquid MPI fluid. Making the surface coat realistic proved very challenging, as there are many parameters to adjust; some changes in the reflectivity parameters, including roughness, IOR (Index of Reflectivity), and coat surface variation (how closely its surface matches the topology of the surface below), are visible in Figures 6p through 6t. A wooden pallette was introduced for background variation in Figure 6e but was removed in 6p in favor of the HDRI background. The indication generation remained mostly consistent throughout all generations, a notable change is visible beginning in Figure 6p, where the indications are more straight and wavy, but was reverted back in Figure 6s. Color was slightly adjusted throughout all generations in order to best match reference images; a more visible iteration can be seen in Figure 6o.

## Conclusion

This project successfully developed a pipeline capable of generating large amounts of synthetic MPI images with corresponding labels for use in computer vision training. The resulting datasets exhibit lots of variety, making it ideal for training a CV model. Initial results suggest that incorporating synthetic images into training process produces some benefits over relying exclusively on real-world data, though the exact extent of this benefit is currently unclear. Continued work of the pipeline would entail refinement of visuals to increase realism and adding variety with new part models; these improvements would likely lead to benefits with model training.

Overall, this work suggests that synthetic datasets can be a valuable asset to training CV models, especially when acquiring real datasets is difficult or impossible.

## References

[1] The American Society for Nondestructive Testing Inc. "Magnetic Particle Testing: An Essential Method for NDT." https://www.asnt.org/what-is-nondestructive-testing/methods/magnetic-particle-testing. Accessed: 2026-04-02.

[2] Poly Haven. "Industrial Pipe & Valve 01." 2020. https://polyhaven.com/a/industrial_pipe_and_valve_01. Licensed under CC0 1.0 Universal (Public Domain). Retrieved April 2026.