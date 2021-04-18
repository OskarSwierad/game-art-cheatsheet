[**Most common glitches in game art and how to solve them**](https://techartaid.com/cheatsheet/)

Troubleshooting tips for mesh issues, lighting bugs, particles, transparency, physics. The solutions can be applied in Unity, Unreal Engine, Godot and most other game engines.

Find more game dev tutorials at [TechArtAid.com](https://techartaid.com)

## Content Troubleshooting

### Geometry

| Name | Symptom | Cause | Solutions |
| - | - | - | - |
| ![](https://techartaid.com/img/cheatsheet/geo-aliased-lines.gif)<br>**Aliased lines** | Thin geo like cables or fences jitters, disappears | Geometry is thinner than a single pixel | - Make LODs with thicker geo, different texture<br>- Blend to a solid texture at far distance<br>- Use a different anti-aliasing algorithm |
| ![](https://techartaid.com/img/cheatsheet/geo-vis-culling.gif)<br>**Visibility culling** | Mesh or particle system disappearing from certain positions | Frustum or occlusion culling hides objects by their bounding boxes | - Check if occluders (geometry) are correct<br>- Increase bounding box size |
| ![](https://techartaid.com/img/cheatsheet/geo-zfighting.gif)<br>**Z-Fighting** | Flickering polygons | Multiple polys have equal Z-depth in the same pixel | - Make sure no polygons overlap on the same plane<br>- Increase near clip distance in camera |
| ![](https://techartaid.com/img/cheatsheet/geo-flipped.png)<br>**Flipped normals** | Mesh is inverted or partially invisible | Back faces of triangles are hidden by default (backface culling) | - Flip (reverse)  vertex normals to face outside<br>- If both sides needed, enabled two-sided rendering in material |
| ![](https://techartaid.com/img/cheatsheet/geo-split.png)<br>**Split edges** | Holes appear in animated mesh | Vertices have two modes: shared or split | - Merge vertices in your 3d software<br>- Lock vertex normals instead of physically splitting edges<br>- Use auto smooth groups |
| ![](https://techartaid.com/img/cheatsheet/geo-scale.png)<br>**Import scale** | Model is huge or tiny after importing | Different editors use 1 cm or 1 m as base unit | -Make sure to follow correct physical dimensions when modelling<br>- Set import or export scale to 0.01 or 100x<br> |
| ![](https://techartaid.com/img/cheatsheet/geo-temporal.gif)<br>**Temporal anti-aliasing** | Ghosting. Meshes leave blurry trail when moving | Temporal smoothing algorithms combine multiple frames over time for high quality | - Make sure game keeps good, stable framerate, high resolution<br>- If using vertex displacement in shader, add motion vectors support<br> - For stability but lower quality, switch to FXAA anti-aliasing |

### Materials

| Name | Symptom | Cause | Solutions |
| - | - | - | - |
| ![](https://techartaid.com/img/cheatsheet/mat-filtering.png)<br>**Bilinear mipmap filtering** | Blurry textures at oblique angles | Bilinear filtering in perspective projection underestimates required mipmap level of a texture | - Use anisotropic texture filtering, if performance tradeoff is acceptable |
| ![](https://techartaid.com/img/cheatsheet/mat-nan.png)<br>**Negative or NaN color** | Black or deeply saturated squares on screen | Pixel’s color value is negative or not a number. This corrupts some algorithms (bloom) | - Check for division-by-zero in shaders<br>- Clamp values to [0,1] in shaders |
| **Edge bleeding** | Colors from another island of UV space bleeding into the borders of given one | Bilinear texture filtering picks up information from neighboring texels, as it's unaware of UV seams or image atlas items | - Increase margins between UV islands<br>- Add 1 pixel of space between items in tileset/atlas<br>- In Substance Painter, use `Dilation` mode when exporting |
| ![](https://techartaid.com/img/cheatsheet/mat-white-edge.png)<br>**Alpha bleeding** | White edges in textures with transparency | [Alpha compositing](https://ciechanow.ski/alpha-compositing/) | - In Substance Painter, use `Dilation` mode when exporting<br> -In Photoshop, fill the background by selecting alpha mask, inverting selection and use Fill > Content Aware<br>- Respect premultiplied alpha in shaders |
| <br>**Banding in textures** | Stepped gradients in textures | Lossy compression can’t represent subtle gradient | - Use more contrast in your source textures (e.g. stronger normal map), then reduce it in shader |
| ![](https://techartaid.com/img/cheatsheet/mat-banding.png)<br>**Banding in display** | Stepped gradients on screen | Transition between certain colors can’t be represented, due to display’s color precision | - Add some film grain<br>- Use dithering<br>- Check if your post-process stack has HDR until very end |
| ![](https://techartaid.com/img/cheatsheet/mat-aliasing.gif)<br>**Aliasing** | Moire patterns, bloom flickering | Resolution is not dense enough to represent (aka sample) the content | - Use smoother patterns in textures<br>- Add soft radius to light sources<br>- Blend to smoother texture by distance<br>- Use different anti-aliasing algorithm |
| ![](https://techartaid.com/img/cheatsheet/mat-srgb.png)<br>**sRGB vs linear** | Textures are too dark or washed out after import, or roughness is wrong | Textures come in (at least) two formats: linear color or sRGB. Linear is used for data, while sRGB for human-readable color | - In texturing programs, export albedo/diffuse as sRGB<br>- In game engine, enable  `sRGB` in albedo/diffuse textures<br>- Disable sRGB (use linear) for data (roughness, masks) |
| ![](https://techartaid.com/img/cheatsheet/mat-normal-flip.png)<br>**Normal Y-axis sign** | Normal map looks inverted | Some engines expect Y+ axis to mean "down", while others consider it "up" | - Invert green channel of normal map<br>-In Unreal, tick `Flip Green Channel` in texture |
| ![](https://techartaid.com/img/cheatsheet/mat-foliage-normals.png)<br>**Hard normals on foliage** | Flat shading on foliage models, harshly disconnected from terrain | Foliage mesh, made of big flat planes, is interpreted too literally by lighting | - Use manually edited, up-pointing vertex normals<br>- Read normal from terrain |
| **Half precision** | Pixelated procedural materials on mobile | Mobile devices optimize some values by storing them in 16 bits instead of 32 | - Make your effects less dependent on precision (smooth flow maps, bigger features on texture)<br>- Use modulo (%, fmod) on final time and UVs<br>- Force full precision on variable  |

### Transparency 
| Name | Symptom | Cause | Solutions |
| - | - | - | - |
| ![](https://techartaid.com/img/cheatsheet/trans-sorting.png)<br>**Bad sorting of polygons** | Triangles of meshes or particles appear in wrong order | Game engines employ per-object sorting instead of per-triangle, for speed | - Split big objects into smaller ones<br>- Move pivot closer to center of mesh<br>- Use dithered opacity or cutout alpha for semi-solid objects<br>- Hack: change draw priority (render queue) of shader |
| ![](https://techartaid.com/img/cheatsheet/trans-dof.png)<br>**Depth of field & transparents** | Transparent objects are not affected by depth of field | DoF reads the depth of the scene to perform blur. Translucent materials don't write single depth | - Use cutout alpha (masked opacity) instead<br>- In Unreal, enable Separate Translucency in material- Sometimes you can't avoid it<br> |

### Lighting

| Name | Symptom | Cause | Solutions |
| - | - | - | - |
| **Lightmap bleeding** | Dark or wrong mesh edges | Bilinear filtering picks up information from neighboring texels | - Increase margins between UV islands |
| **Overlapping polygons** | Black spots in lightmaps | Multiple triangles are baked to the same texel | - Make sure no UV islands overlap<br>- Remove any overlapping geo in mesh & level<br>- Check for duplicate objects in level |
| **Missing reflection probes** | Black reflections on metals | Lack of reflection sources | - Make sure at least 1 reflection probe covers every area in the scene |
| ![](https://techartaid.com/img/cheatsheet/light-mobility.png)<br>**Movable SkyLight** (UE4) | Black reflections on metals | SkyLight or all reflection probes have `Mobility` set to `Movable` | - Change their `Mobility` to `Static` |
| **Shadow acne** | Stepped shadows at certain angles | Shadow map’s resolution is not enough to represent the continuous surface | - Play with shadow bias parameter in light’s settings<br>- Increase shadow map resolution |
| **Dynamic lights limit** | Flickering lights | Renderer reached a limit of light sources affecting the same pixel | - Reduce # of lights overlapping in that place (change radius, move, delete)<br>- Use static (baked) lights instead |

### Animation

| Name | Symptom | Cause | Solutions |
| - | - | - | - |
| **Root motion** | Character "swims" or drifts far from pivot over time | Root bone transform in animation is updated to match character movement in game | - Fix the loop, making sure it ends where it started<br>- Disable root motion |

### Physics

| Name | Symptom | Cause | Solutions |
| - | - | - | - |
| **Tick rate** | Fast objects fly through, skipping collision | Physics frame rate was not sufficient for the system to detect collision | - Use thicker collider<br>- Enable continuous collision detection (CCD) |

## Contributing

If you want something added or fixed, just add a new [issue](https://github.com/OskarSwierad/game-art-cheatsheet/issues).
<br>
I welcome [pull requests](https://github.com/OskarSwierad/game-art-cheatsheet/pulls) too! (though quality is expected)

## Credits

[Oskar Świerad](http://oskarswierad.com) of [Tech Art Aid](https://techartaid.com) is the original author and current maintainer.
<br>
Huge thanks to contibutors of examples from the [Unity](https://www.reddit.com/r/Unity3D/comments/mlp0od/working_on_a_cheatsheet_for_content_issues_what/), [Unreal](https://www.reddit.com/r/unrealengine/comments/mlp19p/working_on_a_cheatsheet_for_game_art_issues_what/) Reddit threads and [Twitter](https://twitter.com/TechArtAid/status/1379574278975332353).

## License

Feel free to share, copy, print and modify the document, under the terms of [Apache License 2.0](https://github.com/OskarSwierad/game-art-cheatsheet/blob/main/LICENSE).
