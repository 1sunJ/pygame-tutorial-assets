# Minesweeper Assets

[한국어](README.md) | **English**

Put the `minesweeper_assets` folder **in the same place** as your game code (`main.py`).

## Preview
![Asset list](../../assets/_preview/cells.png)

In-game screens (by map size)

![In-game screens](../../assets/_preview/demo_sizes.png)

## Size rules
- One cell: **16 × 16**
- Header height: **32** (the board starts at y = 32)
- Screen size: width `cols × 16`, height `32 + rows × 16` (e.g. 9×9 → 144 × 176)
- Any map size works as long as it is at least **8 cells wide**.
- The screen is small, so use `pygame.SCALED`. The window is shown larger automatically, and coordinates stay in 16px units.
- Don't resize the images (no `transform.scale`). The pixels may get blurry.

## Files
| File | Size | When to use |
|---|---|---|
| `header.png` | 128×32 | Top of the screen during play (flags left, time) |
| `cells/cell_closed.png` | 16×16 | A cell that is not opened yet |
| `cells/cell_flag.png` | 16×16 | A cell with a flag |
| `cells/cell_empty.png` | 16×16 | An opened cell with 0 mines around it |
| `cells/cell_1.png` ~ `cell_9.png` | 16×16 | An opened cell with n mines around it |
| `cells/cell_mine.png` | 16×16 | An opened cell that was a mine |
| `screens/win.png` | 256×288 (transparent background) | When you win |
| `screens/lose.png` | 256×288 (transparent background) | When you lose |
| `fonts/Silkscreen-Regular.ttf` | | Font for drawing numbers (size **16**) |

## Example code

```python
import pygame

COLS, ROWS = 9, 9
W, H = COLS * 16, 32 + ROWS * 16

pygame.init()
screen = pygame.display.set_mode((W, H), pygame.SCALED)   # SCALED: shows the window larger

A = "minesweeper_assets/"
font = pygame.font.Font(A + "fonts/Silkscreen-Regular.ttf", 16)
header = pygame.image.load(A + "header.png").convert()
closed = pygame.image.load(A + "cells/cell_closed.png").convert()
win = pygame.image.load(A + "screens/win.png").convert_alpha()    # transparent background → convert_alpha
lose = pygame.image.load(A + "screens/lose.png").convert_alpha()


def draw_text(text, rect, color):
    """Draw text in the exact center of the rect."""
    surf = font.render(text, False, color)          # False = keep pixels sharp
    glyph = surf.get_bounding_rect()                # the part where the letters actually are
    box = pygame.Rect(rect)
    screen.blit(surf, (box.centerx - glyph.centerx, box.centery - glyph.centery))


def draw_header(flags_left, seconds):
    # 1) Stretch the leftmost 1px column of the header across the screen as a background
    strip = header.subsurface((0, 0, 1, 32))
    screen.blit(pygame.transform.scale(strip, (W, 32)), (0, 0))
    # 2) Place the header image in the center
    hx = (W - header.get_width()) // 2
    screen.blit(header, (hx, 0))
    # 3) Numbers: positions are relative to the header image, so add hx
    draw_text(f"{flags_left:02d}", (hx + 13, 7, 38, 18), (255, 216, 79))
    draw_text(f"{seconds:03d}", (hx + 76, 7, 38, 18), (79, 216, 255))


def draw_win(seconds):
    screen.fill((13, 24, 48))                                   # background color
    box = win.get_rect(center=screen.get_rect().center)          # center of the screen
    screen.blit(win, box)
    draw_text(str(seconds), (box.x + 92, box.y + 146, 72, 20), (94, 240, 160))


def draw_lose():
    screen.fill((42, 10, 22))                                   # background color
    screen.blit(lose, lose.get_rect(center=screen.get_rect().center))


# Example of the in-game screen
draw_header(10, 37)
for r in range(ROWS):
    for c in range(COLS):
        screen.blit(closed, (c * 16, 32 + r * 16))              # position of cell (c, r)
pygame.display.flip()
```

## Where to draw numbers
| Where | Rect (x, y, width, height) | Text color (R, G, B) |
|---|---|---|
| Header — flags left | relative to header image (13, 7, 38, 18) | (255, 216, 79) |
| Header — time | relative to header image (76, 7, 38, 18) | (79, 216, 255) |
| Win screen — time taken | relative to win.png (92, 146, 72, 20) | (94, 240, 160) |

## Font license
The Silkscreen font uses the SIL Open Font License (free, can be redistributed). Keep `fonts/Silkscreen-OFL.txt` together with the font.
