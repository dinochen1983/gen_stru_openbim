
Simple OpenBIM Model with Python

This project demonstrates how to create a simple structural frame and wall OpenBIM model using Python and IfcOpenShell.

The script programmatically generates:

    IFC project structure (Project, Site, Building, Storey)
    Basic columns and beams (frame)
    Simple wall elements
    Exported .ifc file

Tech Stack

    Python 3
    IfcOpenShell
    IFC (OpenBIM standard)

Run
bash

pip install ifcopenshell
python create_frame_wall_model.py

Open the generated IFC file in any BIM viewer (e.g., BlenderBIM, BIMVision).

A minimal example for learning IFC generation and BIM automation with Python.
