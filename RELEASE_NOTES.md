# BagViewer release notes

## 1.9.3 — 2026-11-27

### Fixed
- **Live robot**: the SSH connection was closed by BagViewer itself right after it succeeded (closing the
  *Connecting…* window counted as *Cancel*), so the relay stopped about one second later and the session
  ended with *Connection closed* after choosing the topics. This was the real cause of the problem
  reported in 1.9.1 and 1.9.2, with or without Docker.
- **Live topics…** (add or remove topics during a session) showed the topics known at the connection: topics
  started on the robot since then were missing. It now asks the robot for its current list first.

### Improved
- Live topics can be changed with **Ctrl+Shift+L** or the **Topics…** button in the status bar, besides
  *File > Live topics…*.

## 1.9.2 — 2026-11-27

### Fixed
- **Live robot**, ROS installed on the robot itself (no Docker): the relay could miss the ROS environment set
  up in `~/.bashrc` (on Ubuntu, `~/.bashrc` does nothing in a non-interactive shell), and then talk to
  another ROS master (only a few topics) or stop at once. The relay now starts in the same environment as
  a terminal on the robot.

### New
- Connect dialog: optional **ROS setup** commands, run on the robot before the relay (e.g.
  `source ~/catkin_ws/devel/setup.bash`, `export ROS_MASTER_URI=…`), remembered per robot.
- The topics dialog shows the ROS master the robot answered from, and warns when it has very few topics.
- Clearer reasons when the relay stops (its messages and exit status are written in `bagviewer.log`).

## 1.9.1 — 2026-11-27

### Fixed
- **Live robot**: the connection closed (*Connection closed*) right after choosing the topics when the
  choice took more than 30 s: the relay on the robot thought BagViewer was gone. BagViewer now keeps the
  relay informed from the start, and when the relay stops, the reason is shown (and written in
  `bagviewer.log`).

## 1.9.0 — 2026-11-27

### New
- **Live robot**: *File > Connect to a robot (live)…* (Ctrl+L) shows the data of a running robot, like a bag
  that grows: displays, TF, plots, Messages and Inspector. BagViewer connects with SSH and starts a small
  relay in the ROS Docker container (found automatically), so it works with a ROS master on 127.0.0.1.
  Nothing is installed on the robot.
  - Choice of the topics, with a maximum rate each (laser scans, point clouds and maps are throttled by
    default, images are off), remembered per robot.
  - **LIVE** button and **L** key: follow the latest data; pause to look back in the buffer (the last
    10 minutes by default).
  - *File > Save live buffer as .bag…* keeps what was received.
  - Login with an SSH key file protected by a passphrase (OpenSSH or PEM, with its OpenSSH certificate if
    any), a password, or the SSH agent. The password / passphrase can be remembered (encrypted for the
    Windows account); the robot's SSH key is checked.
- *File > Simulate a live robot with a bag (test)…* to try the live mode without a robot.

## 1.8.2 — 2026-11-26

### Fixed
- BagViewer did not start on Windows Server 2016 and Windows 10 version 1607 (build 14393): *DLL load failed
  while importing QtCore: The specified procedure could not be found*. Qt 6.8 needs a Windows function that
  this build lacks, so BagViewer is now built with Qt 6.7. It runs on Windows 10 1607 or later, Windows 11
  and Windows Server 2016 or later.

### Notes
- Versions 1.8.1 and older do not start on these systems, so they cannot update themselves: copy 1.8.2
  over them once by hand.
- Windows 7 and 8.1 are still not supported.

## 1.8.1 — 2026-11-26

### Fixed
- BagViewer did not start on Windows 10 versions older than 1703 (e.g. Windows 10 LTSB 2016): *DLL load
  failed while importing QtCore*. It is now built with Qt 6.8 (LTS) and runs on every Windows 10 and 11.

### Notes
- Windows 7 and 8.1 are still not supported.

## 1.8.0 — 2026-11-25

### New
- **Point clouds** (`sensor_msgs/PointCloud2`), e.g. `/accerion/map`, the signature map of the Accerion
  sensor: one colour per cluster (`cluster_id`) and the heading of the signatures (`th`) as arrows. Any
  cloud: colour by a field (categories, rainbow, or the `rgb` colours of the cloud) or flat, heading arrows
  from a field, point size, alpha, decay time. Point cloud topics are displayed automatically.

## 1.7.1 — 2026-11-24

### Fixed
- **Map image**: the map is sharp when zooming in. Big images were shown at a reduced size and smoothed,
  hence blurry. Now an overview is shown at once, and the visible area is drawn from the full resolution
  image, pixel for pixel, at the level of detail of the zoom (read in the background in a few seconds,
  1 byte per pixel for a grey map). *Full resolution when zoomed* replaces *Max size*.

## 1.7.0 — 2026-11-24

### New
- **Map image (background map)**: *File > Load map image (background)…*, *Add… > Map image*, or drop an
  image into the window. Shows a picture of the site (PNG, JPEG, PGM, BMP, TIFF, or a `map_server` `.yaml`)
  under the scene: laser scans, trajectories and robots are drawn on top of it.
- **Align the image by clicking points**: a point of the image, then where it really is (e.g. on the laser
  scans). Two pairs of points set the position, rotation and scale; one pair only moves the image. The
  resolution, origin and rotation can also be typed in, or read from a `map_server` `.yaml`.
- Options: transparent background (the grey of unknown space is hidden), inverted colours, alpha. Very big
  images (e.g. 17000 x 9000 px) are loaded at a reduced size in about 2 s.

## 1.6.0 — 2026-11-17

### New
- **Compare two bags in the plots**: *File > Open comparison bag (B)…* (Ctrl+Shift+B, or Shift + drop a
  `.bag`). Every plotted field is also drawn for the second bag, same colour, dashed, with both values in the
  legend.
- **Align the two bags** from the *Compare* bar above the plots:
  - *B offset* to slide the B curves by hand;
  - *Align starts*, to compare two runs of a mission;
  - *Same clock*, for consecutive or overlapping logs of the same robot;
  - *Auto-align on* a curve, which finds the offset where both curves match best (cross-correlation).

  The offset is remembered for each pair of bags, and *cursor in B* shows the B time at the cursor.
- Command line: `--compare other.bag`.

## 1.5.6 — 2026-11-04

### Fixed
- An undocked panel is now a normal Windows window: it can be maximized, snapped to the side of a screen
  (drag to an edge, or Win + arrows) and has its own taskbar button. Its **Dock back** button is at its top
  right; closing the window closes the panel, and its toolbar button reopens it in its window.

## 1.5.5 — 2026-11-04

### New
- **Undock a panel into its own window**: the Plots, Messages, Inspector, VDA 5050 and Displays panels have an
  **Undock** button. The panel opens in a separate window (on the other screen when there is one) that can be
  resized and maximized, so you can watch e.g. the plots on one screen and the map on the other. The window
  follows playback and the keyboard shortcuts (Space, arrows, `[` `]`…) work in it. **Dock back** puts the
  panel back into the main window; undocked panels reopen in their window at the next start.

## 1.5.4 — 2026-11-04

### Faster
- **Plots and field lists**: listing the fields of a topic is now instant (it used to take about a second
  per topic on big bags), and plotting a field takes a fraction of a second instead of several seconds
  (e.g. 16,000 `std_msgs/Bool` messages: 3 s → 0.03 s; `nav_msgs/Odometry`, 88 fields: 1.5 s → 0.15 s).
- **Inspector**: opening a topic no longer reads the bag file again.
- **Opening a bag** is about twice as fast (4.5 s → 1.9 s on an 80 MB lz4 bag with 400 topics), with
  every topic kept in memory instead of only the displayed ones.
- **Playback** redraws faster (about 30 % less time per frame on a bag with 50 displays), and long plot
  curves are decimated to the screen resolution.

## 1.5.3 — 2026-10-20

### New
- VDA Order display: **Show the base (released)** checkbox, next to *Show the horizon (not released)*, to
  draw the released part only, the not released part only, or both.
- The action labels say whether their node / edge is released: `dock · WAITING · released`,
  `drop · not released`.

## 1.5.2 — 2026-10-20

### Fixed
- VDA Order display: the actions of an edge are labelled at the centre of the edge (halfway along its
  length) instead of on its end node.

## 1.5.1 — 2026-10-20

### Fixed
- VDA Order display: the actions of a node are written one per line instead of on top of each other, and
  nodes at the same place share one label. Each action shows its status from the State
  (e.g. `dock · WAITING`), and edge actions are now labelled at the middle of their edge.

## 1.5.0 — 2026-10-20

### New
- **VDA 5050 support** for `vda5050_msgs` topics:
  - **VDA Order** display: nodes and edges of the order (NURBS trajectories), base / horizon, progress
    from the State topic (done, current, to do) and node actions;
  - **VDA State** display: AGV pose, deviation range, mode / battery / errors label and remaining route;
  - **VDA 5050 panel** (Ctrl+5): order progress, action states, active errors and instant actions, in sync
    with playback;
  - VDA errors, orders and instant actions marked on the timeline.
- The frame of the VDA positions is found automatically (where `agvPosition` matches the robot).
- The VDA Order display warns when edge trajectories are far from their nodes, and can draw straight edges
  instead.

## 1.4.0 — 2026-10-15

### New
- **TF tree with a checkbox per frame**: select the TF display to see the TF tree of the bag
  (parent → child) and choose which frames are drawn. Each frame shows its publishing rate (or *static*)
  and the node that publishes it. *Show all* / *Hide all*, and right click to show, hide or show only a
  subtree. Saved with the settings and the profiles.

### Changed
- The TF display's regex filter is now called *Extra filter (regex)*; it still applies on top of the tree.

## 1.3.0 — 2026-10-14

### New
- **Inspector panel** (Ctrl+I): every field of a topic's message at the playback time, like `rostopic echo`
  in sync with playback. Works for every topic of the bag; previous / next message; copy a value, a field
  path or the whole message. Right click a topic or a display > *Inspect messages*.
- **Measure tool** (M): click two points in the view to read the distance, heading and dx / dy. The status
  bar also shows the coordinates of the mouse in the Fixed Frame.
- **Events on the timeline**: `/rosout` warnings and errors are marked on the timeline; hover to read them,
  click to jump, Ctrl + ← / → for the previous / next one.
- **Bookmarks** (B): named marks on the timeline, remembered for each bag.
- **Start-end window** (`[` and `]`, or right click on the timeline): playback, Home / End and *Loop* stay
  between the two bounds, the plots are framed on them, and the window is remembered for each bag.

## 1.2.0 — 2026-10-14

### New
- **Automatic updates**: at startup, BagViewer checks GitHub for a newer release. If there is one, a window
  shows its release notes and offers to download and install it; BagViewer then restarts on the new
  version, with the same bag open. *Later* asks again next time, *Skip this version* stays quiet until
  the following release.
- **Help > Check for updates…** to check at any time, and **Help > Check for updates at startup** to turn
  the automatic check off.

### Notes
- The update check is BagViewer's only network access; bag data never leaves the computer.
- The download is verified (size and SHA-256) before the new version replaces the old one.
- If the folder of `BagViewer.exe` cannot be modified, the new version is saved to *Downloads* instead.
- Versions 1.1.1 and older have no automatic update: install 1.2.0 by hand once.

## 1.1.1 — 2026-10-14

### Fixed
- Plots panel: expanding a field group in the topic list (e.g. `/odom` > `pose`) no longer shows an
  "Unexpected error" (*too many values to unpack*).

## 1.1.0 — 2026-10-11

### New
- **Messages panel**: read `/rosout`, `/rosout_agg` and `std_msgs/String` topics like rqt_console. Filter by
  level, topic or text; the list follows playback; double-click a message to jump to that moment.
- **Plots panel**: plot any int, float or bool field of any topic, on several linked plots. Includes values
  computed from TF (`x`, `y`, `yaw`, `speed`, `angular_speed` of any frame). Drag the cursor or double-click
  to jump in time.
- **Profiles**: save the displays, plots and filters to a `.json` file and reload them on any bag
  (Profile menu, or `--profile` on the command line).
- **Light and dark themes** (View > Theme), light by default.
- **Djoka Technologies branding**: new icon, welcome screen, About dialog, file properties.

### Changed
- The interface is now in English.
- Ctrl+S now saves the active profile (the automatic settings are still saved on exit).
- TF plot fields are named `speed` and `angular_speed`.

### Fixed
- The color picker no longer takes the chosen color as its own background.
- No more "missing in this bag" warning when opening another bag without a profile.

### Notes
- This software is provided "as is", without warranty of any kind (see Help > About BagViewer).
- Settings saved by 1.0.0 are kept; display options saved with French names fall back to their default.

## 1.0.0 — 2026-10-08

First release: portable `BagViewer.exe` for ROS1 bags (Kinetic and later), RViz-like 2D top view
(optional 3D), playback timeline, TF, laser scans, maps and costmaps, paths, odometry and poses,
footprints and markers. Compressed bags (bz2, lz4) supported; software rendering when no graphics card
is available.
