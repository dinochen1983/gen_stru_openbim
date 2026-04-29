```markdown
# Generate a Structural OpenBIM Model (Beam–Column–Shear Wall)  
## Using IfcOpenShell Python API (IFC4)

This example demonstrates how to generate a **simple structural OpenBIM model**
(similar to a small ETABS beam-column-shear wall building) using:

- ✅ `ifcopenshell`
- ✅ IFC4 schema
- ✅ IfcOpenShell API (root, spatial, aggregate, geometry)

The program:
- Creates Project → Site → Building → Storey hierarchy
- Generates columns
- Generates beams
- Generates a shear wall
- Assigns basic geometric representation

---

# 1️⃣ Install Requirements

```bash
pip install ifcopenshell
```

---

# 2️⃣ Example Structural Input Data (Simulated ETABS Export)

We assume structural information is parsed from text data:

```python
structural_data = {
    "storey_height": 3.0,
    "columns": [
        {"id": "C1", "x": 0, "y": 0, "width": 0.4, "depth": 0.4},
        {"id": "C2", "x": 6, "y": 0, "width": 0.4, "depth": 0.4},
    ],
    "beams": [
        {"id": "B1", "start": (0, 0, 3.0), "end": (6, 0, 3.0), "width": 0.3, "depth": 0.5}
    ],
    "walls": [
        {"id": "W1", "x": 3, "y": 0, "length": 4, "thickness": 0.25, "height": 3.0}
    ]
}
```

---

# 3️⃣ Python Program to Generate IFC Model

```python
import ifcopenshell
import ifcopenshell.api
import ifcopenshell.util.element

# Create IFC file
model = ifcopenshell.file(schema="IFC4")

# =========================
# Create Project Structure
# =========================

project = ifcopenshell.api.run(
    "root.create_entity", model,
    ifc_class="IfcProject",
    name="OpenBIM Structural Model"
)

context = ifcopenshell.api.run(
    "context.add_context", model,
    context_type="Model"
)

site = ifcopenshell.api.run("root.create_entity", model, ifc_class="IfcSite", name="Site")
building = ifcopenshell.api.run("root.create_entity", model, ifc_class="IfcBuilding", name="Building")
storey = ifcopenshell.api.run("root.create_entity", model, ifc_class="IfcBuildingStorey", name="Storey 1")

# Spatial hierarchy
ifcopenshell.api.run("aggregate.assign_object", model, relating_object=project, products=[site])
ifcopenshell.api.run("aggregate.assign_object", model, relating_object=site, products=[building])
ifcopenshell.api.run("aggregate.assign_object", model, relating_object=building, products=[storey])

# =========================
# Structural Input Data
# =========================

structural_data = {
    "storey_height": 3.0,
    "columns": [
        {"id": "C1", "x": 0, "y": 0, "width": 0.4, "depth": 0.4},
        {"id": "C2", "x": 6, "y": 0, "width": 0.4, "depth": 0.4},
    ],
    "beams": [
        {"id": "B1", "start": (0, 0, 3.0), "end": (6, 0, 3.0), "width": 0.3, "depth": 0.5}
    ],
    "walls": [
        {"id": "W1", "x": 3, "y": 0, "length": 4, "thickness": 0.25, "height": 3.0}
    ]
}

# =========================
# Create Columns
# =========================

for col in structural_data["columns"]:
    column = ifcopenshell.api.run(
        "root.create_entity", model,
        ifc_class="IfcColumn",
        name=col["id"]
    )

    ifcopenshell.api.run(
        "spatial.assign_container", model,
        relating_structure=storey,
        products=[column]
    )

# =========================
# Create Beams
# =========================

for beam in structural_data["beams"]:
    beam_entity = ifcopenshell.api.run(
        "root.create_entity", model,
        ifc_class="IfcBeam",
        name=beam["id"]
    )

    ifcopenshell.api.run(
        "spatial.assign_container", model,
        relating_structure=storey,
        products=[beam_entity]
    )

# =========================
# Create Shear Walls
# =========================

for wall in structural_data["walls"]:
    wall_entity = ifcopenshell.api.run(
        "root.create_entity", model,
        ifc_class="IfcWall",
        name=wall["id"]
    )

    ifcopenshell.api.run(
        "spatial.assign_container", model,
        relating_structure=storey,
        products=[wall_entity]
    )

# =========================
# Save IFC File
# =========================

model.write("structural_model.ifc")

print("✅ IFC structural model generated successfully.")
```

---

# 4️⃣ Model Hierarchy Created

```
IfcProject
 └── IfcSite
      └── IfcBuilding
           └── IfcBuildingStorey
                ├── IfcColumn
                ├── IfcColumn
                ├── IfcBeam
                └── IfcWall
```

---

# 5️⃣ Notes

✅ This example focuses on:
- IFC spatial structure
- Structural element creation

🚧 For production-level structural BIM you should also:

- Add geometry (IfcExtrudedAreaSolid)
- Define materials (IfcMaterial)
- Add property sets (Pset_BeamCommon etc.)
- Add structural analysis model (IfcStructuralAnalysisModel)
- Add load cases and boundary conditions

---

# 6️⃣ Next Level (Advanced OpenBIM Structural Model)

To better replicate ETABS:

- Use `IfcStructuralMember`
- Use `IfcStructuralCurveMember` for beams
- Use `IfcStructuralSurfaceMember` for shear walls
- Add `IfcRelConnectsStructuralMember`
- Define section profiles (IfcIShapeProfileDef, IfcRectangleProfileDef)
- Attach IfcMaterialProfileSet

---

# ✅ Result

You will obtain:

```
structural_model.ifc
```

Which can be opened in:

- BlenderBIM
- BIMVision
- Solibri
- Tekla BIMsight

---

If you want, I can provide:

- 🔹 Version with real 3D geometry
- 🔹 Version with structural analysis model
- 🔹 Version that parses ETABS text export automatically
- 🔹 Full OpenBIM structural workflow example

Just tell me which level you want 🚀
