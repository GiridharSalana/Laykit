# Reading GDSII Files

Learn how to read and parse GDSII files with LayKit.

## Basic Reading

The simplest way to read a GDSII file:

```rust
use laykit::GDSIIFile;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let gds = GDSIIFile::read_from_file("design.gds")?;
    println!("Loaded: {}", gds.library_name);
    Ok(())
}
```

## Accessing File Information

```rust
let gds = GDSIIFile::read_from_file("design.gds")?;

println!("Library: {}", gds.library_name);
println!("Version: {}", gds.version);
println!("Units: {} user, {} database (meters)", gds.units.0, gds.units.1);
println!("Structures: {}", gds.structures.len());
```

## Iterating Through Structures

```rust
for structure in &gds.structures {
    println!("\nStructure: {}", structure.name);
    println!("  Created: {:04}-{:02}-{:02}",
        structure.creation_time.year,
        structure.creation_time.month,
        structure.creation_time.day
    );
    println!("  Elements: {}", structure.elements.len());
}
```

## Processing Elements

```rust
use laykit::GDSElement;

for element in &structure.elements {
    match element {
        GDSElement::Boundary(b) => {
            println!("Boundary: layer={}, {} vertices", b.layer, b.xy.len());
        }
        GDSElement::Path(p) => {
            println!("Path: layer={}, width={:?}", p.layer, p.width);
        }
        GDSElement::Text(t) => {
            println!("Text: \"{}\" at ({}, {})", t.string, t.xy.0, t.xy.1);
        }
        GDSElement::StructRef(s) => {
            println!("Reference: {}", s.sname);
        }
        _ => {}
    }
}
```

## Error Handling

```rust
match GDSIIFile::read_from_file("design.gds") {
    Ok(gds) => {
        println!("Successfully read {} structures", gds.structures.len());
    }
    Err(e) => {
        eprintln!("Error reading GDSII file: {}", e);
    }
}
```
