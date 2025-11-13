# GDSII Elements

Detailed reference for all GDSII element types supported by LayKit.

## Boundary

Polygon element with arbitrary vertices.

```rust
pub struct Boundary {
    pub layer: i16,
    pub datatype: i16,
    pub xy: Vec<(i32, i32)>,
    pub properties: Vec<GDSProperty>,
}
```

**Usage:** Representing filled shapes, design regions, and complex polygons.

## Path

Wire or trace element with specified width.

```rust
pub struct GPath {
    pub layer: i16,
    pub datatype: i16,
    pub pathtype: i16,
    pub width: Option<i32>,
    pub xy: Vec<(i32, i32)>,
    pub properties: Vec<GDSProperty>,
}
```

**Path types:**
- `0` - Square ends (default)
- `1` - Round ends
- `2` - Flush ends

## Text

Text label element.

```rust
pub struct GText {
    pub layer: i16,
    pub texttype: i16,
    pub string: String,
    pub xy: (i32, i32),
    pub strans: Option<STrans>,
    pub properties: Vec<GDSProperty>,
}
```

## StructRef (SREF)

Reference to another structure (cell instance).

```rust
pub struct StructRef {
    pub sname: String,
    pub xy: (i32, i32),
    pub strans: Option<STrans>,
    pub properties: Vec<GDSProperty>,
}
```

## ArrayRef (AREF)

Array of structure references.

```rust
pub struct ArrayRef {
    pub sname: String,
    pub columns: i16,
    pub rows: i16,
    pub xy: [(i32, i32); 3],
    pub strans: Option<STrans>,
    pub properties: Vec<GDSProperty>,
}
```

The `xy` array contains:
- `[0]` - Reference point
- `[1]` - Column spacing point
- `[2]` - Row spacing point

## STrans

Transformation for references and text.

```rust
pub struct STrans {
    pub reflect_x: bool,
    pub absolute_mag: bool,
    pub absolute_angle: bool,
    pub magnification: Option<f64>,
    pub angle: Option<f64>,
}
```

## Node

Net connectivity element.

```rust
pub struct Node {
    pub layer: i16,
    pub nodetype: i16,
    pub xy: Vec<(i32, i32)>,
    pub properties: Vec<GDSProperty>,
}
```

## Box

Box element (rare in modern designs).

```rust
pub struct GDSBox {
    pub layer: i16,
    pub boxtype: i16,
    pub xy: [(i32, i32); 5],
    pub properties: Vec<GDSProperty>,
}
```
