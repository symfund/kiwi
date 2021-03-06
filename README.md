KiWi - The Killer Widgets library
=================================
![Linux](https://github.com/mobius3/kiwi/workflows/Linux/badge.svg)

*Killer Widgets* (KiWi) is a widget-based GUI library for game developers to use in their 2D games, written in C99. Its widgets are constructed by using a tileset, allowing you to easly customize the looks of your GUI by simply changing its tiles.

KiWi works around SDL2 libraries but has space for custom rendering backends.

Here are some screenshots (click on them to see code):

[![Label screenshot](https://raw.githubusercontent.com/mobius3/KiWi/master/examples/label/label-screenshot.png "Label screenshot")](https://github.com/mobius3/KiWi/blob/master/examples/label/label.c)
[![A Family of Frames](https://raw.githubusercontent.com/mobius3/KiWi/master/examples/frame-family/frame-family-screenshot.png "A Family of Frames")](https://github.com/mobius3/KiWi/blob/master/examples/frame-family/frame-family.c)

[![Two Editboxes and a button](https://raw.githubusercontent.com/mobius3/KiWi/master/examples/editbox/editbox-screenshot.png "Two Editboxes and a button")](https://github.com/mobius3/KiWi/blob/master/examples/editbox/editbox.c)
[![Styleswitcher](https://raw.githubusercontent.com/mobius3/KiWi/master/examples/styleswitcher/styleswitcher-screenshot.gif "Styleswitcher")](https://github.com/mobius3/KiWi/blob/master/examples/styleswitcher/styleswitcher.c)

[![A scrollbox full of dragable buttons](https://raw.githubusercontent.com/mobius3/KiWi/master/examples/scrollbox/scrollbox-screenshot.gif "A scrollbox full of dragable buttons")](https://github.com/mobius3/KiWi/blob/master/examples/scrollbox/scrollbox.c)

**Road to 1.0.0**

KiWi is not finished yet, it's API still needs to be stabilized and might change
in the future. We're heading towards a 1.0.0 release. Checkout the [roadmap].

## Bindings
* [Vasilij Schneidermann](https://github.com/wasamasa) [created](http://emacsninja.com/posts/kiwi.html) a [CHICKEN Scheme](https://www.call-cc.org/) [binding](https://github.com/wasamasa/kiwi) for KiWi. There's also a wiki [here](http://wiki.call-cc.org/eggref/4/kiwi).
* [dKiWi](https://github.com/aferust/dkiwi) - D binding.

## Basic usage

Here is a very basic code that draws a label on screen (taken from the 
[label] example):

```cpp
/**
 * This is the `label` example. It displays a label with an icon inside a frame.
 * The icon is merely a region inside the GUI tileset.
 */
#include "KW_gui.h"
#include "KW_frame.h"
#include "KW_label.h"
#include "KW_renderdriver_sdl2.h"

int main(int argc, char ** argv) {
  /* We are going to use the SDL2 Render Driver.
   * Initialize SDL, creating a SDL window and renderer */
  SDL_Init(SDL_INIT_EVERYTHING);
  SDL_Renderer * renderer;
  SDL_Window * window;
  SDL_CreateWindowAndRenderer(320, 240, 0, &window, &renderer);
  SDL_SetRenderDrawColor(renderer, 100, 100, 100, 1); /* pretty background */

  /* Now we are going to use the SDL2 Render Driver. Users can implement their own
   * render driver, as long as it complies to the KW_RenderDriver structure */
  KW_RenderDriver * driver = KW_CreateSDL2RenderDriver(renderer, window);

  /* Loads the "tileset.png" file as a surface */
  KW_Surface * set = KW_LoadSurface(driver, "tileset.png");

  /* At this point we can create the GUI. You can have multiple GUI instances
   * in the same window (or even different windows, its up to the Render Driver) */
  KW_GUI * gui = KW_Init(driver, set);

  /* Loads a font and sets it. KiWi does not manage foreign memory (it does not own
   * your pointers), so you must keep them to release later. */
  KW_Font * font = KW_LoadFont(driver, "Fontin-Regular.ttf", 12);
  KW_SetFont(gui, font);

  /* Define a geometry and create a frame */
  KW_Rect geometry = { x: 0, y: 0, w: 320, h: 240 };
  KW_Widget * frame = KW_CreateFrame(gui, NULL, &geometry);

  /* Now create a label that has the frame as the parent, reutilizing
   * the same geometry. Children widgets offset they x and y coordinates by their parent. */
  KW_Widget * label = KW_CreateLabel(gui, frame, "Label with an icon :)", &geometry);

  /* Sets the rect in the tileset to extract the icon from, and sets it in the label */
  KW_Rect iconrect = { x: 0, y: 48, w : 24, h : 24 };
  KW_SetLabelIcon(label, &iconrect);

  /* Call KW_ProcessEvents(gui) and KW_Paint(gui) in your game loop. */
  while (!SDL_QuitRequested()) {
    SDL_RenderClear(renderer);
    KW_ProcessEvents(gui);
    KW_Paint(gui);
    SDL_Delay(1);
    SDL_RenderPresent(renderer);
  }

  /* All widgets are free'd after a KW_Quit */
  KW_Quit(gui);

  /* Releases things */
  KW_ReleaseSurface(driver, set);
  KW_ReleaseFont(driver, font);
  KW_ReleaseRenderDriver(driver);
  SDL_Quit();

  return 0;
}
```

[label]:https://github.com/mobius3/KiWi/blob/master/examples/label/label.c
[KW_CreateWidget]: https://github.com/mobius3/KiWi/blob/master/src/KW_widget.h#L106
[SDL2]: http://libsdl.org
[SDL2_ttf]: https://www.libsdl.org/projects/SDL_ttf/
[SDL2_image]: https://www.libsdl.org/projects/SDL_image/
[roadmap]: https://github.com/mobius3/KiWi/blob/master/ROADMAP.md

[CHICKENBINDING]: https://github.com/wasamasa/kiwi
