# Car Animation

A pure CSS parallax driving scene — a car moves through a continuously scrolling landscape with a sky, trees, and road, all built using only HTML and CSS. No JavaScript, no canvas, no animation library. The illusion of the car driving forward is created entirely through CSS `@keyframes` animations running at three different speeds on three independently scrolling background layers.

---

## What This Project Does

The page shows a car fixed on screen while three background layers — sky, trees/grass, and road — scroll horizontally from right to left at different speeds, creating a parallax depth effect. The two car wheels spin in sync with the road movement. Everything that moves is driven by a single `@keyframes` rule (`ani-road`) applied at different durations, and a second rule (`ani-wheal`) for the spinning wheels.

**Note:** `car.html` references a `car.js` file, but no JavaScript file exists in the repository. The animation runs entirely through CSS — the `<script src="car.js">` tag has no effect.

---

## Scene Structure — `car.html`

The entire scene is built from 5 `div` elements inside a `.container`:

```html
<div class="container">
  <div class="sky"></div>
  <div class="grass"></div>
  <div class="road"></div>
  <div class="car">
    <div class="tier-1"></div>   <!-- front wheel -->
    <div class="tier-2"></div>   <!-- rear wheel -->
  </div>
</div>
```

Each background layer is a separate `div` that uses a tiled image as its CSS background. The `.car` div holds two `.tier` divs — one for each wheel — also as background images.

---

## How the CSS Works — `car.css`

### The Infinite Scroll Trick — `@keyframes ani-road`

```css
@keyframes ani-road {
  100% { transform: translateX(-500vw); }
}
```

This single keyframe rule drives all three background layers. It translates each element 500 viewport widths to the left over the course of its animation duration. Because all three background `div`s are set to `width: 10000%` (10,000% of the viewport), there is always more background image to the right — so the scroll never reveals an empty edge. The image tiles seamlessly as the element moves, creating an infinite loop.

The same keyframe runs on all three layers but at different durations, producing the parallax depth effect:

| Layer | Duration | Effect |
|---|---|---|
| `.sky` | `40s` | Slowest — distant horizon barely moves |
| `.grass` (trees) | `20s` | Medium speed — mid-distance treeline |
| `.road` | `10s` | Fastest — close road surface rushes past |

The speed ratio is 4:2:1 (sky:grass:road). The faster a layer moves, the closer it appears to the viewer — this is the core principle of CSS parallax.

### Layer Positioning — How the Scene Is Composed

All three background layers use the same `width: 10000%` so they always have excess width to scroll through. Their vertical positions define the scene:

**Sky (`.sky`):**
```css
height: 100vh;
background: url(Assets/sky.jpg);
animation: ani-road linear 40s infinite;
```
Fills the full viewport height. The sky image tiles horizontally as it scrolls. No `position: absolute` — it sits in normal document flow, establishing the page baseline.

**Grass / Trees (`.grass`):**
```css
position: absolute;
bottom: 14vh;
height: 96vh;
background: url(Assets/trees.png);
animation: ani-road linear 20s infinite;
```
Positioned absolutely, starting 14vh from the bottom. The `trees.png` image tiles across its full 10000%-wide width, creating a continuous treeline that scrolls at double the road speed. The height of 96vh means trees cover most of the scene vertically.

**Road (`.road`):**
```css
position: absolute;
top: 79vh;
height: 40vh;
background-image: url(Assets/road.png);
animation: ani-road linear 10s infinite;
```
Sits at 79vh from the top — occupying the bottom portion of the screen. The road image tiles horizontally and scrolls at the fastest speed (10s), creating the sense of ground rushing directly under the car.

### The Car — Fixed on Screen

```css
.car {
  height: 13vh;
  width: 45vh;
  background: url(Assets/car.png) no-repeat;
  background-size: cover;
  position: absolute;
  bottom: 16vh;
  left: 15vw;
}
```

The car itself has **no animation**. It stays fixed at `bottom: 16vh; left: 15vw` while the world moves around it. `width: 45vh` and `height: 13vh` are viewport-height based, keeping the car proportional at any screen size. `background-size: cover` ensures the car image fills the div without distortion.

### The Wheels — Spinning in Place

Both wheels (`.tier-1` and `.tier-2`) are absolutely positioned inside the car `div` and spin continuously:

```css
.tier-1, .tier-2 {
  height: 7vh;
  width: 9vh;
  position: relative;
  background: url(Assets/wheal.png) no-repeat;
  background-size: cover;
  animation: ani-wheal linear .6s infinite;
}
```

```css
@keyframes ani-wheal {
  100% { transform: rotate(360deg); }
}
```

Both wheels use `animation: ani-wheal linear 0.6s infinite` — a full 360° rotation every 0.6 seconds. They spin at the same speed since both are front/rear wheels of the same car. The `linear` timing function ensures smooth, constant rotation with no ease-in or ease-out.

**Wheel positions inside the car div:**

| Wheel | `top` | `left` | Position |
|---|---|---|---|
| `.tier-1` (front) | `5.5vh` | `2.8vh` | Front-left area of the car body |
| `.tier-2` (rear) | `-1.2vh` | `30.3vh` | Rear-right area of the car body |

Both use `position: relative` (not absolute) and are positioned using `top`/`left` offsets from their natural flow position inside the car div.

### Why It Looks Like the Car Is Moving

The car is stationary. The road moves left at 10s/500vw, the trees at 20s/500vw, and the sky at 40s/500vw. The human eye interprets a fixed foreground object against a moving background as the foreground object moving forward — the same visual principle used in classic film productions where actors stand still while a filmed background scrolls behind them.

---

## Assets

| File | Used by | Role |
|---|---|---|
| `Assets/sky.jpg` | `.sky` | Tiling sky background — scrolls at 40s |
| `Assets/trees.png` | `.grass` | Tiling treeline — scrolls at 20s |
| `Assets/road.png` | `.road` | Tiling road surface — scrolls at 10s |
| `Assets/car.png` | `.car` | Static car body image |
| `Assets/wheal.png` | `.tier-1`, `.tier-2` | Spinning wheel image |

---

## Tech Stack

| Technology | Role |
|---|---|
| HTML5 | 5 `div`s — sky, grass, road, car body, 2 wheels |
| CSS3 | `@keyframes` for scroll and spin, `width: 10000%` infinite background trick, parallax via different animation durations, `transform-origin` default for wheel rotation |

---

## Project Structure

```
Car-Game/
├── car.html          # Scene markup — sky, grass, road, car, 2 wheel divs
├── car.css           # All animation — ani-road keyframe, ani-wheal keyframe, layer positioning
└── Assets/
    ├── car.png       # Car body image
    ├── road.png      # Tiling road surface image
    ├── sky.jpg       # Tiling sky background image
    ├── trees.png     # Tiling treeline image
    └── wheal.png     # Wheel image (rotated by CSS)
```

---

## How to Run

1. Clone the repository
   ```bash
   git clone https://github.com/tripathipawan/Car-Game.git
   ```
2. Open `car.html` directly in any modern browser — the animation starts immediately on page load. All assets are local, so no internet connection is required.

---

## Repository

[https://github.com/tripathipawan/Car-Game](https://github.com/tripathipawan/Car-Game)
