# OASIS Format

Open Artwork System Interchange Standard (OASIS) is a modern IC layout format designed to be more compact and efficient than GDSII.

## Overview

OASIS format characteristics:

- **Compact binary encoding**: Variable-length integers
- **Modern design**: Optimized for file size
- **Primitive shapes**: Rectangles, trapezoids, circles
- **Name tables**: String compression
- **IEEE 754 floats**: Standard floating point
- **File extension**: `.oas`

## Main Types

### OASISFile

The root structure:

```rust
pub struct OASISFile {
    pub version: String,
    pub unit: f64,
    pub names: NameTable,
    pub cells: Vec<OASISCell>,
}
```

### OASISCell

Cell/structure in the design:

```rust
pub struct OASISCell {
    pub name: String,
    pub elements: Vec<OASISElement>,
}
```

### OASISElement

Enum of OASIS elements:

```rust
pub enum OASISElement {
    Rectangle(Rectangle),
    Polygon(Polygon),
    Path(OPath),
    Trapezoid(Trapezoid),
    CTrapezoid(CTrapezoid),
    Circle(Circle),
    Text(OText),
    Placement(Placement),
}
```

## Supported Elements

| Element | Description | Status |
|---------|-------------|--------|
| Rectangle | Axis-aligned rectangle | ✅ Full support |
| Polygon | General polygon | ✅ Full support |
| Path | Wire with extensions | ✅ Full support |
| Trapezoid | Trapezoidal shape | ✅ Full support |
| CTrapezoid | Constrained trapezoid | ✅ Full support |
| Circle | Circle primitive | ✅ Full support |
| Text | Text label | ✅ Full support |
| Placement | Cell instance | ✅ Full support |

## Name Tables

OASIS uses name tables for efficient string storage:

```rust
pub struct NameTable {
    pub cell_names: HashMap<u32, String>,
    pub text_strings: HashMap<u32, String>,
    pub prop_names: HashMap<u32, String>,
}
```

## Binary Format

### Variable-Length Integers

Unsigned integers use 7 bits per byte:
```
0xxxxxxx = 0-127
1xxxxxxx 0yyyyyyy = 128-16383
...
```

### Zigzag Encoding

Signed integers use zigzag encoding:
```
0 → 0
-1 → 1
1 → 2
-2 → 3
```

## Next Steps

- [Reading OASIS Files](./oasis-reading.md)
- [Writing OASIS Files](./oasis-writing.md)
- [OASIS Elements](./oasis-elements.md)
