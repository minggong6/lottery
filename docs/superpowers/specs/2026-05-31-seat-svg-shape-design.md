# Seat SVG Shape Design

## Goal

Replace the current simple rounded-rectangle seat divs with inline SVG chair shapes, replicating the user-provided SVG mask-based chair icon.

## Current State

- 500 seats (20x25 grid) rendered as `<div class="seat">` with `border-radius` and `box-shadow`
- Colors: olive green (normal), brick red (active/highlight), white (winner)
- Animation: CSS transitions + `pulse` keyframe for winners

## New Design

### SVG Chair Shape

Use the user-provided SVG with a `<mask>` containing a complex `<path>` that draws a cinema/theater chair (backrest, cushion, connector, two legs). The mask is applied to a `<rect>` whose `fill` color changes per state.

### HTML Structure

A hidden SVG at page top defines the shared `<mask>` once:

```html
<svg style="display:none">
  <defs>
    <mask id="seat-mask" ...>
      <path d="..." fill="black"/>
    </mask>
  </defs>
</svg>
```

Each seat in the grid becomes:

```html
<svg class="seat" viewBox="0 0 280 280" xmlns="http://www.w3.org/2000/svg">
  <rect width="280" height="280" mask="url(#seat-mask)" class="seat-fill"/>
</svg>
```

### CSS Changes

- Remove `border-radius` and `box-shadow` from `.seat`
- Add `filter: drop-shadow(0 3px 0 var(--seat-gap))` for shadow
- State colors applied via `.seat-fill` fill:
  - Default: `fill: var(--seat-color)` (#56573C)
  - `.seat.active .seat-fill`: `fill: var(--frame-color)` (#C04829)
  - `.seat.winner .seat-fill`: `fill: var(--highlight)` (#EADDDD)
- Active state: `filter: drop-shadow(0 0 10px var(--frame-color))`
- Winner state: `filter: drop-shadow(0 0 15px var(--highlight))`

### JS Changes

- `initSeats()`: create `<svg>` elements instead of `<div>`, each containing a `<rect>` with `mask="url(#seat-mask)"` and `class="seat-fill"`
- All other JS logic (draw, finalize, state management) unchanged - still uses `.classList.add('active')` / `.classList.add('winner')`

### Performance

- Single `<mask>` definition shared by all 500 seats via `mask="url(#seat-mask)"`
- SVG `viewBox` handles scaling; no need for separate size calculations
- `drop-shadow` filter is GPU-accelerated in modern browsers

## Files Modified

- `风格参考.html` — single file, all changes inline
