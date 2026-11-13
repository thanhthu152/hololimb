# HoloLimb – Getting started (for people cloning the repo)

Nov 13, 2026 · @ThuNT

## Requirements

You do not need to run the Mixed Reality Feature Tool: the MRTK and OpenXR packages are stored in the repo (Packages/MixedReality) and Unity loads them from there. You only need the right Unity version. Running the app on the PC needs no headset; a HoloLens 2 is needed only to deploy.

| Need | Version / note | Required for |
| --- | --- | --- |
| Windows 10 or 11 | 64-bit PC | Everything (UWP build support is Windows-only) |
| Git | Any recent version | Cloning the repo |
| Unity Hub + Unity **2022.3.62f3** | Exactly this version, with the **Universal Windows Platform Build Support** module | Opening and running the project |
| Visual Studio with Game development with Unity, Windows 11 SDK, MSVC ARM64 build tools | See "Build and deploy to HoloLens 2" | Building for the headset only |
| HoloLens 2 | Developer Mode on | Deploying only |

## Clone and open the project

1. Install Unity Hub, then in Hub > Installs add **2022.3.62f3** with the Universal Windows Platform Build Support module (Hub > Installs > the gear icon > Add modules, if it is already installed).
2. Clone the repo, using the repo's own URL:

```bash
git clone <repo-url>
```

3. In Unity Hub click **Add > Add project from disk** and select the cloned folder (the one that contains Assets, Packages and ProjectSettings).
4. Open it with **2022.3.62f3**. If Hub offers to upgrade to a different editor version, do not accept: install 2022.3.62f3 and choose that.
5. Wait for the first import. It takes several minutes because Unity rebuilds the Library folder, which is not stored in Git.
6. If Unity asks about the Input System backend, choose **Yes** (the editor restarts). MRTK3 needs the new Input System.

## First-run checks

A fresh clone can differ slightly from the original machine, so check these four things once.

1. **Platform.** File > Build Settings: select Universal Windows Platform, set Architecture = ARM 64-bit, Build Type = D3D Project, and click Switch Platform if it is not already active. Ignore the yellow warning about running ARM64 on this PC (the build is for HoloLens 2).
2. **MRTK profile.** Edit > Project Settings > MRTK3: on the first tab (Standalone) and the fourth tab (UWP), the Profile field must not be None. If it is, click **Assign MRTK Default**.
3. **Scene.** Open the Baseline scene from Assets/Scenes. The Hierarchy should contain MRTK XR Rig and MRTKInputSimulator, and no Main Camera.
4. **Play.** Click Clear in the Console, press Play and open the Game tab. The Console should show no red errors. If it reports "No profile is available", repeat check 2.

Press Play again to stop. Changes made while the editor is in Play mode are lost.

## Testing without the headset

The MRTK Input Simulator replaces real hand tracking in the Editor. Click inside the Game view first, then use:

| Action | Input |
| --- | --- |
| Show the right hand | Hold **Space** |
| Pinch (select / grab) | Hold the **left mouse button** while the hand is shown |
| Look around | Hold the right mouse button and move the mouse |

Only the first two controls were verified during setup. Other keys (movement, left hand) depend on the Input Simulator version; check the key hints MRTK shows in the Game view, or the Input Simulator object's settings, before relying on them.

## Build and deploy to HoloLens 2

Skip this section if you only test in the Editor. The build path has not been test-built yet at the time of writing, so treat the steps as a starting point and report any error.

1. **Visual Studio.** In Visual Studio Installer > Modify, tick the Game development with Unity and Desktop development with C++ workloads. Under Individual components add **MSVC Build Tools for ARM64/ARM64EC (Latest)**, **C++ Universal Windows Platform support for ARM64/ARM64EC build tools (Latest MSVC)** and a **Windows 11 SDK**. USB Device Connectivity is only needed for cable deployment.
2. **Build from Unity.** File > Build Settings > Add Open Scenes, then Build into a folder named Builds outside Assets (this folder is not committed to Git).
3. **Deploy.** Open the generated .sln file in Visual Studio, set the configuration to **Release**, the architecture to **ARM64** and the target to **Remote Machine** (Wi-Fi, enter the headset's IP address) or **Device** (USB), then run without debugging.
4. **Headset setup.** Settings > Update & Security > For developers: turn on Developer Mode and Device Portal. Keep the PC and headset on the same Wi-Fi network.

**If the build fails with MSB8020** ("build tools for v143 ... cannot be found"): Unity 2022.3.62f3 has a known issue (UUM-127114) when Visual Studio 2026 is the only Visual Studio installed. Install Visual Studio 2022 Community alongside, with the Universal Windows Platform development workload, and select it in the Visual Studio Version field of Build Settings. The alternative is upgrading Unity to 2022.3.70f1 or later, which the whole team must do together.

## Troubleshooting

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| Hub says the editor version is missing | 2022.3.62f3 not installed | Install exactly 2022.3.62f3 from Unity Hub (Installs > Install Editor > Archive) |
| Console: "MRTK Profile could not be retrieved" / "No profile is available", black Game view | MRTK3 Profile = None | Project Settings > MRTK3 > Assign MRTK Default on the Standalone and UWP tabs |
| Compile errors mentioning MixedReality or MRTK namespaces | Packages failed to load, or a different MRTK version | Check Packages/MixedReality contains the .tgz files and manifest.json points to them; delete the Library folder and reopen the project |
| Package Manager shows a package missing | The .tgz files were not cloned | Confirm .gitignore does not exclude \*.tgz or Packages/MixedReality, then pull again |
| No hands appear when pressing Space | Focus is not in the Game view, or MRTK did not start | Click inside the Game view; check the Console for red errors |
| No Universal Windows Platform module loaded | UWP module not installed | Unity Hub > Installs > Add modules > Universal Windows Platform Build Support, restart Unity |
| Red "Could not find any supported UWP SDK" in Build Settings | Windows SDK missing | Install a Windows SDK in Visual Studio Installer |
| Build error MSB8020 (v143) | Visual Studio 2026 only | See the MSB8020 note above |

## Rules for contributors

- **Do not change package versions.** The project uses MRTK 3.x: Core Definitions 3.2.2, Input 3.2.2, UX Core Scripts 3.2.2, Spatial Manipulation 3.3.1, UX Components 3.3.0, UX Components (Non-Canvas) 3.1.4, Standard Assets 3.2.0, plus Mixed Reality OpenXR Plugin 1.11.2. Never move to the 4.0.0-pre releases.
- **Do not rerun the Mixed Reality Feature Tool** unless the team agrees, because it rewrites manifest.json and the .tgz files.
- **Commit:** Assets (including MRTK.Generated, XR, XRI, Scenes), Packages (including manifest.json, packages-lock.json and the MixedReality folder) and ProjectSettings.
- **Do not commit:** Library, Temp, Obj, Builds, Logs, UserSettings, .vs.
- **Stay on Unity 2022.3.62f3.** Upgrade only when the whole team does it together, and then update this document.
- **Scope reminder:** HoloLimb is a rehabilitation-training prototype that records task performance. It makes no claim of clinical effect on stroke recovery.
