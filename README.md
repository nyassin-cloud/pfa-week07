Midterm checkpoint + peer run
Maya Character Retopology Assistant

This is a Python tool that runs inside Autodesk Maya 2027. It uses Maya's built-in `polyRetopo` command to generate retopology, then adds controls for reviewing the result. It does not require third-party Python packages.
The interface and character preview have been tested in Maya 2027.2 on Windows. Other Maya versions have not been verified.

My revised pitch focuses on using Maya's existing retopology engine rather than creating a new automatic topology algorithm. The goal is to help an artist generate a starting mesh, compare it with the original, and identify areas that need manual refinement.

How to run it

1. Download or copy this project folder to your computer. Keep `retopo_assistant.py` in it.
2. Open Maya.
3. Open **Windows > General Editors > Script Editor**.
4. Choose a **Python** tab, not MEL.
5. Paste the following code. Replace the folder path with the location of the project on your computer.
6. Select the code and press **Ctrl+Enter** to execute it.

```python
import sys

folder = r'C:\Users\free\Documents\maya_retopology_assistant'
if folder not in sys.path:
    sys.path.insert(0, folder)

import retopo_assistant
retopo_assistant.show()
```

The **Maya Character Retopology Assistant** window should appear. Select one polygon mesh object, click **Use Selected Mesh**, enter a target face count, then click **Generate Preview**.

If you replace the Python file with a newer version, restart Maya before running it again so the updated code is loaded.

## What works

The following have been observed working in the Maya interface:

- Opening the tool window.
- Registering the selected character mesh as the source.
- Reporting source face count, disconnected shells, nonmanifold edges and vertices, and lamina faces.
- Generating a separate retopology preview using Maya's engine.
- Displaying the candidate and its new edge layout.
- Reporting actual face count, percentage reduction, generation time, and a warning when the target is missed.

### Character test result

| Measurement | Result |
| --- | --- |
| Source face count | 16,826 |
| Requested face count | 5,000 |
| Actual preview face count | 5,937 |
| Face-count reduction | 64.7% |
| Generation time | 8.74 seconds |

This result demonstrates mesh generation and review, not animation-ready edge flow. Maya did not reach the requested face count within the tool's 10% tolerance, and the tool reported that warning.

### Implemented, with further testing needed

- **Show Original / Show Candidate / Show Overlay:** switch visibility for comparison.
- **Multiple candidates:** generate previews with different settings and select them from the list.
- **Region marking:** save selected source faces into face or joint selection sets for later review. Marking adds to the set; it does not control retopology density.
- **Accept / Discard:** keep a selected result as an independent mesh or remove its preview and private reference snapshot.
- **Reset Settings:** restore default controls without removing existing candidates.
- **Preserve Hard Edges, preprocessing, and X symmetry:** pass settings to Maya's retopology node. Symmetry uses the object's pivot and the +X-to−X direction.
- **Non-quad inspection:** select candidate faces that are not quads.
- **Distance heat map:** measure candidate-vertex distances to a saved source surface. A color-set API error was found during the live demo and corrected in the Python file. The corrected heat map has not yet been verified in the live Maya interface.

## What does not work yet / limitations

- The tool does **not** automatically generate anatomical loops around eyes, mouths, shoulders, elbows, hips, or knees. Manual refinement is still needed.
- Marked regions do not receive extra polygons and do not influence Maya's engine in this version.
- Automatic skeleton creation, joint-bending tests, and UV generation are not implemented.
- The requested polygon count is a target, not a guarantee.
- The heat map samples unsigned distances from candidate vertices to the source surface. It does not measure full surface error or deformation quality. Blue means close to the source; red means the chosen distance threshold or greater.
- Source geometry is retained. Display controls temporarily change source visibility. Region selection sets can add Maya component-membership nodes to source history.
- Previews use Maya's default material; material transfer is not implemented.
- The tool expects one mesh shape on the selected object. It rejects nonmanifold or lamina geometry before generation and does not automatically repair it.
- Retopology and distance measurement may take time. Wait for processing to finish before clicking another control.
- Candidate list state lasts only while the window is open. Closing keeps preview meshes in the scene but removes their private reference snapshots.
- Undo/Redo and manually deleting tracked objects can make the Python candidate list out of sync. Use the tool's controls during a session.
- Other Maya versions, creature meshes, and animation deformation have not been fully tested.

- [Autodesk: Retopologize settings](https://help.autodesk.com/cloudhelp/2026/ENU/Maya-Modeling/files/GUID-FDE163EE-39C1-421E-AC31-9956D21F6D0F.htm)
- [Autodesk: polyRetopo node attributes](https://help.autodesk.com/cloudhelp/2026/ENU/Maya-Tech-Docs/Nodes/polyRetopo.html)
