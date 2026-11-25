<img src="assets/bagviewer.svg" width="72" align="right" alt="">

# BagViewer

**by Djoka Technologies** · version 1.7.1 · [release notes](RELEASE_NOTES.md)

Portable **ROS1** rosbag viewer (Kinetic and later) for Windows, in the spirit of RViz.
A single `BagViewer.exe` file (about 57 MB): nothing to install, no ROS, no Python.
100% local processing: the bag is read on the computer and its data never leaves it. The only network
access is the update check on GitHub (see [Updates](#updates)).

Requirements: Windows 10 or 11, 64-bit. A graphics card is not required (see below).

## Usage

- Start `BagViewer.exe`: the welcome screen offers *Open a bag*. Or use **File > Open bag** (Ctrl+O),
  drop a `.bag` into the window, or drop the `.bag` onto the exe. *File > Recent bags* reopens a bag.
- The default view is the **2D top view**. The *Top view (2D)* button (or `T`) switches to 3D.
- **Fixed Frame**: the display frame (by default the root of the TF tree, e.g. `world`).
- **Follow**: the camera follows a frame (by default `base_link`).
- *Displays* panel: checkbox to show or hide, editable properties below, *Add…* to create a display.
  *Topics* tab: double-click a topic to display it.
- The *Status* column reports problems (e.g. a missing TF to the fixed frame).
- **Undock a panel** (Plots, Messages, Inspector, VDA 5050, Displays): its **Undock** button opens it in its
  own window, a normal Windows window: move it to another screen, maximize it or snap it to a side
  (Win + arrows) while the main window shows the view. It stays in sync with playback and the keyboard
  shortcuts (Space, ←/→…) work in it. **Dock back** (top right of the window) puts it back; closing the
  window closes the panel. Undocked panels reopen in their window at the next start.
- **View > Theme**: *Light* (default) or *Dark*. The change is immediate and remembered (`--theme dark` on
  the command line). Default colors (grid, TF trajectory, plot curves) follow the theme; colors you picked
  yourself are kept.
- *File > Save screenshot…* (Ctrl+P) saves the view as PNG. *Help > Controls…* recalls the mouse and keyboard
  controls, *Help > About BagViewer* shows the version, the rendering mode (hardware / software) and the
  disclaimer.

| Mouse | Action |
|---|---|
| Left drag | pan (rotate in 3D view) |
| Middle drag / Shift + left drag | pan |
| Wheel / right drag | zoom |
| Shift + right drag (top view) | rotate around Z |

| Keyboard | Action |
|---|---|
| Space | play / pause |
| ← / → (Shift: ×10) | -/+ 0.1 s (1 s) |
| Home / End | start / end of the bag (of the start-end window if set) |
| [ / ] | set the start / end of the playback window at the current time |
| B | add a bookmark at the current time |
| Ctrl + ← / → | previous / next bookmark or `/rosout` warning-error |
| M / Esc | measure tool / clear the measure |
| R / T | recenter / toggle 2D-3D |
| Ctrl+I / Ctrl+M / Ctrl+G | Inspector / Messages / Plots panel |
| Ctrl+Shift+B | open a comparison bag (B) for the plots |

### TF tree

Select the **TF** display: its properties show the TF tree of the bag (parent → child), with one checkbox per
frame to show or hide it, the publishing rate (or *static*) and the node that publishes it (widen the panel
or hover a frame to read it). *Show all* / *Hide all*, and right click a frame to show, hide or show only
its subtree. The choice is saved with the settings and the profiles.

### Timeline: events, bookmarks and start-end window

- The timeline shows `/rosout` warnings (amber ticks) and errors (red ticks) at the top, and your bookmarks
  (triangles) at the bottom. Hover a mark to read it; click near it to jump there; Ctrl + ← / → goes to
  the previous / next one. The flag button lists the bookmarks and hides warnings or errors.
- **Bookmarks**: B (or right click on the timeline > *Add bookmark here*) asks for a name; right click on a
  bookmark removes it.
- **Start-end window**: `[` and `]` (or the bracket buttons, or right click on the timeline) set a start and
  an end. Playback, Home / End, the jumps and *Loop* then stay between them, and the plots are framed on
  them. Drag the window edges on the timeline to adjust it; the cross button clears it.
- Bookmarks and the window are remembered for each bag file.

### Measure and coordinates

The status bar shows the coordinates of the mouse in the Fixed Frame (on the ground plane). **Measure** (M)
turns on the ruler: click a first point then a second one to read the distance, the heading and dx / dy; a
third click starts a new measure, Esc clears it.

### Inspector

The **Inspector** panel (Ctrl+I, or right click on a topic > *Inspect messages*) shows every field of a
topic's message at the playback time, like `rostopic echo` in sync with playback. Any topic of the bag can
be inspected, including types that are not drawn. Expand the fields you need (they stay expanded while
playing), step to the previous / next message with the arrows, and right click to copy a value, a field
path or the whole message.

## Displayed message types

| Display | Messages |
|---|---|
| TF | `tf2_msgs/TFMessage`, `tf/tfMessage` (`/tf`, `/tf_static`): axes, names, links; choose the frames in the TF tree |
| TF Trajectory | trajectory of a frame (e.g. `base_link`) computed from TF |
| LaserScan | `sensor_msgs/LaserScan` (intensity, range or flat color; decay time) |
| Map | `nav_msgs/OccupancyGrid` (map / costmap / raw color schemes), and `<topic>_updates` (`map_msgs/OccupancyGridUpdate`) |
| Path | `nav_msgs/Path` |
| Pose | `nav_msgs/Odometry`, `geometry_msgs/PoseStamped`, `geometry_msgs/PoseWithCovarianceStamped` (arrow, history, trail, covariance ellipse) |
| Polygon | `geometry_msgs/PolygonStamped` (footprint) |
| Marker | `visualization_msgs/Marker` and `MarkerArray` (every type except meshes, drawn as a cube) |
| VDA Order | `vda5050_msgs/Order`: nodes, edges (NURBS trajectories), base / horizon, progress, node actions |
| VDA State | `vda5050_msgs/State`: AGV position and heading, deviation range, remaining route, mode / battery / errors |
| Map image | an image file of the site (PNG, JPEG, PGM, BMP, TIFF) or a `map_server` `.yaml`, under the scene |

### Map image (background map)

**File > Load map image (background)…**, *Add… > Map image*, or drop an image into the window: the picture
of a site (e.g. a map exported from the SLAM) is drawn under the scene.
- It is placed like a `map_server` map: **Resolution** (m per pixel), **Origin x / y** (position of the
  lower-left corner) and **Rotation**, in a TF **Frame** (the fixed frame by default). A `map_server`
  `.yaml` (chosen directly, or with the same name next to the image) gives them.
- Without a `.yaml`, align the image on the scene by clicking points (Esc cancels):
  - **Align (2 pairs of points)**: click a recognisable point of the image (a corner, a pillar), then where
    it really is (e.g. on the laser scans), and again with a second point far from the first. Position,
    rotation and scale are set.
  - **Move (1 pair of points)**: same with one point; only moves the image.
  - Then fine-tune the values by hand if needed.
- *Background: transparent* hides the colour of the image corners (e.g. the grey of unknown space);
  *Colors: inverted* gives white walls on dark free space. *Alpha* and *Z offset* as for the maps.
- Big images stay sharp at every zoom, like an online map: an overview (4096 px) is shown at once, and when
  zooming in, the visible area is drawn from the full resolution image, pixel for pixel. The full resolution
  is read in the background (a few seconds for a 17408 x 8832 px map; the status says *Loading the full
  resolution…*) and takes 1 byte per pixel for a grey map (about 150 MB for that map). Untick *Full
  resolution when zoomed* to keep the overview only and save that memory.
- The file and its placement are saved with the settings and the profiles.


### VDA 5050

For bags with `vda5050_msgs` topics (`Order`, `State`, `InstantAction`, `Errors`):
- **VDA Order** display: the order's nodes and edges, drawn along their NURBS trajectories when they have
  one. Base edges are solid, the horizon is faded, edges and nodes already done are grey, the current node is
  highlighted (progress read from the State topic chosen in *Progress from*). *Show the base (released)* and
  *Show the horizon (not released)* choose which parts are drawn (one, the other or both). Labels show the
  actions of the nodes and of the edges (at the centre of the edge), one per line, with their status from the
  State and whether their node / edge is released, e.g. `dock · WAITING · released`.
  If the edge trajectories are far from their nodes, the *Status* column says so; *Use edge trajectories*
  off draws straight edges between the nodes instead.
- **VDA State** display: the AGV pose (red when a FATAL error is active, amber when paused or with errors),
  its deviation range, a "mode · driving · battery · errors" label and, when the bag has no Order topic, the
  remaining route reported in the State.
- The VDA positions are drawn in the TF frame where `agvPosition` matches the robot (`auto`, e.g.
  `IW_world`); the *Frame of the VDA map* property can force another frame.
- **VDA 5050 panel** (*VDA 5050* toolbar button, Ctrl+5): summary (mode, driving, paused, battery, position,
  velocity, order, last node, safety), the order with the progress and status of every node / edge and
  action, the action states, the active errors and the instant actions received (double-click to jump).
- **Timeline**: new errors (when they appear), orders and instant actions are marked; the flag menu can
  hide them (*Show VDA 5050 events*).
- Messages of any type are decoded from the definitions stored in the bag, so the fields of every
  `vda5050_msgs` topic can also be read in the Inspector and plotted.

### Text messages (`/rosout`)

The **Messages** panel (View > Messages panel, Ctrl+M) lists `rosgraph_msgs/Log` (`/rosout`, `/rosout_agg`)
and `std_msgs/String` topics, like rqt_console:
- it opens automatically when the bag contains text messages;
- filters by level (Debug, Info, Warn, Error, Fatal, Text), by topic (*Topics* menu) and by text
  (search in the message and the node name);
- *Follow playback* selects the last message received at the current time; future messages are dimmed;
- double-click a message: playback jumps to that moment of the bag;
- the bottom area shows the full (multi-line) message with its node and `file:function:line`;
- Ctrl+C copies the selected rows.

When both `/rosout` and `/rosout_agg` are present, only `/rosout_agg` is checked by default
(it contains the same messages).

### Plots

The **Plots** panel (*Plots* toolbar button, or Ctrl+G) plots any numeric value of a topic: int, float or
bool, whatever the message type (`std_msgs/Float64`, `Bool`, fields of `nav_msgs/Odometry`,
`sensor_msgs/LaserScan`…).
- On the left, the topic list: expand it down to the field you want. **Double-click** adds the field to the
  active plot (highlighted), or **drag** it onto any plot. *New plot* stacks one more plot; time axes are linked.
- Bools and integers are drawn as steps. Quaternions get a computed `yaw` field (rad).
- `[TF] <frame>` entries give, from TF, `x`, `y`, `z`, `yaw`, `roll`, `pitch`, `speed` (m/s) and
  `angular_speed` (rad/s) of the frame in the Fixed Frame.
- The vertical cursor line follows playback and the legend shows the values at that time.
  **Drag the cursor** or **double-click** in a plot to jump to that time.
- *Sliding window*: the time axis follows playback over the chosen duration. Wheel: zoom; right click:
  pyqtgraph options (CSV/PNG export of the curves…). A plot's *Curves* menu: remove a curve.
- In the *Topics* tab, double-clicking a topic that cannot be drawn in the view opens it in the plots.

### Comparing two bags

**File > Open comparison bag (B)…** (Ctrl+Shift+B, or **Shift + drop** a `.bag` into the window) opens a
second bag next to the main one (A). Every curve of the plots is then also drawn for B, in the same colour
with a **dashed** line, and the legend gives both values at the cursor (`[A]` / `[B]`). The view, the
displays and playback stay on A.
- The **Compare** bar above the plots sets the **B offset** (s): the B curves slide along the time axis.
  - *Align starts* (offset 0): both bags start at the same time, to compare two runs of the same mission.
  - *Same clock*: aligned on the recording time, for consecutive or overlapping logs of the same robot.
  - *Auto-align on ▸ curve*: finds the offset where the B curve best matches the A curve
    (cross-correlation), e.g. on a speed: pick a curve that varies.
  - Type a value or use the arrows / wheel (±0.1 s) to adjust by hand. *cursor in B* gives the B time at
    the cursor.
- The offset is remembered for each pair of bags. Curves whose field is missing in B are listed
  (*n curve(s) not in B*). *File > Close comparison bag* or ✕ on the bar closes B; profiles keep only the
  A curves.

### Profiles (reloadable configurations)

**Profile** menu: a profile is a `.json` file that stores the displays and their settings, the Fixed Frame,
the followed frame, the 2D/3D view, the plots (curves of each plot) and the Messages panel filters.
- *Save profile as…* (Ctrl+Shift+S) creates the file; *Save profile* (Ctrl+S) updates it.
- *Load profile…* (Ctrl+Shift+O) applies it to the open bag and to every bag opened afterwards. Only the
  profile's displays are created. Items missing from the bag (topic, frame…) are reported and skipped.
- The active profile is shown in the window title and reloaded at the next start. On exit, you are asked
  whether to save it if it was modified.
- *Disable profile* goes back to the automatic settings (one display per drawable topic).
- Command line: `BagViewer.exe log.bag --profile my_profile.json`.

Profiles are plain files: share them with colleagues, or keep one per kind of analysis
(navigation, localization, safety…).

Compressed bags (bz2, lz4) are supported. Frame names with or without a leading `/` are unified.

## No graphics card (VM, remote desktop)

Rendering automatically falls back to software OpenGL (Mesa, bundled in the exe). It can also be forced:
`BagViewer.exe --software-gl`.

## Updates

At startup BagViewer asks GitHub ([Djoka-Tech/windows_ros1_bag_viewer](https://github.com/Djoka-Tech/windows_ros1_bag_viewer/releases))
whether a newer release exists. If so, a window shows its release notes with three choices:
- **Download and install**: the new `BagViewer.exe` is downloaded, checked (size and SHA-256 published by
  GitHub) and put in place of the current one; BagViewer restarts with the same bag open. The previous
  version is kept as `BagViewer.exe.old` until the next start, then deleted.
- **Later**: asks again at the next start.
- **Skip this version**: no popup until the following release.

*Help > Check for updates…* checks at any time; *Help > Check for updates at startup* turns the automatic
check off (`--no-update-check` on the command line for one run). Without network access, or behind a proxy
that blocks GitHub, nothing is shown at startup (the manual check explains the problem).

If the folder of `BagViewer.exe` cannot be modified, the new version is saved to *Downloads* and BagViewer
says so. The folder build (`BagViewer_folder.zip`) is not updated in place: the release page is opened instead.

## Files written

`bagviewer_config.json` (displays, fixed frame, theme, active profile, window layout, recent bags and
profiles, update preferences) and `bagviewer.log` are written next to the exe. If that folder is not writable, they go to
`%APPDATA%\BagViewer`. Delete `bagviewer_config.json` to go back to the default settings.

If the computer's security policy blocks executables that unpack themselves into `%TEMP%`, use
`BagViewer_folder.zip`: unzip the folder, then run `BagViewer\BagViewer.exe`.

## Command line

```
BagViewer.exe [file.bag] [--compare other.bag] [--profile profile.json] [--theme dark|light]
              [--software-gl] [--3d] [--no-config] [--no-update-check]
BagViewer.exe file.bag --screenshot shot.png --time 30 [--zoom 20] [--center x,y]
BagViewer.exe --help
```

## Known limitations

- ROS1 bags only (ROS2 `.db3` / `.mcap` are not supported).
- Not displayed: `sensor_msgs/PointCloud2`, camera images, robot model (URDF); `MESH_RESOURCE` markers are
  drawn as cubes. Their numeric fields can still be plotted.
- The messages of the bag are kept in memory, still serialized, while the bag is open (memory use is about
  the uncompressed size of the bag). Camera images and point clouds are not: they are read from the file
  when inspected or plotted.

## Development

```
python -m venv .venv
.venv\Scripts\pip install -r requirements.txt
set PYTHONPATH=src && .venv\Scripts\python -m bagviewer file.bag
.venv\Scripts\python -m pytest tests          # generates tests\data\synthetic.bag
powershell -ExecutionPolicy Bypass -File build.ps1   # -> dist\BagViewer.exe and dist\BagViewer_folder.zip
```

Code in `src/bagviewer/`: `bag_loader.py` (bag reading with `rosbags`), `tf_buffer.py` (interpolated TF
tree), `displays/` (one file per display family), `ui/main_window.py` (main window),
`glsetup.py` (points PyOpenGL at Qt's OpenGL DLL), `updater.py` + `ui/update_dialog.py` (self-update).

## Publishing a release

The version is written in one place: `__version__` in `src/bagviewer/__init__.py`.

1. Set `__version__` (e.g. `1.3.0`) and add a `## 1.3.0 — <date>` section at the top of `RELEASE_NOTES.md`.
2. Run `build.ps1`. It also writes the exe file properties for that version and
   `dist\release_notes_v1.3.0.md`.
3. On GitHub: *Releases > Draft a new release*, tag **`v1.3.0`** (the `v` + the exact version), attach
   **`dist\BagViewer.exe`** (keep this file name), paste `dist\release_notes_v1.3.0.md` as the description
   (it is what users see in the update window), then *Publish release*.

Running copies pick it up at their next start. Drafts and pre-releases are ignored by the update check.

## Visual identity

The interface follows the Djoka Technologies brand guide (`DESIGN.md` of the djoka-tech site), in two themes:
- **Light** (default): pale-mint surface `#F3FAF6`, panels `#D9F3E4`, ink text `#011109`, mint `#75BB99` for
  surfaces and darker mint `#2B6E50` for text accents, so every text color keeps at least 4.5:1 contrast.
- **Dark** (the canonical Djoka identity): ink green background `#011109`, panels `#003E1C`, borders `#024E25`,
  pale mint text `#D9F3E4`, mint highlights `#75BB99`.

In both, rust `#BB2D0C` is reserved for small logo accents. Inter font (bundled, SIL Open Font License 1.1,
see `assets/fonts/README.txt`). Colors, logo and Qt themes live in `src/bagviewer/brand.py`
(`DEFAULT_THEME` selects the default). The `assets/bagviewer.ico` icon is rebuilt with `tools/make_icon.py`
(needs Pillow).

Each theme has its own categorical palette for plot curves, validated for its background (contrast, color
blindness); beyond 8 curves in one plot, colors repeat as dashed lines.

## Disclaimer

This software is provided "as is", without warranty of any kind, express or implied, including but not
limited to the warranties of merchantability, fitness for a particular purpose and noninfringement. In no
event shall Djoka Technologies be liable for any claim, damages or other liability arising from, out of or in
connection with the software or its use.

© 2026 Djoka Technologies
