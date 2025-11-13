# API Reference

Complete API reference for LayKit.

## Module Structure

```
laykit
├── gdsii   - GDSII format support
├── oasis   - OASIS format support
└── converter - Format conversion utilities
```

## GDSII Module

### GDSIIFile

Main structure for GDSII files.

**Fields:**
- `version: i16` - File format version
- `library_name: String` - Library name
- `units: (f64, f64)` - (user_unit, database_unit) in meters
- `structures: Vec<GDSStructure>` - All structures in the file

**Methods:**
- `new(library_name: String) -> Self` - Create new empty file
- `read_from_file(path: &str) -> Result<Self, Box<dyn Error>>` - Read from file
- `write_to_file(&self, path: &str) -> Result<(), Box<dyn Error>>` - Write to file
- `read<R: Read>(reader: &mut R) -> Result<Self, Box<dyn Error>>` - Read from reader
- `write<W: Write>(&self, writer: &mut W) -> Result<(), Box<dyn Error>>` - Write to writer

### GDSStructure

Represents a cell/structure.

**Fields:**
- `name: String` - Structure name
- `creation_time: GDSTime` - Creation timestamp
- `modification_time: GDSTime` - Modification timestamp
- `elements: Vec<GDSElement>` - Elements in this structure

### GDSElement

Enum of all GDSII element types:

```rust
pub enum GDSElement {
    Boundary(Boundary),
    Path(GPath),
    Text(GText),
    StructRef(StructRef),
    ArrayRef(ArrayRef),
    Node(Node),
    Box(GDSBox),
}
```

### GDSTime

Timestamp structure.

**Fields:**
- `year: i16` - Year
- `month: i16` - Month (1-12)
- `day: i16` - Day (1-31)
- `hour: i16` - Hour (0-23)
- `minute: i16` - Minute (0-59)
- `second: i16` - Second (0-59)

**Methods:**
- `now() -> Self` - Current time

## OASIS Module

### OASISFile

Main structure for OASIS files.

**Fields:**
- `version: String` - OASIS version
- `unit: f64` - Database unit in meters
- `names: NameTable` - Name tables
- `cells: Vec<OASISCell>` - All cells

**Methods:**
- `new() -> Self` - Create new empty file
- `read_from_file(path: &str) -> Result<Self, Box<dyn Error>>` - Read from file
- `write_to_file(&self, path: &str) -> Result<(), Box<dyn Error>>` - Write to file

### OASISCell

Cell definition.

**Fields:**
- `name: String` - Cell name
- `elements: Vec<OASISElement>` - Elements in this cell

### OASISElement

Enum of all OASIS element types:

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

### NameTable

Name storage for OASIS files.

**Fields:**
- `cell_names: HashMap<u32, String>` - Cell name mapping
- `text_strings: HashMap<u32, String>` - Text string mapping
- `prop_names: HashMap<u32, String>` - Property name mapping

## Converter Module

### Functions

#### gdsii_to_oasis

Convert GDSII file to OASIS format.

```rust
pub fn gdsii_to_oasis(gds: &GDSIIFile) -> Result<OASISFile, Box<dyn Error>>
```

**Features:**
- Converts all structures to cells
- Maps element types appropriately
- Detects rectangles from 5-vertex polygons
- Preserves hierarchy

#### oasis_to_gdsii

Convert OASIS file to GDSII format.

```rust
pub fn oasis_to_gdsii(oasis: &OASISFile) -> Result<GDSIIFile, Box<dyn Error>>
```

**Features:**
- Converts all cells to structures
- Maps element types back to GDSII
- Converts primitive shapes to boundaries/paths
- Preserves geometry

#### is_rectangle

Helper function to detect rectangles.

```rust
pub fn is_rectangle(points: &[(i32, i32)]) -> Option<(i32, i32, i32, i32)>
```

Returns `Some((x, y, width, height))` if the points form a rectangle.

## Error Handling

All I/O operations return `Result<T, Box<dyn std::error::Error>>`.

Common error sources:
- File not found
- Invalid file format
- Corrupted data
- I/O errors

**Example:**

```rust
match GDSIIFile::read_from_file("design.gds") {
    Ok(gds) => println!("Success!"),
    Err(e) => eprintln!("Error: {}", e),
}
```

## Type Safety

LayKit uses Rust's type system for safety:
- Strong typing for all elements
- Enum-based element types
- Result types for error handling
- No null pointers
