---
title: "Wulkan"
date: 2026-01-29T11:22:10+01:00
description: "a real-time renderer in vulkan to learn new things with"
summary: "summary"
tags: ["vulkan", "real-time rendering", "C++"]
featured: true
draft: true
params:
  math: true
showTableOfContents: true
---

For this project, I build a real time renderer (rasterizer) in Vulkan. Part of the motivation being to learn a lower level / more modern graphics API another being to imitate an image me and my group partner created for the rendering competition at ETH ([click here]() to go to the page about that project). The renderer is a simple forward renderer using Vulkan 1.3 using Dynamic Rendering.

<!-- 

Mention github project, 
learning vulkan
trying to mimick the visual of a raytracer made at eth

-->

# Terrain system

<div align="center">
<table>
  <tr>
    <td>
      <img src="terrain_wireframe.png" width="300">
    </td>
    <td>
      <img src="terrain_tess.png" width="400">
    </td>
  </tr>
  <tr>
    <td colspan="2">
      <em>Shows the wireframe of the terrain (left) and a visualization of tesselation strength (right)</em>
    </td>
  </tr>
</table>
</div>

The terrain uses a height map (along with other maps for visuals). To reduce triangles we don't just want a uniform grid and to then adjust the height based on the texture., but want to support a coarse mesh which is dynamically tesselated using hardware. There are two factors we care about when deciding how much we want to tesselate a terrain patch. The first one being how close the camera is to the patch and the other how much change in height happens within the patch. Whilst distance is easy to compute, how do we quantify the latter part.

Well this sounds a lot like curvature. Something that is easy for 2d functions but a bit more complicated for meshes (or 3d height functions). When loooking into it, we recall the two different curavtures mean and gaussian curavture. Given the principle curvatures \(k_1\) and \(k_2\) (the minimal and maximal curvature of the surface projected on to a tangent plane; see [Wikipedia](https://en.wikipedia.org/wiki/Principal_curvature) for more details), we can compute mean curvature \(H\) and Gaussian curvature \(K\) as follows (in the contiguous case):

<!-- Define gaussian and mean curavutre -->
\[
\begin{aligned}
H &= \frac{k_1 + k_2}{2} \\
K &= k_1 * k_2 
\end{aligned}
\]

We do not want to use gaussian curvature because its zero if either \(k_1\) or \(k_2\) is zero even if there is non zero curvature in the other principle direction. So we want to use mean curvature, but now the question is how do we compute that given our height map. Given our height map \(h(u,v)\) and its first order \(h_u, h_v\) and second order derivatives \(h_{uu}, h_{vv}, h_{uv}\), computed using finite difference, we can compute mean curvature as:

\[
H = \frac{h_{uu} (1 + h_v^2) - 2h_{uv} h_u h_v + h_{vv} (1 + h_u^2)}{2 (1 + h_u^2 + h_v^2)^{3/2}}
\]

We can precompute the curvature on initialization and then we only require one texture look up. For the details on the computation of mean curvature look at [Adaptive Hardware-accelerated Terrain Tessellation](https://www.diva-portal.org/smash/get/diva2:617225/FULLTEXT01.pdf).

# Shadows

# CPU Instancing

<link rel="stylesheet" type="text/css" href="/beerslider/BeerSlider.css">

<div id="slider1" class="beer-slider" data-beer-label="before">
  <img src="/images/wulkan/None1.png" alt="">
  <div class="beer-reveal" data-beer-label="after">
    <img src="/images/wulkan/Rheinhard1.png" alt="">
  </div>
</div>

<div id="slider2" class="beer-slider" data-beer-label="before">
  <img src="/images/wulkan/None1.png" alt="">
  <div class="beer-reveal" data-beer-label="after">
    <img src="/images/wulkan/ExtRheinhard1.png" alt="">
  </div>
</div>

<script src="/beerslider/BeerSlider.js"></script>
<!-- <script>   new BeerSlider(document.getElementById('slider')); </script> -->


<script>
  document.querySelectorAll('.beer-slider').forEach(el => {
    new BeerSlider(el);
  });
</script>

This project is hosted on [Github](https://github.com/Klark007/Wulkan).