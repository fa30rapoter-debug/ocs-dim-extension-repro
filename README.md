# OpenCADStudio — minimal repro: extend only ONE end of a dimension line

Minimal drawing that demonstrates that a linear dimension's dimension line cannot be extended on a
single end in OpenCADStudio 2026.46, and that the two "dimension line end" grips report
`moves_entity: true` but do not change the dimension line when dragged.

Upstream issue: https://github.com/HakanSeven12/OpenCADStudio/issues/3332

## Contents

| File | What it is |
|---|---|
| `OCS_dim_extension_repro.dxf` | DXF 2018, millimetres. 12 × 5.2132 rectangle + one `DIMLINEAR` (style `CHIFENG`, shows 5.21) + one red `LINE` on layer `DIM_EXT` (the hand-drawn extension) + 9 lines of notes |
| `OCS_dim_extension_preview.png` | Rendered preview of the same DXF |

## Measured (read back from entity properties, not from a screenshot)

- dimension: `actual_measurement = 5.2132`, `first_point = (12,0)`, `second_point = (12,5.2132)`, dimension line at `x = 20`
- dimension block geometry after `REGEN`: dimension line `(20,0) → (20,5.2132)` — no overhang at either end
- red line: layer `DIM_EXT`, `(20,−5.6162) → (20,−2.2162)` (gap 2.2162 below the bottom end, length 3.4)
- grips (must be selected first, otherwise the grip list comes back empty):

| grip_id | position | `moves_entity` | interpretation |
|---|---|---|---|
| 0 | (12, 0) | false | first extension-line origin |
| 1 | (12, 5.2132) | true | second extension-line origin |
| 2 | (20, 2.6066) | true | dimension line position |
| 3 | (17.15, 2.6066) | false | dimension text position |
| 4 | (20, 0) | true | dimension line, lower end |
| 5 | (20, 5.2132) | true | dimension line, upper end |

Dragging grip 5 to `(20, 8.6)`: `actual_measurement` stays 5.2132, both extension-line origins are
unchanged, and the dimension line inside the block is still `(20,0) → (20,5.2132)`. The only field that
changes is `definition_point.y` (0 → 3.3868), which has no visual effect on a vertical dimension.

## Not covered

- Only tested on Linux; other platforms not verified.
- Contains no customer data — the geometry is a bare rectangle created for this report.
