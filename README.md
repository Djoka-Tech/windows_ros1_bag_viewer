<img src="assets/bagviewer.svg" width="72" align="right" alt="">

# BagViewer

**by Djoka Technologies**

Portable **ROS1** rosbag viewer (Kinetic and later) for Windows, in the spirit of RViz.
A single BagViewer.exe file: nothing to install, no ROS, no Python.
100% local processing: the bag is read on the computer, no data is sent over the network.

## Usage

- Start BagViewer.exe, then **File > Open bag** (Ctrl+O), drop a .bag into the window,
  or drop the .bag onto the exe.
- The default view is the **2D top view**. The *Top view (2D)* button (or T) switches to 3D.
- **Fixed Frame**: the display frame (by default the root of the TF tree, e.g. world).
- **Follow**: the camera follows a frame (by default base_link).
- *Displays* panel: checkbox to show or hide, editable properties below, *Add…* to create a display.
  *Topics* tab: double-click a topic to display it.
- The *Status* column reports problems (e.g. a missing TF to the fixed frame).
- **View > Theme**: *Light* (default) or *Dark*. The change is immediate and remembered (--theme light on
  the command line). Default colors (grid, TF trajectory, plot curves) follow the theme; colors you picked
  yourself are kept.

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
| Home / End | start / end of the bag |
| R / T | recenter / toggle 2D-3D |

## Displayed message types

| Display | Messages |
|---|---|
| TF | tf2_msgs/TFMessage, tf/tfMessage (/tf, /tf_static): axes, names, links |
| TF Trajectory | trajectory of a frame (e.g. base_link) computed from TF |
| LaserScan | sensor_msgs/LaserScan (intensity, range or flat color; decay time) |
| Map | nav_msgs/OccupancyGrid (map / costmap / raw color schemes), and <topic>_updates (map_msgs/OccupancyGridUpdate) |
| Path | nav_msgs/Path |
| Pose | nav_msgs/Odometry, geometry_msgs/PoseStamped, geometry_msgs/PoseWithCovarianceStamped (arrow, history, trail, covariance ellipse) |
| Polygon | geometry_msgs/PolygonStamped (footprint) |
| Marker | visualization_msgs/Marker and MarkerArray (every type except meshes, drawn as a cube) |

### Text messages (`/rosout`)

The **Messages** panel (View > Messages panel, Ctrl+M) lists rosgraph_msgs/Log (/rosout, /rosout_agg)
and std_msgs/String topics, like rqt_console:
- it opens automatically when the bag contains text messages;
- filters by level (Debug, Info, Warn, Error, Fatal, Text), by topic (*Topics* menu) and by text
  (search in the message and the node name);
- *Follow playback* selects the last message received at the current time; future messages are dimmed;
- double-click a message: playback jumps to that moment of the bag;
- the bottom area shows the full (multi-line) message with its node and file:function:line;
- Ctrl+C copies the selected rows.

When both /rosout and /rosout_agg are present, only /rosout_agg is checked by default
(it contains the same messages).

### Plots
The **Plots** panel (*Plots* toolbar button, or Ctrl+G) plots any numeric value of a topic: int, float or
bool, whatever the message type (std_msgs/Float64, Bool, fields of nav_msgs/Odometry,
sensor_msgs/LaserScan…).
- On the left, the topic list: expand it down to the field you want. **Double-click** adds the field to the
  active plot (highlighted), or **drag** it onto any plot. *New plot* stacks one more plot; time axes are linked.
- Bools and integers are drawn as steps. Quaternions get a computed yaw field (rad).
- [TF] <frame> entries give, from TF, x, y, z, yaw, roll, pitch, speed (m/s) and
  angular_speed (rad/s) of the frame in the Fixed Frame.
- The cursor (light vertical line) follows playback and the legend shows the values at that time.
  **Drag the cursor** or **double-click** in a plot to jump to that time.
- *Sliding window*: the time axis follows playback over the chosen duration. Wheel: zoom; right click:
  pyqtgraph options (CSV/PNG export of the curves…). A plot's *Curves* menu: remove a curve.
- In the *Topics* tab, double-clicking a topic that cannot be drawn in the view opens it in the plots.

### Profiles (reloadable configurations)

**Profile** menu: a profile is a .json file that stores the displays and their settings, the Fixed Frame,
the followed frame, the 2D/3D view, the plots (curves of each plot) and the Messages panel filters.
- *Save profile as…* (Ctrl+Shift+S) creates the file; *Save profile* (Ctrl+S) updates it.
- *Load profile…* (Ctrl+Shift+O) applies it to the open bag and to every bag opened afterwards. Only the
  profile's displays are created. Items missing from the bag (topic, frame…) are reported and skipped.
- The active profile is shown in the window title and reloaded at the next start. On exit, you are asked
  whether to save it if it was modified.
- *Disable profile* goes back to the automatic settings (one display per drawable topic).
- Command line: BagViewer.exe log.bag --profile my_profile.json.

Profiles are plain files: share them with colleagues, or keep one per kind of analysis
(navigation, localization, safety…).

Compressed bags (bz2, lz4) are supported. Frame names with or without a leading / are unified.

## No graphics card (VM, remote desktop)

Rendering automatically falls back to software OpenGL (Mesa, bundled in the exe). It can also be forced:
BagViewer.exe --software-gl.

## Files written

bagviewer_config.json (displays, fixed frame, window, recent files) and bagviewer.log are written next
to the exe. If that folder is not writable, they go to %APPDATA%\BagViewer.

If the computer's security policy blocks executables that unpack themselves into %TEMP%, use
BagViewer_folder.zip: unzip the folder, then run BagViewer\BagViewer.exe.

## Command line

BagViewer.exe [file.bag] [--profile profile.json] [--theme dark|light] [--software-gl] [--3d] [--no-config]
BagViewer.exe file.bag --screenshot shot.png --time 30 [--zoom 20] [--center x,y]

## Development

python -m venv .venv
.venv\Scripts\pip install -r requirements.txt
set PYTHONPATH=src && .venv\Scripts\python -m bagviewer file.bag
.venv\Scripts\python -m pytest tests          # generates tests\data\synthetic.bag
powershell -ExecutionPolicy Bypass -File build.ps1   # -> dist\BagViewer.exe and dist\BagViewer_folder.zip

Code in src/bagviewer/: bag_loader.py (bag reading with rosbags), tf_buffer.py (interpolated TF
tree), displays/ (one file per display family), ui/main_window.py (main window),
glsetup.py (points PyOpenGL at Qt's OpenGL DLL).

## Visual identity

The interface follows the Djoka Technologies brand guide (DESIGN.md of the djoka-tech site): ink green
background #011109, panels #003E1C, borders #024E25, pale mint text #D9F3E4, mint highlights
#75BB99, rust #BB2D0C reserved for small logo accents. Inter font (bundled, SIL Open Font License 1.1,
see assets/fonts/README.txt). Colors, logo and Qt theme live in src/bagviewer/brand.py. The
assets/bagviewer.ico icon is rebuilt with tools/make_icon.py (Pillow).
The light theme uses the same tokens on a pale-mint surface (#F3FAF6, panels #D9F3E4, ink text
#011109, darker mint #2B6E50 for text accents so every text color keeps at least 4.5:1 contrast).

Each theme has its own categorical palette for plot curves, validated for its background (contrast, color
blindness); beyond 8 curves in one plot, colors repeat as dashed lines.

## Disclaimer

This software is provided "as is", without warranty of any kind, express or implied, including but not
limited to the warranties of merchantability, fitness for a particular purpose and noninfringement. In no
event shall Djoka Technologies be liable for any claim, damages or other liability arising from, out of or in
connection with the software or its use.

© 2026 Djoka Technologies
