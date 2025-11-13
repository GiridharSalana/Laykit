# Writing GDSII Files

Learn how to create and write GDSII files with LayKit.

## Creating a New File

```rust
use laykit::{GDSIIFile, GDSStructure, GDSTime, GDSElement, Boundary};

let mut gds = GDSIIFile::new("MY_LIBRARY".to_string());
gds.units = (1e-6, 1e-9); // 1µm user unit, 1nm database unit
```

## Adding Structures

```rust
let mut structure = GDSStructure {
    name: "TOP_CELL".to_string(),
    creation_time: GDSTime::now(),
    modification_time: GDSTime::now(),
    elements: Vec::new(),
};

// Add elements to structure...

gds.structures.push(structure);
```

## Adding Elements

### Rectangle

```rust
use laykit::{GDSElement, Boundary};

structure.elements.push(GDSElement::Boundary(Boundary {
    layer: 1,
    datatype: 0,
    xy: vec![
        (0, 0),
        (10000, 0),
        (10000, 5000),
        (0, 5000),
        (0, 0),
    ],
    properties: Vec::new(),
}));
```

### Path

```rust
use laykit::{GDSElement, GPath};

structure.elements.push(GDSElement::Path(GPath {
    layer: 2,
    datatype: 0,
    pathtype: 0,
    width: Some(100),
    xy: vec![(0, 0), (1000, 1000), (2000, 0)],
    properties: Vec::new(),
}));
```

### Text

```rust
use laykit::{GDSElement, GText};

structure.elements.push(GDSElement::Text(GText {
    layer: 3,
    texttype: 0,
    string: "LABEL".to_string(),
    xy: (500, 500),
    strans: None,
    properties: Vec::new(),
}));
```

## Writing to File

```rust
gds.write_to_file("output.gds")?;
println!("GDSII file written successfully!");
```

## Complete Example

See [Complete Examples](./examples-complete.md) for full working programs.
