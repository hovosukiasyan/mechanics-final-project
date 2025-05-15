# Particle Packing & Floor-Planning Simulation

**Live Demo:** https://hovosukiasyan.github.io/mechanics-final-project/

## Introduction

This project delivers a browser-based physics simulation to evaluate three geometric shapes—circles, squares, and rectangles—under two scenarios:

1. **Packing Mode**: Shapes mutually attract and repel to minimize the area of their enclosing bounding box.  
2. **Floor-Planning Mode**: Shapes are drawn toward a central 100×100 target region and ranked by the standard **Intersection-over-Union (IoU)** metric, which balances coverage against overshoot.

## Setup

No build tools or servers are required. The simulation runs directly in any modern browser that supports ES6 modules.

1. Place **index.html** and **style.css** in a single directory.  
2. Open **index.html** via the browser’s **File → Open** menu.

## Usage

1. Choose **Packing** or **Floor-Planning** from the mode selector.  
2. Click **Start All** to launch three simultaneous simulations (one per shape).  
3. After 10 seconds—or once the physics settle automatically—the system stops and displays a ranked table.  
4. Use **Stop** at any time to terminate early and reveal current standings.

## Repository Contents

* Two example result screenshots (one for Packing Mode and one for Floor-Planning Mode) are included to illustrate typical outputs.

## Code Structure

- **index.html**  
  Defines the page layout, controls, and three `<canvas>` elements.

- **style.css**  
  Contains all styling rules and inlined JavaScript logic (combined into a single file to avoid CORS issues with ES6 modules over `file://`).

## Core Components

### 1. PackingEngine Class

Encapsulates the simulation loop for one shape type:

- `generateParticles(n)`: Initializes N shapes with random size, position, and zero velocity.  
- `computeForces()`: Applies inverse-square attraction, overlap repulsion (`K_REPEL`), and—only in floor mode—a linear pull toward the target box (`GATHER_STRENGTH`).  
- `computeAccelerations()` & `updatePositions()`: Integrate motion with timestep `DT`, apply damping `DAMP`, handle wall bounces, and resolve inter-particle collisions.  
- `draw()`: Renders all shapes on 500×500 `<canvas>`, outlines the target region (in red) and final bounding box (in blue).  
- `start(mode)` / `stop()`: Manages the animation loop, dispatches an `engineDone` event upon completion.

### 2. Utility Functions

- `massOf(p)`: Computes area of a circle (`πr²`), square (`size²`), or rectangle (`width×height`).  
- `computeIntersectionArea(p, box)`: Estimates the overlap between a particle and the target box (16-point sampling for circles; rectangular clipping for polygons).

### 3. Ranking Logic (`rankEngines()`)

- **Packing Mode**: Sort shapes by ascending **bounding-box area** computed via `computeBBox()`.  
- **Floor-Planning Mode**: Compute **IoU** = `interArea` ÷ (`shapeArea` + `boxArea` – `interArea`), sort descending.

## Configuration Parameters

All simulation constants are declared at the top of **style.css**:

- `G_PACK`, `G_FLOOR`: Attraction strengths for packing and floor modes.  
- `K_REPEL`: Overlap repulsion coefficient.  
- `GATHER_STRENGTH`: Target-box pull strength in floor mode.  
- `DT`, `DAMP`: Integration timestep and velocity damping.  
- `N`: Number of shapes per run.  
- `T_MAX`, `STOP_EKIN`: Maximum iterations and kinetic-energy threshold for auto-stop.  
- `TARGET_BOX`: Center coordinates (`cx`, `cy`) and dimensions (`w`, `h`) of the floor-planning area.

## Results Interpretation

- In **Packing Mode**, a smaller bounding-box area indicates better self-packing efficiency.  
- In **Floor-Planning Mode**, a higher IoU reflects more complete coverage of the target with minimal spill.

For rigorous analysis, perform multiple trials and report the mean and standard deviation of each metric by shape type.
