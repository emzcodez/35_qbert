Q*bert Repair Lab
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

LLM used:

Getting Started
Setup
Make sure you have Python 3.10+ installed.
Install dependencies:
pip install pygame
Run the game:
python game.py
Controls: Left/Up/Down/Right to hop diagonally, R to reset, Space to continue after clearing a level.

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
