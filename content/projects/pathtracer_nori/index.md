---
title: "Physically based Pathtracer"
date: 2024-12-20T18:12:46+01:00
featured: true
weight: 3

tags: ["C++", "Pathtracing"]
---

<link rel="stylesheet" type="text/css" href="/portfolio_hugo/beerslider/BeerSlider.css">

A physically based pathtracer built in C++ as part of ETH Zürich's computer graphics course, extended with advanced rendering features including volumetric media, Disney BRDF, and low-discrepancy sampling. The renderer is based on [Nori](https://wjakob.github.io/nori/), a minimal pathtracer 
framework providing model loading, ray-triangle intersection, and BVH acceleration. During the homeworks I implemented features such as diffuse, specular and dielectic materials (with multiple importance sampling), and photon mapping. he final project was done in a group 
of two with my friend contributed environment map and many-light sampling.

<div align="center">
    <img src="featured.png">
    Final renderer
</div>

I will now discuss the interesting features implemented by me.

## Halton Sampling
We replace Uniform sampling with Halton sampling due to its low discrepancy. Halton sampling is done using [Halton Sequences](https://en.wikipedia.org/wiki/Halton_sequence). For a sample we compute the n-th number (index) in the halton sequence of base k. For the next sample we use the next prime as base. To avoid correlation we pick a random index depending on the pixel position and permute the sequence of prime bases.

<div align="center">
  <img src="Warp Independent.png">
  <img src="Warp Halton 2 3.png">

  Uniform (top) compared to Halton sampling (bottom; base 2 and 3)
</div>

Here a comparison between uniform and halton sampling both with 16 sample per pixel (spp).
<div id="slider1" class="beer-slider" data-beer-label="Halton">
  <img src="/portfolio_hugo/images/pathtracer/path_halton.png" alt="">

  <div class="beer-reveal" data-beer-label="Independent">
    <img src="/portfolio_hugo/pathtracer/path_independent.png" alt="">
  </div>
</div>

While Halton sampling is more expensive it still performs better when compared to uniform sampling using the same render time of 1.2 min. The Independent sampler used 1412 spp and Halton 1024.

<div id="slider1" class="beer-slider" data-beer-label="Halton">
  <img src="/portfolio_hugo/images/pathtracer/path_ref_halton.png" alt="">

  <div class="beer-reveal" data-beer-label="Independent">
    <img src="/portfolio_hugo/images/pathtracer/path_ref_independent 1412.png" alt="">
  </div>
</div>

## Disney BRDF
I implemented the Disney BRDF with the parameters: Roughness, Metallic, Anisotropic, Sheen, Sheen Tint. To check the correctness I compared my rendering with [Mitsuba](https://www.mitsuba-renderer.org/) using a mesh from [here](https://casual-effects.com/data/). Anistropic materials  needed consistent tangent frames so I implemented [Computing Tangent Space Basis Vectors for an Arbitrary Mesh](https://www.cs.upc.edu/~virtual/G/1.%20Teoria/06.%20Textures/Tangent%20Space%20Calculation.pdf).

<div id="slider1" class="beer-slider" data-beer-label="Mitsuba">
  <img src="/portfolio_hugo/images/pathtracer/anisotropic_dir_ref.png" alt="">

  <div class="beer-reveal" data-beer-label="Mine">
    <img src="/portfolio_hugo/images/pathtracer/anisotropic_dir.png" alt="">
  </div>
</div>

Also important to add was texture support for the materials.
<div id="slider1" class="beer-slider" data-beer-label="Mitsuba">
  <img src="/portfolio_hugo/images/pathtracer/texture_path_ref.png" alt="">

  <div class="beer-reveal" data-beer-label="Mine">
    <img src="/portfolio_hugo/images/pathtracer/texture_path.png" alt="">
  </div>
</div>

## Heterogeneous Participating Media
Heterogeneous participating media is rendered using delta tracking and supports isotropic and Henyey-Greenstein phase functions. The code uses [nanovdb](https://github.com/AcademySoftwareFoundation/openvdb/blob/master/doc/nanovdb/doc.md) for loading the volume.

<div align="center">
  <img src="cbox_vol_phase_pos.png">

  Volume with Henyey-Greenstein phase function (g = 0.8)
</div>

<script src="/portfolio_hugo/beerslider/BeerSlider.js"></script>

<script>
  document.querySelectorAll('.beer-slider').forEach(el => {
    new BeerSlider(el);
  });
</script>