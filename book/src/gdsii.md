# GDSII Format

GDSII Stream Format is the industry-standard format for IC layout data. LayKit provides complete support for reading and writing GDSII files.

## Overview

GDSII is a binary format with these characteristics:

- **Binary encoding**: Big-endian byte order
- **Record-based structure**: Each record has type, data type, and data
- **Hierarchical organization**: Libraries contain structures (cells)
- **Custom floating point**: 8-byte Real8 format
- **File extension**: `.gds`

## Main Types

### GDSIIFile

The root structure representing a complete GDSII file:

```rust
pub struct GDSIIFile {
    pub version: i16,
    pub library_name: String,
    pub units: (f64, f64),  // (user_unit, database_unit) in meters
    pub structures: Vec<GDSStructure>,
}
```

**Key methods:**
- `new(library_name: String) -> Self` - Create empty file
- `read_from_file(path: &str) -> Result<Self, Box<dyn Error>>` - Read from disk
- `write_to_file(&self, path: &str) -> Result<(), Box<dyn Error>>` - Write to disk

### GDSStructure

Represents a cell/structure in the design:

```rust
pub struct GDSStructure {
    pub name: String,
    pub creation_time: GDSTime,
    pub modification_time: GDSTime,
    pub elements: Vec<GDSElement>,
}
```

### GDSElement

An enum of all possible GDSII elements:

```rust
pub enum GDSElement {
    Boundary(Boundary),      // Polygons
    Path(GPath),             // Wires/traces
    Text(GText),             // Text labels
    StructRef(StructRef),    // Cell instances
    ArrayRef(ArrayRef),      // Cell arrays
    Node(Node),              // Net topology
    Box(GDSBox),             // Box elements
}
```

## Supported Elements

| Element | Description | Status |
|---------|-------------|--------|
| Boundary | Polygon with layer/datatype | ✅ Full support |
| Path | Wire with width | ✅ Full support |
| Text | Text labels | ✅ Full support |
| StructRef | Cell instance | ✅ Full support |
| ArrayRef | Cell array | ✅ Full support |
| Node | Net topology | ✅ Full support |
| Box | Box element | ✅ Full support |

## Binary Format Details

### Record Structure

```
[2 bytes: length] [1 byte: record type] [1 byte: data type] [n bytes: data]
```

### Units

Units are stored as a pair of f64 values in meters:
- `units.0` - User unit (typically 1e-6 for micrometers)
- `units.1` - Database unit (typically 1e-9 for nanometers)

### Coordinates

All coordinates are 32-bit signed integers (`i32`) in database units.

## Next Steps

- [Reading GDSII Files](./gdsii-reading.md)
- [Writing GDSII Files](./gdsii-writing.md)
- [GDSII Elements](./gdsii-elements.md)
