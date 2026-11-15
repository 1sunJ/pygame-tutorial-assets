# 스네이크 에셋

**한국어** | [English](README.en.md)

`snake_assets` 폴더를 내 게임 코드(`main.py`)와 **같은 위치**에 넣고 사용하세요.

## 미리보기
![에셋 목록](../../assets/_preview/cells.png)

실제 게임 화면 (맵 크기별)

![실제 게임 화면](../../assets/_preview/demo_sizes.png)

## 크기 규칙
- 칸 하나: **16 × 16**
- 헤더 높이: **32** (맵은 y = 32부터 시작)
- 화면 크기: 가로 `열 × 16`, 세로 `32 + 행 × 16` (예: 16×12 → 256 × 224)
- 맵은 가로 **10칸 이상**, 세로 **10칸 이상**이면 크기와 상관없이 사용할 수 있습니다.
- 화면이 작으니 `pygame.SCALED`를 쓰세요. 창이 자동으로 크게 보이고, 좌표는 그대로 16px 기준입니다.
- 이미지 크기는 바꾸지 마세요(`transform.scale` X). 머리도 돌리지 말고, 방향별 그림을 골라 쓰세요.

## 파일
| 파일 | 크기 | 언제 쓰나 |
|---|---|---|
| `header.png` | 160×32 | 게임 중 맨 위 (점수, 최고 점수 표시창) |
| `cells/ground.png` | 16×16 | 바닥 (모든 칸에 먼저 깐다) |
| `cells/food.png` | 16×16 (투명 배경) | 먹이 (복숭아) |
| `cells/snake_body.png` | 16×16 (투명 배경) | 몸통. 둥근 구슬이라 꺾이는 칸에도 그대로 씀 |
| `cells/snake_head_up.png` / `_down` / `_left` / `_right` | 16×16 (투명 배경) | 머리. 움직이는 방향에 맞는 그림을 고른다 |
| `screens/gameover.png` | 160×192 (투명 배경) | 게임 오버 |
| `fonts/Silkscreen-Regular.ttf` | | 숫자를 그릴 때 쓰는 폰트 (크기 **16**) |

**그리는 순서**: 바닥 → 먹이 → 몸통 → 머리. 투명 배경 그림은 바닥 위에 올려야 보입니다.

## 예제 코드

```python
import pygame

COLS, ROWS = 16, 12
W, H = COLS * 16, 32 + ROWS * 16

pygame.init()
screen = pygame.display.set_mode((W, H), pygame.SCALED)   # SCALED: 창을 크게 보여줌

A = "snake_assets/"
font = pygame.font.Font(A + "fonts/Silkscreen-Regular.ttf", 16)
header = pygame.image.load(A + "header.png").convert()
ground = pygame.image.load(A + "cells/ground.png").convert()
food = pygame.image.load(A + "cells/food.png").convert_alpha()          # 투명 배경 → convert_alpha
body = pygame.image.load(A + "cells/snake_body.png").convert_alpha()
heads = {}                                                   # "up" → 이미지, "down" → 이미지 ...
for d in ["up", "down", "left", "right"]:
    heads[d] = pygame.image.load(A + f"cells/snake_head_{d}.png").convert_alpha()
gameover = pygame.image.load(A + "screens/gameover.png").convert_alpha()


def draw_text(text, rect, color):
    """rect 사각형 한가운데에 글자를 그린다."""
    surf = font.render(text, False, color)          # False = 픽셀이 번지지 않음
    glyph = surf.get_bounding_rect()                # 실제 글자가 있는 부분
    box = pygame.Rect(rect)
    screen.blit(surf, (box.centerx - glyph.centerx, box.centery - glyph.centery))


def draw_header(score, best):
    # 1) 헤더 이미지의 왼쪽 끝 1줄을 화면 너비만큼 늘려서 바탕으로 깐다
    strip = header.subsurface((0, 0, 1, 32))
    screen.blit(pygame.transform.scale(strip, (W, 32)), (0, 0))
    # 2) 헤더 이미지를 가운데에 놓는다
    hx = (W - header.get_width()) // 2
    screen.blit(header, (hx, 0))
    # 3) 숫자: 위치는 헤더 이미지 기준이라 hx 를 더한다
    draw_text(f"{score:03d}", (hx + 25, 6, 38, 18), (255, 180, 166))
    draw_text(f"{best:03d}", (hx + 94, 6, 38, 18), (255, 224, 138))


def draw_gameover(score):
    screen.fill((20, 28, 23))                                    # 바탕색
    box = gameover.get_rect(center=screen.get_rect().center)    # 화면 가운데
    screen.blit(gameover, box)
    draw_text(str(score), (box.x + 52, box.y + 116, 56, 20), (255, 180, 166))


def cell(c, r):
    """칸 (c, r) 의 화면 좌표"""
    return (c * 16, 32 + r * 16)


# 게임 중 화면 예시 — snake[0] 이 머리
snake = [(5, 4), (4, 4), (3, 4), (3, 5)]
direction = "right"
apple = (9, 6)

draw_header(3, 12)
for r in range(ROWS):                                       # 1) 바닥
    for c in range(COLS):
        screen.blit(ground, cell(c, r))
screen.blit(food, cell(*apple))                             # 2) 먹이
for part in snake[1:]:                                      # 3) 몸통
    screen.blit(body, cell(*part))
screen.blit(heads[direction], cell(*snake[0]))              # 4) 머리 (방향에 맞는 그림)
pygame.display.flip()
```

## 숫자 위치 정리
| 어디에 | 사각형 (x, y, 너비, 높이) | 글자 색 (R, G, B) |
|---|---|---|
| 헤더 — 점수 (3자리) | 헤더 이미지 기준 (25, 6, 38, 18) | (255, 180, 166) |
| 헤더 — 최고 점수 (3자리) | 헤더 이미지 기준 (94, 6, 38, 18) | (255, 224, 138) |
| 게임 오버 — 점수 | gameover.png 기준 (52, 116, 56, 20) | (255, 180, 166) |

## 폰트 라이선스
Silkscreen 폰트는 SIL Open Font License(무료, 재배포 가능)를 따릅니다. `fonts/Silkscreen-OFL.txt`를 폰트와 함께 두세요.
