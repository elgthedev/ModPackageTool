# ModPackageTool

A tool for copying DLLs into plugins folders and creating packages for publishing (Nexus, Thunderstore).

## Features

The tool is a `.targets` file that automates the copy and zip tasks of mod files.

### Debug
- Generates .mdb symbols file (for debugging at runtime step by step)
- Copies DLL and artifacts to plugins folder

    ![image](https://user-images.githubusercontent.com/101152506/215940370-cdbb4e4b-ab16-446a-9f54-31ae4c5b4a62.png)

### Release
- Generates Nexus Package
- Generates Thunderstore Package

    ![image](https://user-images.githubusercontent.com/101152506/215941566-1b71c8e5-0613-47a4-914d-714026a9083d.png)

## Setup

1. Download or copy the <a href="https://github.com/elgthedev/ModPackageTool/blob/main/ModPackageTool.targets"  target="_blank">ModPackageTool.targets</a> content into a file in the same folder as your `.csproj`.
     > If you are not using ILRepack in your project, you can change all `AfterTargets="ILRepacker"` to `AfterTargets="Build"` in the `.targets` file.
2. Import the file into your `[YourModName].csproj` before the `</Project>` tag:
```xml
<Import Project="ModPackageTool.targets" Condition="Exists('ModPackageTool.targets')" />
```
3. Create the following variables in your `[YourModName].csproj.user` file, changing as necessary (see the example file: <a href="https://github.com/elgthedev/ModPackageTool/blob/main/YourMod.csproj.user"  target="_blank">YourMod.csproj.user</a>): 
```xml
<PropertyGroup>
  <GamePath>C:\Program Files (x86)\Steam\steamapps\common\Valheim</GamePath>
  <PluginsPath>$(GamePath)\BepInEx\plugins</PluginsPath><!-- Your plugins folder where the .dll/.mdb will be copied -->
  <Pdb2MdbPath>$(SolutionDir)\pdb2mdb.exe</Pdb2MdbPath><!-- Location of the pdb2mdb.exe file -->
</PropertyGroup>
```
  > * GamePath is not necessary if you use one of <a href="https://github.com/AzumattDev"  target="_blank">Azumatt's</a> templates or if you've already defined this variable in your `.csproj`.
  > * Pdb2MdbPath has the default value shown above but is not needed if you don't generate `.mdb` files to debug your mods for debugging at runtime or if you `pdb2mdb.exe` in your **solution directory**. 
  > * Plugins has the default value shown above, so you don't even need this variable if you don't use a package manager. You can set it to your bepinex/r2modman/thunderstore plugins location.
  4. Use the **Debug** or **Release** build in your IDE and watch the magic happen!

## Package Creation
The package is set to be created in the following structure:

    .
    ├── Project Directory        
    │   └── Publish              
    │       ├── Nexus              
    │       │   └── YourModName.dll     # This DLL will change with each release
    │       │   └── YourModName.zip     # Your auto-generated Nexus zip package
    │       └── Thunderstore
    │           └── README.md           # Thunderstore required readme auto-copied to this folder
    │           └── icon.png            # Thunderstore required mod icon, place this file here once
    │           └── manifest.json       # Thunderstore required manifest, create once
    │           └── YourModName.dll     # This DLL will change with each release
    │           └── YourModName.zip     # Your auto-generated Thunderstore zip package
    └── ...
 
 * You can run the **Release** build to generate the publish folder structure and place your Thunderstore files.
 * Place the `manifest.json` and `icon.png` in the Thunderstore location only once for use in creating the zip package.
 * The readme is auto-copied from the **Project directory** to the Thunderstore folder (or from the **Solution directory** if not found in the project directory) but can be changed in the `.targets` file as needed.

## Tips
* If collaborating on a project, consider ignoring the `.csproj.user` file in the repository as each developer will have their own. Create a `.csproj.user.example` for the variable structure.
If not using the DLLs in the repository for Github Actions or release tags, gitignore them as they will change with each release. The same goes for the README file if already committed in the Project directory. No need to commit the one in the Thunderstore folder as it will be auto-copied.
* When changing the `csproj.user` you may need to reload your project for new values to take effect.

## Did you like the tool?
Check out what's being developed here or buy me a coffee to support:
<p align="center">
  <a href="https://www.buymeacoffee.com/elgthedev">
    <img width="300" alt="bmc-button" src="https://user-images.githubusercontent.com/101152506/215227016-c5a9ea72-fe35-42bf-be89-06c643b46993.png">
  </a>
</p>

<p>
  <p align="center"><h2>For Questions or Suggestions find Elg in the Odin Plus Team on Discord:</h2></p>

  <p align="center"><a href="https://discord.gg/mbkPcvu9ax"><img src="https://i.imgur.com/Ji3u63C.png" alt="Odin Plus Discord"></a>
</p>

## FAQ

#### Do I need to use the same file name (ModPackageTool.targets)?

No, you can use any name, just make sure to keep the .targets extension and change the name in the import command in step 2.

#### How do I debug at runtime?

Refer to this page for information: <a href="https://github.com/elgthedev/ModPackageTool/blob/main/ModPackageTool.targets"  target="_blank">https://github.com/Valheim-Modding/Wiki/wiki/Debugging-Plugins-via-IDE</a>. No need to add the property group/post build shown on that page if using ModPackageTool.targets.

#### I don't want to debug at runtime, can I delete the CreateMdbFile task from the .targets?

The task will not affect other tasks even if it doesn't find the `pdb2mdb.exe` file, so keep or delete as desired.

#### Where can I find a working project that uses this tool?

A working example will be posted soon.