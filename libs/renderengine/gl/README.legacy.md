# Legacy GLES RenderEngine

This backend restores the OpenGL ES 2 renderer for legacy drivers such as the
Exynos 4412 Mali stack. It is opt-in; the default Skia backend selection is
unchanged.

Set `debug.renderengine.backend=gles` for rendering on the calling thread, or
`debug.renderengine.backend=threaded` for the threaded wrapper. SurfaceFlinger
handles these values before probing Vulkan support.

Sources: html6405/android_frameworks_native, branch `lineage-21.0-r08`, tip
`5255e5d255`. This includes the GLES restoration (`6db912d6f3`), String8 fixes
(`a88f414373`), and the Mali post-render cleanup workaround (`5255e5d255`).
The LineageOS 23 port uses the current RenderEngine draw/fence interface,
creation arguments, texture mapping, and threaded wrapper. Framebuffer caching
stays private to this backend. Shader priming returns a completed future.

Limitations inherited from the legacy renderer:

- HDR gainmap generation is unsupported and returns `INVALID_OPERATION`.
- Rounded corners use a uniform radius derived from the top-left corner.
- New Skia effects (including edge extension, display LUTs, and box shadows)
  are not implemented here.
- Per-frame post-render resource cleanup is skipped to avoid legacy Mali stalls;
  resource destruction still happens when the engine is destroyed.

A successful build does not establish driver compatibility. Verify on device
with SurfaceFlinger logs/dumps and a boot through to the launcher, then check
rotation, screenshots, and suspend/resume.
