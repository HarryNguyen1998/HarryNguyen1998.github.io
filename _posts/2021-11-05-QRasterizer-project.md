---
layout: portfolio_entry
title: QRasterizer
image: /img/QRasterizer/Thumbnail.png
permalink: blog/qrasterizer 
use-math: true
github-link: https://github.com/HarryNguyen1998/QRasterizer

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

Some pictures during the development process:
<table>
<tr>
  <td> <img src="/img/QRasterizer/wireframe.png" alt="Wireframe" style="width: 125px;"/>
  <figcaption>Wireframe rendering</figcaption>
  </td>
  <td> <img src="/img/QRasterizer/z-buffer.png" alt="z-buffer" style="width: 125px;"/>
  <figcaption>z-buffer</figcaption>
  </td>
</tr>
<tr>
<td> <img src="/img/QRasterizer/flat-shading.png" alt="flat-shading" style="width: 125px;"/>
  <figcaption>flat shading</figcaption></td>
<td> <img src="/img/QRasterizer/texture-and-flat-shading.png" alt="texture with flat shading" style="width: 125px;;"/> 
  <figcaption>texture mapping</figcaption>
  </td>
<td> <img src="/img/QRasterizer/Thumbnail.png" alt="Demo" style="width: 125px;"/>
  <figcaption>Full demo</figcaption>
</td>
</tr></table>


### Postmortem

#### C++ tidbits:
`<cmath>` doesn't support `constexpr`, e.g., `std::abs`, so I could only partially apply `constexpr`
in my math library

`initializer_list` trivia: given `foo f = {2, 3, 4};`, what would happen if I don't provide any copy constructor? 
- It will convert the list to a temp `Foo` using `initializer_list` constructor, then it passes that
  as an argument to the implicitly generated copy assignment member function. Guess what happens
  next? If your class has pointers, boom!
- The lesson is when using `initializer_list`, check if you have any pointer around and decide
  whether to change to smart pointers, or use RO3

Pass by value + `std::move` save typings, since it will optimally move or copy

Smart pointers can be used on C functions in library like SDL2 (courtesy to [1][1] & [2][2]):
``` c++
struct SDL_Deleter
{
   void operator()(SDL_Window *window)
   {
         SDL_DestroyWindow(window);
   }
};

// In Foo.h
std::unique_ptr<SDL_Window, decltype(&SDL_DestroyWindow)> m_window;

// In Foo.cpp
Foo() : m_window(nullptr, SDL_DestroyWindow)
{
    m_window.reset(SDL_CreateWindow(...));
}

```

When working with templates, we want to **deactivate** some functions if template type is of some
type. This can be leveraged with `std::enable_if_t` (this [link][3] explains more in detail):
``` c++
// Code excerpt: The Cross product is only generated when working with 3D vectors. If you
// accidentally use it on 2D vectors, the compiler won't generate the function andd error is caught
// at compile time
template<typename T, size_t Size, typename = std::enable_if_t<(Size == 3)>>
constexpr Vector<T, Size> Cross(const Vector<T, Size>& v1, const Vector<T, Size>& v2)
{
      return Vector<T, Size>{
          v1[1] * v2[2] - v1[2] * v2[1],
          v1[2] * v2[0] - v1[0] * v2[2],
          v1[0] * v2[1] - v1[1] * v2[0]};
}

```

#### Computer Graphics tidbits
<embed src="/img/QRasterizer/point-flow.svg" type="image/svg+xml" />



### Resources
- Use SDL2 with smart pointers: [1][1] and [2][2]
- The holy bible: [Scratchapixel](https://www.scratchapixel.com/index.php?redirect)

[1]: https://stackoverflow.com/questions/48672399/is-it-possible-to-use-sdl2-with-smart-pointers
[2]: https://stackoverflow.com/questions/24251747/smart-pointers-with-sdl
[3]: https://www.fluentcpp.com/2018/05/15/make-sfinae-pretty-1-what-value-sfinae-brings-to-code/
