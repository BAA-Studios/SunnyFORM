# SunnyFORM

SunnyFORM is an experimental CastFORM Windows Installer project.

CastFORM v1.0.0 through v2.0.1 was built via iExpress and while this was serviceable, it was also clearly lacking many creature comforts like the ability to uninstall (rather than manually deleting the install folder).  
SunnyFORM aims to improve upon this by leveraging on Windows Installer to provide an installation experience that is more aligned with the expectations of users.

At the moment, WiX v4 is still very new, and its documentationa are still in development. 
As such, this repository currently aims to adapt their presently available documentation for Flutter projects as our MVP. 
As FireGiant puts out new documentation, we will revisit and continue to refine this repository to provide more comprehensive instructions, 
as well as a more pleasing UI.

You may find the official tutorial [here](https://www.firegiant.com/docs/wix/tutorial/), and the official docs [here](https://wixtoolset.org/docs/intro/).

## A rose by any other name would smell as sweet

SunnyFORM is designed as a part of the toolchain for CastFORM, a Pokémon registration sheet filler.  
Being a form-automation application based around Pokémon TCG, CastFORM is a play on words using the name of one of the playable Pokémon.  
This project inherits its name from Castform's forms, which changes depending on the weather: under harsh sunlight, Castform will be in its Sunny Form and be a Fire-type Pokémon.

## Detours

Although we initially explored the possibility of using the installer creation template from the *Microsoft Visual Studio Installer Projects* extension, 
we were unable to get it working due to our relative inexperience with production C++ nor building Flutter via Visual Studio. 
It didn't help that this doesn't seem to be a particularly popular approach, and we could not find good documentation for this. 
Next, we considered using Windows Installer, which is what the Flutter team at Google recommends (if not using Microsoft Store). 
Microsoft's own documentation for this, while extensive, is also very difficult to read through and has minimal follow-along instructions (at the time of writing). 
It reads more like developer docs, and turned out too difficult to get right into. Searching around, we did find [this 75 page guide](https://www.itninja.com/static/c94ee3e6f937bcb62a8451cb94a0a206.pdf) 
written by ITNinja for an earlier version of Windows Installer - which is really great and much more digestable than Microsoft's official documentation - but unfortunately 
was still a bit much for us, considering our extremely small team size and product size.

In view of this, we opted for a more user-friendly option: the [WiX Toolset](https://github.com/wixtoolset/wix) (aka `Windows Installer XML`).
WiX Toolset is an open source set of build tools for building Windows Installer packages. 
It's still Windows Installer (and/or MSBuild) under the hood, but with many extensions provided to automate some of the more tedious parts of Windows Installer package creation, 
and exposing functionality in text form via XML. While the learning curve is supposedly steeper than with [Inno Setup](https://jrsoftware.org/isinfo.php), 
we would like to give WiX a shot since it's able to generate `.msi` bundles (Inno Setup produces `.exe` installers instead).

Taking inspiration from [this repository on WiX v3](https://github.com/zonble/flutter_wix_installer_example) by Weizhong Yang that we chanced upon during our research phase, 
we decided to continually update this README with instructions as we progress with this project, 
in hopes of providing more documentation for other members of the open source community whom wish to also deploy Flutter applications using WiX.

## Tech Stack

Our test bench is set up with the following dependencies:  
- Visual Studio Community 2022 v17.8.1
- .NET SDK 8 (via Visual Studio)
- HeatWave for VS2022 v1.0.2 (Visual Studio extension)
  - This is the name on the Visual Studio marketplace
  - FireGiant which produces both WiX and HeatWave calls this version `HeatWave Community Edition` instead
- WiX Toolset v4 (HeatWave obtains this from nuget - no installation needed)

WiX supports a number of interfaces including CLI - we decided to go with the Visual Studio approach for dealing with WiX, so we grabbed the HeatWave extension.

At the time of writing, we are letting HeatWave and WiX generate GUIDs instead of managing them manually. 
Should we want to micromanage this in the future, we will probably use the `New-Guid` PowerShell cmdlet to generate new GUIDs.

## How To Use SunnyFORM

1. Run `flutter build windows`, and note CastFORM version
2. Clone the repository, and open in Visual Studio. Set the build configuration to x64 (required for Flutter).
3. In the top toolbar go to `Project > Properties > Build`, and modify the absolute paths for Fluttter build output and Visual C++ Redistributable libraries directories
3. Navigate to `Package.wxs`, and under the `Package`tag, update the version number attribute
4. Check if the the folder structure in `CastFORM/build/windows/runner/Release` has changed, and update `Folders.wxs` accordingly
5. Check if the file structure has changed, and update `AppComponents.wxs` accordingly
6. In the top toolbar got to `Build > Build Solution`
7. Go to `Installer/bin/x64/Release/en-US` to find `CastFORM_Installer_x64.msi` and upload that to the CastFORM releases page

## How To Use HeatWave for Flutter Projects

Note: These instructions are written for HeatWave v1.0.2, WiX v4.0.3 which are the latest versions at the time of writing.
Note2: These instructions assume system-wide installation, rather than user-only installation since the latter does not currently have documentation from FireGiant at the moment.

1. Open Visual Studio and create a new project. Set the build configuration to x64 (required for Flutter).
    - Select `WiX` under the languages list, and then the `MSI Package (WiX v4)` temmplate
    - After clicking `Next`, set the project name and location and check the option to use directory for project and solution
      - If you don't check that last option, you'll end up with a folder structure like so: `.../solution/project/`, 
which may be suitable for large projects but unlikely to be necessary for our intents and purposes
    - After this you can hit `Create`
2. In the top toolbar go to `Project > Properties > Build`, and under `Preprocessor variables` set any user-defined variables you'd like to use
    - Refer to our `Installer.wixproj` file for an example
    - We use it to house the absolute paths for Fluttter build output and Visual C++ Redistributable libraries directories
3. `Package.wxs` should already be open for you. Modify the `Package` tag with the attributes you'd like to see in the installed app
    - E.g. `Name` is how it should be displayed in the Start Menu after installation
    - Version should be in the format `major.minor.build`, with the first 2 numbers being 0-255, and the build number being 0-65535
    - Upgrade code is a GUID that lets Windows Installer identify different version of the same software
4. Nest the following line within the `Package` tags (see ours in the `headless` branch for reference): `<MediaTemplate EmbedCab="yes"/>`
    - This tells WiX to bundle the cabinet files inside of the `.msi` so that you only distribute one file as the installer
5. Create a new `Feature` tag to enclose the existing template feature; create another one in the same level as the along side `Main`. Feel free to name the IDs as you see fit (see ours in the `headless` branch for reference).
    - The feature tag should now be a tree with one parent and two children
    - We are repurposing the feature tag provided for the main application, and creating one in the same level for Visual C++ redistributables
6. Duplicate `ComponentGroupRef` tag for the other feature with a suitable ID for Visual C++ Redustributable libraries; similarly duplicate `ExampleComponents.wxs` and rename IDs to waht you used in the corresponding `ComponentGroupRef` ID
    - See `Package.wxs` and `VRredist.wxs` in ours in the `headless` branch for reference
7. Use the `ComponentGroupRef` tag to create one component group reference for each folder you'd like to write files to (non-recursive)
    - See `Package.wxs` and `AppComponents.wxs` in ours in the `headless` branch for reference
8. Modify `INSTALLFOLDER` in `Folders.wxs` to suit your preferred install location
    - Supplying `ProgramFiles6432Folder` for `StandardDirectory` simply means to use either `C:\Program Files (x86)` or `C:\Program Files` as appropriate depending on build target
    - The template created by HeatWave concatenates company name and product name to make one folder; we split this up into a nested folder structure for our use case
9. Specify additional folders nested under `INSTALLFOLDER` according to your Flutter build
    - Refer to `build/windows/runner/Release` after running `flutter build windows` in your Flutter project
10. Flesh out application component groups - see our `AppComponents.wxs` in the `headless` branch for reference
    - To use preprocessor variables, see step 2
11. Repeat for Visual C++ Redustributable libraries
    - See our `VRredist.wxs` for reference
12. In the top toolbar got to `Build > Build Solution`
13. Check the output and test that it installs your program correctly

The steps outlined above will generate a non-GUI `.msi` installer that you can distribute. 
Once you have confirmed that you can get it working, you might want to start setting up a GUI inteface for configuration. 
For a set-up GUI during the installation process, WiX has an extension called [WixUI dialog library](https://wixtoolset.org/docs/tools/wixext/wixui/). 
You may also refer to either the source code in our `main` branch, or [Rob Mensching's VOD](https://www.youtube.com/live/-1-72Py0GSM?si=gCTOtuOEt7KDDJvG) to see it in action.

## Disclaimer

**SunnyFORM** is an open-source project for creating [CastFORM](https://github.com/BAA-Studios/CastFORM) installers for the Windows platform.  
**SunnyFORM** is part of the developer toolchain created for [CastFORM](https://github.com/BAA-Studios/CastFORM).  
**SunnyFORM** is non-monetised, and provided as is. Every reasonable effort has been taken to ensure correctness and reliability of **Weather Ball**. 
We will not be liable for any special, direct, indirect, or consequential damages or any damages whatsoever resulting from 
loss of use, data or profits, whether in an action if contract, negligence or other tortious action, arising out of or in connection with the use of **SunnyFORM** (in part or in whole).