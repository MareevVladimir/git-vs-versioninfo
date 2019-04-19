Git - Visual Studio VersionInfo Generator
=========================================

Overview
--------

This project uses an MS-DOS batch file to parse the output of `git-describe` and
generates a `.cs`-file to be included in a project's.
For building from source without having git the script can use a version file
or embedded default value (and marks the build as _unversioned_)

Usage
-----

This can be run from the command line.  It uses the following arguments:

    usage: [--help] | | [--force] [--quiet] CACHE_PATH OUTFILE ROOTNS PRJNAME SWNAME

    When called without arguments version information writes to console.

    --help     - displays this output.

    --quiet    - Supress console output.
    --force    - Ignore cached version information.
    CACHE_PATH - Path for non-tracked file to store git-describe version.
    OUTFILE    - Path to writable file that is included in the project.
    ROOTNS     - Project's root namespace
    PRJNAME       - Project name
    SWNAME     - Override auto-detected software name

    Version information is expected to be in the format: 
    vMajor[.Minor[.Maint[.Bugfix]]][-stage#][-Patchcount-Committish]
    ( where -stage# is alpha, beta, or rc. Example: v1.0.0-alpha0 )

    Example pre-build event:
    CALL $(SolutionDir)scripts\git-vs-versioninfo.bat "$(ProjectDir)" "$(ProjectDir)git-vs-versioninfo.cs" "$(RootNamespace)" "$(ProjectName)" "$(SolutionName)"


Notes
-----

Generated definitions:

  - `Short`  
    Git tag minus the leading `v` and trailing release markers.

  - `Build`  
    Full (git) version identifier.

  - `PatchCount`  
    Patches applied since last (tagged) version/release.

  - `Committish`  
    Git committish

  - `ReleaseType`  
    Description of the release
    (e.g. `Beta Release [n]`, `Release Candidate [n]`, etc.)
    
  - `BuildType`  
    Description of the build:
    - `Custom Build`  when there is no git(repo) available;
    - `Private Build` when there are changes since the last commit;
    - `Test Build` when there are commits between the last (tagged) release
      and the commit being built;
    - `Release Build` when none of the above is true.

  - `IsUnversioned`  
    Set when there is no version control active 
    (i.e. no git installed or not repo available).
    
  - `IsPrivate`  
    Set when HEAD is dirty (or this is a Custom Build).

  - `IsPatched`  
    Set when there are commits between the tag and the commit being built 
    (or this is a Custom Build).

  - `IsPreRelease`  
    Set when the tag includes `alpha`, `beta` or `rc`.

  - `Major` / `Minor` / `Maintenance` / `Bugfix` \
    Separate Version digits (if unavailable zero is used)

  - `Patches{Major/Minor/Maintenance/Bugfix}`  
    Commits since last (tagged) {Major/Minor/Maintenance/Bugfix} version/release.

  - `ProductVersion`  
    Product version
    (same as Version, but always in `Major.Minor.Maintenance.Bugfix` format).

  - `TechnicalVersion`  
    Version in format: `Major.Minor.Maintenance.PatchesMaintenance`
    (this can be configured in the script).

  - `PluginApi.Version`  
    Integer <n> as being parsed from git tag in the format: `plugin-api-v<n>`
