# cub3d (Raycasting Engine)

<img src="https://github.com/wenjuin95/cub3d/blob/main/cub3d.gif" />

This project is a small 3D engine built with **C** and **MiniLibX**, inspired by Wolfenstein-style rendering.

It reads a `.cub` map file, casts one ray per screen column, and draws textured walls, floor/ceiling colors, and a minimap.

## Raycasting (How it works)

The renderer follows the classic grid-based raycasting flow:

1. **Cast a ray for each screen column**
   - In `src/raycast_and_dda.c`, `initialize_ray()` computes the ray direction from:
     - player direction (`dir_x`, `dir_y`)
     - camera plane (`plane_x`, `plane_y`)
2. **Find the first wall hit with DDA**
   - `get_step()` sets step direction (`step_x`, `step_y`) and initial side distances.
   - `perform_dda()` moves cell-by-cell across the map grid until it hits `'1'` (a wall).
3. **Compute perpendicular wall distance**
   - `raycasting()` stores `wall_dis` from the DDA result.
   - This avoids fish-eye distortion and is used for projection.
4. **Project wall height to the screen**
   - In `src/draw.c`, `define_line_height()` calculates the vertical line to draw:
     - closer wall => taller slice
     - farther wall => shorter slice
5. **Apply textures**
   - `get_texture_index()` picks N/S/E/W texture by hit side and ray direction.
   - `define_texture()` computes texture X/Y sampling.
   - `update_texture_pixel()` writes final textured pixels into the frame buffer.

## Project structure

- `src/` → render loop, movement, raycasting, drawing, minimap
- `parse/` → `.cub` file parsing and validation
- `include/` → headers and core structs
- `maps/` → example maps
- `texture/` → texture assets
- `libft/` → utility library
- `minilibx-linux/` → MiniLibX source for Linux

## Compile guide

### 1) Install dependencies (Linux)

MiniLibX (Linux) requires X11-related dev packages.

On Debian/Ubuntu:

```bash
sudo apt update
sudo apt install -y build-essential libx11-dev libxext-dev zlib1g-dev
```

### 2) Build

From repository root:

```bash
make
```

### 3) Run

Run with any valid map:

```bash
./cub3d maps/map1.cub
```

## Controls

- `W / A / S / D` → move
- `← / →` → rotate
- `M` → toggle minimap
- `ESC` → exit

## Useful Make targets

- `make` → build
- `make fsan` → build with AddressSanitizer
- `make clean` / `make fclean` / `make re` → cleanup/rebuild
- `make leak` → run valgrind with `maps/map1.cub`
- `make norm` → run norminette checks
