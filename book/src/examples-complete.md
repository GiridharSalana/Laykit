# Complete Examples

Full working examples demonstrating LayKit features.

## Basic GDSII File Creation

Complete program to create a GDSII file with multiple elements:

```rust
use laykit::{Boundary, GDSElement, GDSIIFile, GDSStructure, GDSTime, GPath, GText};

fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Create library
    let mut gds = GDSIIFile::new("DEMO_LIB".to_string());
    gds.units = (1e-6, 1e-9); // 1µm, 1nm
    
    // Create structure
    let mut structure = GDSStructure {
        name: "TOP_CELL".to_string(),
        creation_time: GDSTime::now(),
        modification_time: GDSTime::now(),
        elements: Vec::new(),
    };
    
    // Add rectangle
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
    
    // Add path
    structure.elements.push(GDSElement::Path(GPath {
        layer: 2,
        datatype: 0,
        pathtype: 0,
        width: Some(100),
        xy: vec![(0, 0), (5000, 0), (5000, 5000)],
        properties: Vec::new(),
    }));
    
    // Add text
    structure.elements.push(GDSElement::Text(GText {
        layer: 3,
        texttype: 0,
        string: "DEMO".to_string(),
        xy: (2500, 2500),
        strans: None,
        properties: Vec::new(),
    }));
    
    gds.structures.push(structure);
    gds.write_to_file("demo.gds")?;
    
    println!("✅ Created demo.gds");
    Ok(())
}
```

## Format Conversion

Convert between GDSII and OASIS:

```rust
use laykit::{GDSIIFile, OASISFile, converter};

fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Read GDSII
    println!("Reading GDSII file...");
    let gds = GDSIIFile::read_from_file("input.gds")?;
    println!("  Library: {}", gds.library_name);
    println!("  Structures: {}", gds.structures.len());
    
    // Convert to OASIS
    println!("Converting to OASIS...");
    let oasis = converter::gdsii_to_oasis(&gds)?;
    oasis.write_to_file("output.oas")?;
    println!("  ✅ Written output.oas");
    
    // Read OASIS
    println!("Reading OASIS file...");
    let oasis = OASISFile::read_from_file("output.oas")?;
    println!("  Cells: {}", oasis.cells.len());
    
    // Convert back to GDSII
    println!("Converting to GDSII...");
    let gds2 = converter::oasis_to_gdsii(&oasis)?;
    gds2.write_to_file("roundtrip.gds")?;
    println!("  ✅ Written roundtrip.gds");
    
    println!("\n✅ All conversions completed!");
    Ok(())
}
```

## Processing All Elements

Iterate through and analyze all elements:

```rust
use laykit::{GDSIIFile, GDSElement};

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let gds = GDSIIFile::read_from_file("design.gds")?;
    
    let mut stats = Statistics::default();
    
    for structure in &gds.structures {
        println!("\n📦 Structure: {}", structure.name);
        
        for element in &structure.elements {
            stats.total_elements += 1;
            
            match element {
                GDSElement::Boundary(b) => {
                    stats.boundaries += 1;
                    println!("  ▢ Boundary: layer {}, {} vertices", 
                        b.layer, b.xy.len());
                }
                GDSElement::Path(p) => {
                    stats.paths += 1;
                    println!("  ─ Path: layer {}, width {:?}", 
                        p.layer, p.width);
                }
                GDSElement::Text(t) => {
                    stats.texts += 1;
                    println!("  T Text: \"{}\"", t.string);
                }
                GDSElement::StructRef(s) => {
                    stats.refs += 1;
                    println!("  → Reference: {}", s.sname);
                }
                GDSElement::ArrayRef(a) => {
                    stats.arrays += 1;
                    println!("  ⊞ Array: {} [{}×{}]", 
                        a.sname, a.columns, a.rows);
                }
                GDSElement::Node(n) => {
                    stats.nodes += 1;
                    println!("  ◉ Node: layer {}", n.layer);
                }
                GDSElement::Box(b) => {
                    stats.boxes += 1;
                    println!("  □ Box: layer {}", b.layer);
                }
            }
        }
    }
    
    println!("\n📊 Statistics:");
    println!("  Total elements: {}", stats.total_elements);
    println!("  Boundaries: {}", stats.boundaries);
    println!("  Paths: {}", stats.paths);
    println!("  Texts: {}", stats.texts);
    println!("  References: {}", stats.refs);
    println!("  Arrays: {}", stats.arrays);
    println!("  Nodes: {}", stats.nodes);
    println!("  Boxes: {}", stats.boxes);
    
    Ok(())
}

#[derive(Default)]
struct Statistics {
    total_elements: usize,
    boundaries: usize,
    paths: usize,
    texts: usize,
    refs: usize,
    arrays: usize,
    nodes: usize,
    boxes: usize,
}
```

## Hierarchical Design

Create a design with cell references:

```rust
use laykit::{Boundary, GDSElement, GDSIIFile, GDSStructure, 
             GDSTime, StructRef};

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let mut gds = GDSIIFile::new("HIERARCHICAL".to_string());
    gds.units = (1e-6, 1e-9);
    
    // Create subcell
    let mut subcell = GDSStructure {
        name: "SUBCELL".to_string(),
        creation_time: GDSTime::now(),
        modification_time: GDSTime::now(),
        elements: Vec::new(),
    };
    
    subcell.elements.push(GDSElement::Boundary(Boundary {
        layer: 1,
        datatype: 0,
        xy: vec![(0, 0), (1000, 0), (1000, 1000), (0, 1000), (0, 0)],
        properties: Vec::new(),
    }));
    
    // Create top cell
    let mut topcell = GDSStructure {
        name: "TOPCELL".to_string(),
        creation_time: GDSTime::now(),
        modification_time: GDSTime::now(),
        elements: Vec::new(),
    };
    
    // Add multiple instances of subcell
    for i in 0..3 {
        topcell.elements.push(GDSElement::StructRef(StructRef {
            sname: "SUBCELL".to_string(),
            xy: (i * 2000, 0),
            strans: None,
            properties: Vec::new(),
        }));
    }
    
    gds.structures.push(subcell);
    gds.structures.push(topcell);
    gds.write_to_file("hierarchical.gds")?;
    
    println!("✅ Created hierarchical.gds");
    Ok(())
}
```

## Running Examples

The repository includes working examples:

```bash
# Clone repository
git clone https://github.com/giridharsalana/laykit.git
cd laykit

# Run basic usage example
cargo run --example basic_usage

# Run GDSII-only example
cargo run --example gdsii_only

# Run OASIS-only example
cargo run --example oasis_only
```
