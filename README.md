# **Q*bert Repair Lab**

NAME: PREMA P KOTUR

SRN: PES1UG24CS343

SECTION: F

This project is a single-file Q*bert-lite clone using Pygame. It introduces students to isometric projection, diagonal hop validation, and enemy chase behavior using a small, readable object-oriented codebase.

What's Provided
A working Q*bert-lite game with:

A pyramid of cubes the player hops across diagonally, painting each cube toward its target color on landing
Coily, an enemy that chases the player across the pyramid, and red balls that roll downward
Falling off the edge of the pyramid (or bumping an enemy while grounded) costs a life
Levels, lives, and scoring, with a win once every cube reaches its target color
It has one deliberate bug and three optional features left as empty functions. You are expected to analyze, interact with an AI assistant, and complete/fix the game to make it fully functional and more interesting.

LLM used: ChatGPT
Link: https://chatgpt.com/share/6abd4487-73ac-83ee-b1c1-fa88beac9a22

Getting Started
Setup
Make sure you have Python 3.10+ installed.
Install dependencies:
pip install pygame
Run the game:
python game.py
Controls: Left/Up/Down/Right to hop diagonally, R to reset, Space to continue after clearing a level.


How the Game Works

1. Pyramid Grid

The pyramid is represented internally using (row, col) coordinates.

There are 7 rows:

              □
            □ □
          □ □ □
        □ □ □ □
      □ □ □ □ □
    □ □ □ □ □ □
  □ □ □ □ □ □ □

The valid cells are generated using:

CELLS = {(r, c) for r in range(ROWS) for c in range(r + 1)}

Therefore:

Row 0 contains 1 cube

Row 1 contains 2 cubes

...

Row 6 contains 7 cubes

for a total of 28 cubes.

2. Cube Projection

Each grid coordinate is converted into a screen position by
cube_center(row, col).

The repaired implementation uses:

def cube_center(row, col):
    return pygame.Vector2(
        WIDTH / 2 + (col - row / 2) * CUBE_W,
        90 + row * CUBE_H
    )

The original bug

The original code used:

row // 2

instead of:

row / 2

// performs integer division, causing the horizontal row offset to
change in uneven steps.

For example:

0 // 2 = 0
1 // 2 = 0
2 // 2 = 1
3 // 2 = 1

The result was a visibly skewed pyramid.

Using true division gives:

0 / 2 = 0
1 / 2 = 0.5
2 / 2 = 1
3 / 2 = 1.5

This creates the correct half-cube horizontal shift between rows and
produces a symmetric triangular pyramid.

3. Cube Painting

Each cube has a painting stage.

The game uses:

TARGET = 2

so a cube progresses through:

Stage 0 → Stage 1 → Stage 2

When the player successfully lands on a cube, its stage increases by
exactly one.

Each successful painting step awards:

+25 points

A cube that has already reached the target stage cannot be painted
further.

When a cube reaches the target stage for the first time,
on_cube_completed(cell) is called.

4. Level-Specific Colors

The cube_palette(level) function provides a different three-color
palette for the available levels.

Each palette contains:

Unpainted cube color

Partially painted cube color

Fully painted cube color

For example, level 1 uses a blue palette, level 2 uses an orange
palette, and level 3 uses a green palette.

The drawing code uses:

colors = cube_palette(self.level) or DEFAULT_PALETTE

If no custom palette exists for a level, the game automatically uses the
default palette.

5. Player Movement and Hopping

The player is represented by the Hopper class.

A hop has a short animation controlled by:

HOP_TIME = 0.28
HOP_HEIGHT = 26

The player moves from the current cube to a target cell using
interpolation.

During the hop, the player rises and then lands on the destination cube.

A hop can also target a cell outside the valid pyramid. In that case,
the player falls instead of landing on a cube.

6. Falling Off the Pyramid

The valid pyramid cells are stored in CELLS.

When the player lands outside that set, the game starts a falling
animation.

The player continues falling until the fall distance reaches the
configured threshold, at which point a life is lost.

This creates the classic Q*bert-style risk of jumping from the edge of
the pyramid.

7. Enemies

Coily

Coily is represented by another Hopper.

When Coily is active, the game examines its neighboring cells and
chooses the available cell closest to the player's current position.

The chase logic is based on:

goal = cube_center(*self.player.cell)

self.coily.start(
    min(
        options,
        key=lambda n: cube_center(*n).distance_squared_to(goal)
    )
)

Therefore, Coily moves toward the player rather than randomly or away
from the player.

Red Balls

Red balls periodically appear at the top of the pyramid.

They move downward through the pyramid using:

(ball.cell[0] + 1, ball.cell[1] + random.randint(0, 1))

If they move beyond the valid pyramid, they fall away.

8. Collision Detection

When the player is not currently hopping or falling, the game checks the
distance between the player and active enemies.

If an enemy gets sufficiently close, the player loses a life.

This prevents collisions from being repeatedly triggered during a hop or
while the player is already falling.

9. Lives and Bonus Lives

The player starts with:

3 lives

A life is lost when:

The player falls from the pyramid.

The player collides with an enemy while grounded.

The implemented bonus-life threshold is:

def bonus_life_threshold():
    return 1000

This means the player receives one additional life whenever the score
reaches a new multiple of 1000:

1000 points → +1 life
2000 points → +1 life
3000 points → +1 life
...

The game already contains bookkeeping using self.bonus_awarded so that
the same threshold does not award multiple lives.

10. Winning and Losing

Winning

The level is completed when every cube reaches the target stage:

if all(stage >= TARGET for stage in self.stages.values()):

The player receives a level-clear state and can press Space to
continue to the next level.

Losing

When the player's lives reach zero, the game changes to the "lose"
state and displays:

GAME OVER - Press R

Pressing R resets the game.


Task 1: Fix the pyramid projection bug
Each cube's screen position is computed from its (row, col) grid coordinates, and the whole pyramid should come out as a symmetric triangle. In the current build the pyramid is visibly skewed from the very first frame — rows drift sideways in a way that breaks the triangular symmetry. Look at the arithmetic combining row and col in cube_center, and check whether the row offset should be using integer division or true division.

<img width="398" height="112" alt="image" src="https://github.com/user-attachments/assets/2d765c8d-19a7-448e-ac41-db8a18bdcea0" />

Task 2: Implement cube_palette(level)
Called once per frame in draw, as colors = cube_palette(self.level) or DEFAULT_PALETTE. It receives the current level number and should return a list of TARGET + 1 (currently 3) (r, g, b) colors — one per stage from unpainted to fully painted — or None to keep DEFAULT_PALETTE. Idea: return a different 3-color palette for each level.

<img width="490" height="406" alt="image" src="https://github.com/user-attachments/assets/23a4d782-4027-4733-8157-5827c4a81bc2" />


Task 3: Implement on_cube_completed(cell)
Called from paint() the instant a specific cube first reaches its target stage — not on every hop onto it, only the hop that finishes it. It receives the (row, col) cell that was just completed. Its return value is ignored. Idea: a brief flash on that cube, or a small bonus beyond the 25 points already awarded per paint step.

<img width="328" height="65" alt="image" src="https://github.com/user-attachments/assets/cfd5c57c-1f80-46c0-9249-b01a1a03223e" />


Task 4: Implement bonus_life_threshold()
Called every frame in update(). It takes no arguments and should return an integer score value, or None to disable bonus lives entirely. Whenever the score crosses a multiple of that value for the first time, one life is awarded automatically — the bookkeeping (self.bonus_awarded) is already implemented, so you only need to choose the threshold. Idea: return 1000.

<img width="250" height="56" alt="image" src="https://github.com/user-attachments/assets/5e8e49f9-61d8-4fb1-be66-58146d511234" />


Expected Behavior
The pyramid renders as a symmetric triangle of cubes
Landing on a cube advances its color exactly one stage, only once per hop
Hopping off the edge of the pyramid makes the player fall and costs a life
Coily chases toward the player's current position rather than away from it
Painting every cube to its target color wins the level; running out of lives ends the game
