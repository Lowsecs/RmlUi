# RmlUi Meson Integration Guide

This document outlines the steps, details, and potential gotchas for integrating RmlUi into a custom game engine using the provided Meson build files.

## Overview

This project has been set up with a non-invasive, single-file Meson build configuration to make it easy to maintain and merge updates from the upstream RmlUi repository. The entire Meson build system is contained in two files in the root directory:

-   `meson.build`: The main build file for the RmlUi project.
-   `meson_options.txt`: A file defining the build options you can configure.

## How to Use as a Subproject

To use RmlUi as a Meson subproject in your engine:

1.  **Add RmlUi as a subproject:** Place the RmlUi project directory inside your engine's `subprojects` directory.
2.  **In your engine's `meson.build` file, get the dependency:**
    ```meson
    rmlui_dep = dependency('RmlUi', fallback: ['RmlUi', 'rmlui_dep'])
    ```
3.  **Link your executable or library with RmlUi:**
    ```meson
    my_engine_executable = executable(
      'my_engine',
      'main.cpp',
      dependencies: [rmlui_dep]
    )
    ```

You can configure the RmlUi options from your parent project by passing them to the `meson` command, for example:

```sh
# From your engine's build directory
meson configure -Dsvg_plugin=true
```

## Build Configurations & Gotchas

The Meson build has been configured and tested to work with your custom `mac-release.ini` and `mac-debug.ini` files. This involves a few important workarounds for your specific build requirements.

### Gotcha: RTTI and Exception Handling

Your native configuration files disable C++ exceptions (`-fno-exceptions`) and RTTI (`-fno-rtti`). RmlUi can support this, but it requires two specific workarounds that have been implemented in the `meson.build` file:

1.  **Custom RTTI:** To handle the lack of native RTTI, the `rmlui_custom_rtti_impl` option in `meson_options.txt` is enabled by default. This compiles in RmlUi's custom RTTI implementation and defines the `RMLUI_CUSTOM_RTTI` preprocessor macro, which disables the use of `typeid`.
2.  **No Exceptions:** To handle the lack of exceptions, the `meson.build` file detects when `cpp_eh` is set to `none` and defines the `ITLIB_FLAT_MAP_NO_THROW` macro. This prevents a third-party container library from using `throw` statements, substituting them with `assert`s instead.

**Takeaway:** When you integrate RmlUi, ensure that your build system correctly passes the `-fno-rtti` and `-fno-exceptions` flags and that the corresponding options in `meson_options.txt` are enabled.

## Font and Text Rendering

-   **Font Engine:** The `font_engine` option in `meson_options.txt` allows you to choose between the `freetype` font engine (the default) or `none`.
-   **HarfBuzz Text Shaping:** The `harfbuzz_text_shaping` option is available but is set to `false` by default. Note that RmlUi does not have a core HarfBuzz integration. To use HarfBuzz, you would need to adapt the sample implementation from `Samples/basic/harfbuzz` and use it as a custom font engine.

## Binary Size and Link Time

-   **Binary Size:** A realistic binary size overhead for statically linking RmlUi into a **release build** of your engine is in the range of **2-5 MB** (before web compression). This is achieved through dead-code stripping and compiler optimizations.
-   **Link Time:**
    -   **Debug Builds (no LTO):** You can expect a fast iteration loop. The link time overhead should be in the range of **5-10 seconds**.
    -   **Release Builds (with LTO):** Link-Time Optimization is very expensive. Expect the final linking step for your shipping build to take **30-90+ seconds** longer when RmlUi is included. This is a standard trade-off for the performance and size benefits of LTO.

This setup should provide a solid and maintainable foundation for you to fork the repository and begin your integration.
