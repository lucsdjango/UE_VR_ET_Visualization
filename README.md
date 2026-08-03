Unreal 5.7 project, based on the VR starter assets, with added support for logging, and visualizing VR with integrated eye-tracking data.

All eye-tracking additions (C++ classes, blueprints, editor widget, and Python scripts) are packaged as a self-contained plugin in `VRStart/Plugins/VRETLogger`, so they can be installed into other Unreal projects — see *Using the VRETLogger plugin in other projects* below and `VRStart/Plugins/VRETLogger/README.md`. The blueprints live in the plugin's content folder: enable **Settings > Show Plugin Content** in the Content Browser to see them under *VRETLogger Content*.

**Dependencies**:
- Unreal Engine 5.7
- [Varjo OpenXR plugin](https://fab.com/s/5761f1f00efa) or [Unreal Meta XR plugin](https://developers.meta.com/horizon/downloads/package/unreal-engine-5-integration/71.0). 

**Usage**:
- Open project.
- Import / build level.
- Add TSVLogger object to level.
- Add VRLogger object to level.
- Set the Logger field on VRLogger to the TSVLogger instance.
- Add VRETLogger object to level.
- Set VRLogger field on VRETLogger to VRLogger instance.
- Set file prefix to desired log file name.
- Wear headset with eyetracking enabled (tested on Varjo).
- Set DebugSphere > Rendering > Visible to show/hide indication of gaze vectors intersection with level geometry.
- Run Level in VRPreview mode.
- Inspect log file in /Content/VRETLogs/

**Visualization of fixations**:
- Run FixationsVisualizationWidgetBlueprint, found under *VRETLogger Content* in the content browser. (Rightclick and select run in content browser)
- On first run, click Install Requirements to install required python libraries.
- Check that the name of the latest log file is in the Input Log File entry field.
- Click Calculate Fixations. Calls python library taken from [Llanes-Jurado et al. (2020)](https://www.mdpi.com/1424-8220/20/17/4956). Refer to paper for explanation of parameters.
- Wait!
- Check that a new file has been created under /Content/VRETLogs/withCalculatedFixations/, and its name is in the Processed Log File entry field.
- Click Visualize Fixations.
- Note that an ETVisualization object with blue spheres representing fixations as children appears in the level.
- Scrub the slider to replay the movement of the headset, gaze intersection point, and fixations.
- Pressing Play (best in non-VR mode) will also replay the replay the movement and fixations in 1:1 time.
  

**Components**:
- VRETLogger
  - Blueprint class that checks that tracking is enabled, starts logging after a specified delay, and updates tracking data to log every tick.
  - Supports logging more data (focal depth, stereo gaze, pupil diameter, eyeblinks) than is currently exposed by the VarjoXR plugin.
- VRLogger
  - C++ class that registers tracking variables (columns) to log, updates values and checks for gaze vector intersections in the scene by calls to public function UpdateETData.
- TSVLogger
  - C++ class that handles logging of data organized in columns (variables) and rows (ticks) as tab separated text files.
  - Can log custom varibles (besides the tracking data) by calling AddEntry() (before StartLogging() is called in VRETLogging) and then one of the Update functions at every tick. All these functions are callable from blueprints. For an example, see LogTest blueprint.
- FixationsVisualizationWidgetBlueprint / ETVisualization / Fixation
  - BLueprint classes that control the visualization of recorded data. 

All of the above (plus the VRSpectator desktop-view pawn and the Python scripts) live in the VRETLogger plugin: C++ in `VRStart/Plugins/VRETLogger/Source`, blueprints in the plugin content folder, Python in `VRStart/Plugins/VRETLogger/Content/Python`.

**Using the VRETLogger plugin in other projects**:
- Copy the `VRStart/Plugins/VRETLogger` folder into the other project's `Plugins/` folder (create it if needed).
- Open the project and rebuild the plugin module when prompted (needs a C++ toolchain, see VS Code setup below), or use a prebuilt package so no compiler is required.
- Enable the plugin in Edit > Plugins if it is not enabled automatically. The Python Editor Script Plugin and Editor Scripting Utilities are pulled in automatically as dependencies.
- Full installation and usage instructions are in `VRStart/Plugins/VRETLogger/README.md`.

**Setting up UE 5.7 with Visual studio Code**:
- Needed to work with C++ classes in Unreal Engine.
  - [Follow steps in UE official documentation](https://dev.epicgames.com/documentation/en-us/unreal-engine/setting-up-visual-studio-code-for-unreal-engine).
    - When installing "Build Tools for Visual Studio", check "Desktop Development with C++" and ".NET desktop build tools".
  - In UE
    - Disable LiveCoding in Editor Preferences
    - Close UE editor when first compiling.
  - In Visual Studio Code
    - Set Solution configuration (dropdown in toolbar) to Development editor.
    - Build/rebuild by pressing F5.

**Using Varjo OpenXR plugin**:
  - Can be installed via [Unreal Engine’s FAB plugin browser](https://fab.com/s/5761f1f00efa).
  - Enable OpenXR in Varjo Base.
  - For further documentation, check out [Varjo Devloper Portal](https://developer.varjo.com/docs/unreal/ue5/unreal5). Future updates will (according to Varjo) provide more extensive eyetracking data.

**Using Meta Quest Pro (via link cable or AirLink)**:
  - Setup Meta Quest Link app on PC, and connect to Quest Pro Headset.
  - In [Meta Quest Link app](https://www.meta.com/en-gb/help/quest/1517439565442928/)
    - Set Meta Quest Link as OpenXR runtime.
    - Enable Eyetracking over Link in Settings > Beta.
  - Download [Unreal Meta XR plugin](https://developers.meta.com/horizon/downloads/package/unreal-engine-5-integration/71.0). 
  - In UE
    - Enable Meta XR plugin and restart.
    - In Project Settings > Plugins > Meta XR, set XR API To Epic Native OpenXR
    - In Project Settings > Plugins > OpenXR, disable Foveated Rendering
    - (May require a couple of restarts of headset and UE to work.)

**Renaming Unreal Project**:
  - Perhaps you want to rename or create a new copy of the Unreal Project. This is a bit complicated with Projects containing C++ source code.
  - [Check out guide here](https://unrealistic.dev/posts/rename-your-project-including-code).

**References**:
  - Llanes-Jurado, J.; Marín-Morales, J.; Guixeres, J.; Alcañiz, M. Development and Calibration of an Eye-Tracking Fixation Identification Algorithm for Immersive Virtual Reality. Sensors 2020, 20, 4956
