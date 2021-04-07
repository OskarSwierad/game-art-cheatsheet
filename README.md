# Game Art Cheatsheet by [Tech Art Aid](https://techartaid.com)

> Most common glitches in game art and how to solve them

---

This is a raw source for the guide at **https://techartaid.com/cheatsheet/**

## Table of contents

- [Content Troubleshooting](#content-troubleshooting)
  * [Geometry](#geometry)
  * [Materials](#materials)
  * [Lighting](#lighting)
- [Credits](#credits)
- [Contributing](#contributing)
- [License](#license)

---

## Content Troubleshooting

### Geometry

| Name | Symptom | Cause | Solutions |
| - | - | - | - |
| Z-Fighting | Flickering polygons | Multiple polys have equal Z-depth in the same pixel | - Make sure no polygons overlap on the same plane<br>- Increase near clip distance in camera |
| Aliased lines | Thin geo like cables or fences jitters, disappears | Geometry is thinner than a single pixel | - Make LODs with thicker geo, different texture<br>- Blend to a solid texture at far distance<br>- Use a different anti-aliasing algorithm |
| Visibility culling | Mesh or particle system disappearing from certain positions | Frustum or occlusion culling hides objects by their bounding boxes | - Check if occluders (geometry) are correct<br>- Increase bounding box size |

### Materials

| Name | Symptom | Cause | Solutions |
| - | - | - | - |
| Bilinear mipmap filtering | Blurry textures at oblique angles | Bilinear filtering in perspective projection underestimates required mipmap level of a texture | - Use anisotropic texture filtering, if performance tradeoff is acceptable |
| Negative or NaN color | Black or deeply saturated squares on screen | Pixel’s color value is negative or not a number. This corrupts some algorithms (bloom) | - Check for division-by-zero in shaders<br>- Clamp values to [0,1] in shaders |
| Alpha bleeding | White edges in textures with transparency | https://ciechanow.ski/alpha-compositing/ | - Use a “solidify” filter in a texture<br>- Respect premultiplied alpha in shaders |
| Banding in textures | Stepped gradients in textures | Lossy compression can’t represent subtle gradient | - Use more contrast in your source textures (e.g. stronger normal map), then reduce it in shader |
| Banding in display | Stepped gradients on screen | Transition between certain colors can’t be represented, due to display’s color precision | - Add some film grain<br>- Use dithering<br>- Check if your post-process stack has HDR until very end |
| Aliasing | Moire patterns, bloom flickering | Resolution is not dense enough to represent (aka sample) the content | - Use smoother patterns in textures<br>- Add soft radius to light sources<br>- Blend to smoother texture by distance<br>- Use different anti-aliasing algorithm |
| Hard normals on foliage | Flat shading on foliage models, harshly disconnected from terrain | Foliage mesh, made of big flat planes, is interpreted too literally by lighting | - Use manually edited, up-pointing vertex normals<br>- Read normal from terrain |

### Lighting

| Name | Symptom | Cause | Solutions |
| - | - | - | - |
| Lightmap bleeding | Dark or wrong mesh edges | Bilinear filtering picks up information from neighboring texels | - Increase margins between UV islands |
| Overlapping polygons | Black spots in lightmaps | Multiple triangles are baked to the same texel | - Make sure no UV islands overlap<br>- Remove any overlapping geo in mesh & level<br>- Check for duplicate objects in level |
| Missing reflection probes | Black reflections on metals | Lack of reflection sources | - Make sure at least 1 reflection probe covers every area in the scene |
| Shadow acne | Stepped shadows at certain angles | Shadow map’s resolution is not enough to represent the continuous surface | - Play with shadow bias parameter in light’s settings<br>- Increase shadow map resolution |
| Dynamic lights limit | Flickering lights | Renderer reached a limit of light sources affecting the same pixel | - Reduce # of lights overlapping in that place (change radius, move, delete)<br>- Use static (baked) lights instead |

## Credits

[Oskar Świerad](http://oskarswierad.com) started and maintains the project.

Huge thanks to contibutors of examples from the [Unity](https://www.reddit.com/r/Unity3D/comments/mlp0od/working_on_a_cheatsheet_for_content_issues_what/), [Unreal](https://www.reddit.com/r/unrealengine/comments/mlp19p/working_on_a_cheatsheet_for_game_art_issues_what/) Reddit threads and [Twitter](https://twitter.com/TechArtAid/status/1379574278975332353).

## Contributing

If you want something added or fixed, just add a new [issue](https://github.com/OskarSwierad/game-art-cheatsheet/issues).
I welcome [pull requests](https://github.com/OskarSwierad/game-art-cheatsheet/pulls) too! (though quality is expected)

## License

Feel free to link, copy, print and modify the project, under the terms described in the [license document](https://github.com/OskarSwierad/game-art-cheatsheet/blob/main/LICENSE).
