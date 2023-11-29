# SunnyFORM

SunnyFORM is an experimental CastFORM Windows Installer project.

CastFORM v1.0.0 through v2.0.1 was built via iExpress and while this was serviceable, it was also clearly lacking many creature comforts like the ability to uninstall (rather than manually deleting the install folder).  
SunnyFORM aims to improve upon this by leveraging on Windows Installer to provide an installation experience that is more aligned with the expectations of users.

At the moment, WiX v4 is still very new, and its documentations are still in development. 
As such, this repository currently aims to adapt their presently available documentation for Flutter projects as our MVP. 
As FireGiant puts out new documentation, we will revisit and continue to refine this repository to provide more comprehensive instructions, 
as well as a more pleasing UI.

You may find the official tutorial [here](https://www.firegiant.com/docs/wix/tutorial/), and the official docs [here](https://wixtoolset.org/docs/intro/).

## A rose by any other name would smell as sweet

SunnyFORM is designed as a part of the toolchain for CastFORM, a Pokémon registration sheet filler.  
Being a form-automation application based around Pokémon TCG, CastFORM is a play on words using the name of one of the playable Pokémon.  
This project inherits its name from Castform's forms, which changes depending on the weather: under harsh sunlight, Castform will be in its Sunny Form and be a Fire-type Pokémon.

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

## Usage Guides

### How To Use SunnyFORM

1. Run `flutter build windows`, and note CastFORM version
2. Clone the repository, and open in Visual Studio. Set the build configuration to x64 (required for Flutter).
3. In the top toolbar go to `Project > Properties > Build`, and modify the absolute paths for Fluttter build output and Visual C++ Redistributable libraries directories
3. Navigate to `Package.wxs`, and under the `Package`tag, update the version number attribute
4. Check if the the folder structure in `CastFORM/build/windows/runner/Release` has changed, and update `Folders.wxs` accordingly
5. Check if the file structure has changed, and update `AppComponents.wxs` accordingly
6. In the top toolbar got to `Build > Build Solution`
7. Go to `Installer/bin/x64/Release/en-US` to find `CastFORM_Installer_x64.msi` and upload that to the CastFORM releases page

### How To Use HeatWave for Flutter Projects

This section has been migrated to [its own repository](https://github.com/KOOKIIEStudios/WiX-v4-for-Flutter).

### Common Errors and Gotchas

This section has been migrated to [its own repository](https://github.com/KOOKIIEStudios/WiX-v4-for-Flutter).

## Disclaimer

**SunnyFORM** is an open-source project for creating [CastFORM](https://github.com/BAA-Studios/CastFORM) installers for the Windows platform.  
**SunnyFORM** is part of the developer toolchain created for [CastFORM](https://github.com/BAA-Studios/CastFORM).  
**SunnyFORM** is non-monetised, and provided as is. Every reasonable effort has been taken to ensure correctness and reliability of **Weather Ball**. 
We will not be liable for any special, direct, indirect, or consequential damages or any damages whatsoever resulting from 
loss of use, data or profits, whether in an action if contract, negligence or other tortious action, arising out of or in connection with the use of **SunnyFORM** (in part or in whole).