---
layout: portfolio_entry
title: QRasterizer
image: /img/QRasterizer/Thumbnail.png
use-math: true
---

This is a software renderer/rasterizer that emulates how a graphics API work. To be more precise, it
is a "clone" that mimics how OpenGL/DirectX work behind the scene by providing its own
implementation. The project is built mostly from "scratch" using C++14

### Introduction
The goal of this project is to unveil the black box behind the rendering pipeline. Although I only
implemented a few features, the lessons I learned was numerous: grow my C++ skills, more courage to
do math, the adrenaline rush of tackling challenging problems, and make me more comfortable when
tackling a graphics API

<style>
td, th {
   border: none!important;
}
</style>

| Features | Back-end features |
| -------- | ----------------- |
| Wireframe rendering | Line drawing |
| Flat Shading | Linear Algebra library |
| Wavefront .obj file loader | Z-Buffer |
| | Perspective correct interpolation |
| | Back-face culling |
| | Rasterizer |
| | Clipping |

Gallery:



### Work Summary
For those who are a bit interested in the specifics:

$ \dfrac 1Z = \dfrac{1}{Z_0}(1-q) + \dfrac{1}{Z_1}q $


