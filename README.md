![](../../workflows/gds/badge.svg) ![](../../workflows/docs/badge.svg)

# Secret Initial

A 4-bit code detector accepted into the Tiny Tapeout GF 26d shuttle. Enter one
of two secret codes and the 7-segment display shows **C** or **Y**. Any other
code lights only the decimal point.

![Chip layout](https://cyxwh.github.io/tt-secret-initial/gds_render.png)

- [3D layout viewer](https://cyxwh.github.io/tt-secret-initial/)
- [Simulate it in Wokwi](https://wokwi.com/projects/481557245529335809)
- [Project documentation](docs/info.md)
- [Shuttle submission](https://github.com/TinyTapeout/tinytapeout-gf-26d/pull/43)
  (merged 25 November 2026, Singapore time)

## How it works

```
c_ok = IN0 AND (NOT IN1) AND IN2 AND IN3          code 1 0 1 1
y_ok = (NOT IN0) AND IN1 AND IN2 AND (NOT IN3)    code 0 1 1 0
```

| Signal               | Segments | Why                     |
|----------------------|----------|-------------------------|
| `c_ok`               | a, e     | Used only by C          |
| `y_ok`               | b, c, g  | Used only by Y          |
| `c_ok OR y_ok`       | d, f     | Shared by both letters  |
| `NOT (c_ok OR y_ok)` | dp       | No code matched         |

One OR gate serves both shared segments, and its output is inverted for the
decimal point. The circuit is combinational, so it uses no clock or reset.

## From gates to layout

| Item                    | Result                                        |
|-------------------------|-----------------------------------------------|
| Logic                   | 11 gates: 6 AND2, 4 inverters, 1 OR2          |
| Process kit             | GlobalFoundries gf180mcuD (open source PDK)   |
| Flow                    | LibreLane 3.0.14 on GitHub Actions            |
| Tile                    | 1x1, 346.64 x 160.72 um                       |
| Standard cell use       | 4.7 % (includes tap, endcap and tie cells)    |
| Routed wire length      | 641 um                                        |
| Routing DRC errors      | 0                                             |
| Tiny Tapeout precheck   | Passed here and on the shuttle pull request   |

All 11 gates appear one to one in the synthesis report as gf180mcu standard
cells.

## Testing

All 16 input codes were simulated with the Wokwi CLI. Code 1011 shows C, code
0110 shows Y and the other 14 light only the decimal point.

## What I learned

- Drew an NMOS transistor in SiliWiz and read its threshold voltage, about
  1.2 to 1.3 V in simulation, from the current curve.
- Saw how gates become standard cells, then a placed and routed GDS layout.
- Read GitHub Actions results to tell a documentation failure and a failed
  web page deployment apart from a layout problem.

## Credits

Made by Chen Yixin at the Make Your Own Chip workshop with Tiny Tapeout,
ArtScience Museum, Singapore, 24 November 2026. Built on the official Tiny
Tapeout Wokwi template by Uri Shaked.
