# ADR-0001: Adopt ZMK Pointing Acceleration

## Status

Accepted

## Context

The roBa right half includes a PMW3610 trackball that is exposed to ZMK as a physical pointing device. The current configuration sends a fixed movement amount to the host, which makes fine cursor placement difficult during text selection, copy, and paste workflows.

ZMK's built-in input processors support fixed scaling, transforms, scroll conversion, and temporary layer activation. Fixed scaling can make all movement slower or faster, but it cannot make slow movements precise while preserving fast long-distance cursor movement.

## Decision

Use the `oleksandrmaslov/zmk-pointing-acceleration` external ZMK module as an input processor for the trackball listener.

Configure the processor to reduce low-speed movement and increase high-speed movement:

- `min-factor = <700>` for fine low-speed control
- `max-factor = <3000>` for faster large movements
- `speed-threshold = <1200>` as the acceleration start point
- `speed-max = <6000>` as the maximum acceleration point
- `acceleration-exponent = <2>` for a smooth quadratic curve
- `track-remainders` to preserve fractional movement while scaling

Pin the module revision in `config/west.yml` to avoid unexpected behavior changes from upstream `main`.

## Consequences

### Pros

- Slow trackball operation becomes more precise for text selection and small cursor adjustments.
- Fast trackball operation can still move the cursor across larger distances.
- The implementation stays in the ZMK input processor chain and composes with the existing Auto Mouse Layer processor.

### Cons

- The firmware now depends on an additional external ZMK module.
- The module must remain compatible with the project's ZMK revision and PMW3610 driver.
- Trackball feel will likely need hardware testing and parameter tuning after flashing.

## Alternatives Considered

### Option 1: Use ZMK's built-in scaler input processor

- Summary:
  Apply a fixed `zip_xy_scaler` multiplier or divisor to all trackball movement.

- Reason not adopted:
  It only changes the global cursor speed. Slowing it down improves precision but makes large movements tedious; speeding it up keeps large movements usable but makes fine placement harder.

### Option 2: Tune PMW3610 CPI only

- Summary:
  Change `CONFIG_PMW3610_CPI` or related PMW3610 sensitivity settings.

- Reason not adopted:
  CPI is also a fixed sensitivity change. It does not satisfy the requirement that cursor movement vary according to operating speed.

### Option 3: Rely on host OS pointer acceleration

- Summary:
  Leave firmware unchanged and tune mouse acceleration in macOS, Windows, or Linux.

- Reason not adopted:
  Host-side acceleration is environment-specific and may affect other pointing devices. Firmware-side acceleration keeps roBa behavior portable across paired hosts.

## Links

- https://zmk.dev/docs/features/pointing
- https://zmk.dev/docs/keymaps/input-processors
- https://github.com/oleksandrmaslov/zmk-pointing-acceleration

## Notes

If the first hardware test feels too slow for normal cursor movement, raise `min-factor` toward `800` or lower `speed-threshold`. If fine selection still feels too jumpy, lower `min-factor` toward `600` or raise `speed-threshold`.

The Auto Mouse Layer referenced in this ADR was later removed. See [ADR-0002](./0002-remove-auto-mouse-layer.md).
