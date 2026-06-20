# ADR-0002: Remove the Auto Mouse Layer

## Status

Accepted

## Context

The current roBa pointing configuration enables a temporary mouse layer after trackball movement. That layer remaps the `J`, `K`, and `L` keys to mouse buttons for a fixed timeout window.

In practice, this behavior adds hidden state to normal typing. When the temporary layer remains active, trying to type `J` or `K` can trigger mouse buttons instead of normal key input. Avoiding that mistake requires deliberately breaking out of the temporary layer first by pressing an excluded key such as `LSHIFT`, which makes ordinary typing feel more cumbersome than intended.

The current change set also adds explicit mouse button combos on the home-row keys so that removing the temporary layer does not also remove access to primary, secondary, and middle click.

## Decision

Remove the Auto Mouse Layer entirely.

Keep the trackball itself and its pointer acceleration processor, but stop activating any temporary layer from pointer movement. Provide mouse button access through the explicit combos added alongside this change instead of through remapped typing keys.

## Consequences

### Pros

- Removes hidden layer state from normal typing.
- Prevents accidental mouse clicks when the user intends to type `J` or `K`.
- Simplifies the keymap by deleting layer-exit macros and temporary-layer processor wiring.
- Avoids maintaining layer-number coupling between the pointing configuration and the keymap.

### Cons

- `J`, `K`, and `L` no longer become mouse buttons automatically after trackball movement.
- Users must rely on explicit click combos instead of temporary single-key mouse buttons.

## Alternatives Considered

### Option 1: Keep the current temporary mouse layer

- Summary:
  Continue using `zip_temp_layer` to expose temporary mouse buttons after trackball movement.

- Reason not adopted:
  It preserves the hidden state and the extra cleanup logic that this change is intended to remove.

### Option 2: Replace it with a manually activated mouse layer

- Summary:
  Remove automatic activation but keep a dedicated mouse layer behind an explicit layer key.

- Reason not adopted:
  The explicit click combos added in this change already cover button input, so keeping a dedicated mouse layer would add complexity without a clear need.

### Option 3: Switch to ZMK's built-in Auto Mouse Layer

- Summary:
  Use the built-in automouse behavior instead of the current temporary-layer processor setup.

- Reason not adopted:
  The goal is to eliminate automatic mouse-layer activation altogether, not to replace one automatic mechanism with another.

## Links

- [ADR-0001: Adopt ZMK Pointing Acceleration](./0001-adopt-zmk-pointing-acceleration.md)
- https://zmk.dev/docs/keymaps/input-processors

## Notes

If future usage shows that the explicit click combos are insufficient, revisit this as a new decision rather than reintroducing hidden temporary layer state ad hoc.
