# 지뢰찾기 에셋

`minesweeper_assets` 폴더를 내 게임 코드(`main.py`)와 **같은 위치**에 넣고 사용하세요.

## 크기 규칙
- 칸 하나: **16 × 16**
- 헤더 높이: **32** (보드는 y = 32부터 시작)
- 화면 크기: 가로 `열 × 16`, 세로 `32 + 행 × 16` (예: 9×9 → 144 × 176)
- 맵은 가로 **8칸 이상**이면 크기와 상관없이 사용할 수 있습니다.
- 화면이 작으니 `pygame.SCALED`를 쓰세요. 창이 자동으로 크게 보이고, 좌표는 그대로 16px 기준입니다.
- 이미지 크기는 바꾸지 마세요(`transform.scale` X). 픽셀이 깨질 수 있습니다.

## 파일
| 파일 | 크기 | 언제 쓰나 |
|---|---|---|
| `header.png` | 128×32 | 게임 중 맨 위 (깃발 개수, 시간 표시창) |
| `cells/cell_closed.png` | 16×16 | 아직 안 연 칸 |
| `cells/cell_flag.png` | 16×16 | 깃발을 꽂은 칸 |
| `cells/cell_empty.png` | 16×16 | 연 칸, 주변 지뢰 0개 |
| `cells/cell_1.png` ~ `cell_9.png` | 16×16 | 연 칸, 주변 지뢰 n개 |
| `cells/cell_mine.png` | 16×16 | 열었는데 지뢰인 칸 |
| `screens/win.png` | 256×288 (투명 배경) | 이겼을 때 |
| `screens/lose.png` | 256×288 (투명 배경) | 졌을 때 |
| `fonts/Silkscreen-Regular.ttf` | | 숫자를 그릴 때 쓰는 폰트 (크기 **16**) |

## 예제 코드

```python
import pygame

COLS, ROWS = 9, 9
W, H = COLS * 16, 32 + ROWS * 16

pygame.init()
screen = pygame.display.set_mode((W, H), pygame.SCALED)   # SCALED: 창을 크게 보여줌

A = "minesweeper_assets/"
font = pygame.font.Font(A + "fonts/Silkscreen-Regular.ttf", 16)
header = pygame.image.load(A + "header.png").convert()
closed = pygame.image.load(A + "cells/cell_closed.png").convert()
win = pygame.image.load(A + "screens/win.png").convert_alpha()    # 투명 배경 → convert_alpha
lose = pygame.image.load(A + "screens/lose.png").convert_alpha()


def draw_text(text, rect, color):
    """rect 사각형 한가운데에 글자를 그린다."""
    surf = font.render(text, False, color)          # False = 픽셀이 번지지 않음
    glyph = surf.get_bounding_rect()                # 실제 글자가 있는 부분
    box = pygame.Rect(rect)
    screen.blit(surf, (box.centerx - glyph.centerx, box.centery - glyph.centery))


def draw_header(flags_left, seconds):
    # 1) 헤더 이미지의 왼쪽 끝 1줄을 화면 너비만큼 늘려서 바탕으로 깐다
    strip = header.subsurface((0, 0, 1, 32))
    screen.blit(pygame.transform.scale(strip, (W, 32)), (0, 0))
    # 2) 헤더 이미지를 가운데에 놓는다
    hx = (W - header.get_width()) // 2
    screen.blit(header, (hx, 0))
    # 3) 숫자: 위치는 헤더 이미지 기준이라 hx 를 더한다
    draw_text(f"{flags_left:02d}", (hx + 13, 7, 38, 18), (255, 216, 79))
    draw_text(f"{seconds:03d}", (hx + 76, 7, 38, 18), (79, 216, 255))


def draw_win(seconds):
    screen.fill((13, 24, 48))                                   # 바탕색
    box = win.get_rect(center=screen.get_rect().center)          # 화면 가운데
    screen.blit(win, box)
    draw_text(str(seconds), (box.x + 92, box.y + 146, 72, 20), (94, 240, 160))


def draw_lose():
    screen.fill((42, 10, 22))                                   # 바탕색
    screen.blit(lose, lose.get_rect(center=screen.get_rect().center))


# 게임 중 화면 예시
draw_header(10, 37)
for r in range(ROWS):
    for c in range(COLS):
        screen.blit(closed, (c * 16, 32 + r * 16))              # 칸 (c, r) 위치
pygame.display.flip()
```

## 숫자 위치 정리
| 어디에 | 사각형 (x, y, 너비, 높이) | 글자 색 (R, G, B) |
|---|---|---|
| 헤더 — 남은 깃발 개수 | 헤더 이미지 기준 (13, 7, 38, 18) | (255, 216, 79) |
| 헤더 — 시간 | 헤더 이미지 기준 (76, 7, 38, 18) | (79, 216, 255) |
| 승리 화면 — 걸린 시간 | win.png 기준 (92, 146, 72, 20) | (94, 240, 160) |

## 폰트 라이선스
Silkscreen 폰트는 SIL Open Font License(무료, 재배포 가능)를 따릅니다. `fonts/Silkscreen-OFL.txt`를 폰트와 함께 두세요.
