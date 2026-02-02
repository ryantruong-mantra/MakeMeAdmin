# Building MakeMeAdmin for Windows ARM64

This document describes how to build MakeMeAdmin for Windows on ARM (ARM64) architecture.

## Prerequisites

- Visual Studio 2019 or later with the following workloads:
  - .NET desktop development
  - Desktop development with C++
- Windows SDK
- MSBuild

## Supported Platforms

MakeMeAdmin now supports three platforms:
- **x86** (32-bit Intel/AMD)
- **x64** (64-bit Intel/AMD)
- **ARM64** (64-bit ARM) - New!

## Building Locally

### Using Visual Studio

1. Open `MakeMeAdmin.sln` in Visual Studio
2. Select the configuration and platform from the dropdown:
   - Configuration: `Debug` or `Release`
   - Platform: `x86`, `x64`, or `ARM64`
3. Build the solution (F7 or Build > Build Solution)

### Using MSBuild Command Line

For ARM64 Release build:

```cmd
# Restore NuGet packages
nuget restore MakeMeAdmin.sln

# Build the entire solution for ARM64 (includes MSI installer)
msbuild MakeMeAdmin.sln /p:Configuration=Release /p:Platform=ARM64

# Or build individual projects
msbuild Service/Service.csproj /p:Configuration=Release /p:Platform=ARM64
msbuild UserRequestApp/LocalUI.csproj /p:Configuration=Release /p:Platform=ARM64
msbuild RemoteUI/RemoteUI.csproj /p:Configuration=Release /p:Platform=ARM64

# Build just the MSI installer (after building the projects above)
msbuild Setup/Setup.wixproj /p:Configuration=Release /p:Platform=ARM64
```

**Note**: Building the MSI installer requires the WiX Toolset v3.11 to be installed.

## GitHub Actions Automated Builds

This repository includes a GitHub Actions workflow (`.github/workflows/arm64-build.yml`) that automatically builds the ARM64 MSI installer.

### Triggering the Build

The ARM64 build workflow is triggered:
- On push to `main` or `master` branches
- On pull requests to `main` or `master` branches
- Manually via workflow_dispatch

### Downloading Build Artifacts

1. Go to the **Actions** tab in the GitHub repository
2. Click on the latest successful workflow run
3. Scroll down to the **Artifacts** section
4. Download `MakeMeAdmin-ARM64-Installer` - Contains the MSI installer file

The MSI installer includes all components:
- MakeMeAdmin Service
- Local UI
- Remote UI
- All dependencies and localization files

## Output Locations

Build artifacts are placed in:
- `Service/bin/Release ARM64/` - Service binaries
- `UserRequestApp/bin/Release ARM64/` - Local UI binaries
- `RemoteUI/bin/Release ARM64/` - Remote UI binaries
- `Setup/bin/Release ARM64/` - MSI installer (named `MakeMeAdmin-2.4.1-ARM64.msi`)
- `Installers/` - Copy of MSI installer (created by post-build event)

## Dependencies

All NuGet packages used in this project support ARM64:
- Microsoft.Diagnostics.Tracing.TraceEvent
- Microsoft.Diagnostics.Tracing.EventSource
- Microsoft.Win32.Registry
- SyslogNet.Client
- System.Runtime.CompilerServices.Unsafe

## Code Compatibility

The codebase uses standard .NET Framework APIs and P/Invoke calls that are platform-agnostic. No code changes were needed for ARM64 support - only build configuration updates.

## Testing

To test the ARM64 build:
1. Download the ARM64 artifacts from GitHub Actions
2. Deploy to a Windows on ARM device (e.g., Surface Pro X, Windows Dev Kit 2023)
3. Install and run the service as you would on x64 Windows

## Troubleshooting

### Build Errors

If you encounter build errors:
1. Ensure you have the latest Windows SDK installed
2. Verify MSBuild can find the ARM64 toolchain
3. Check that all NuGet packages are restored correctly

### Runtime Issues

If the application doesn't run on ARM64:
1. Verify you're running on a Windows on ARM device
2. Check that all dependencies are present in the output directory
3. Ensure you're using the ARM64 build, not x64 (which would run through emulation)

## Additional Resources

- [Windows on ARM documentation](https://learn.microsoft.com/en-us/windows/arm/)
- [ARM64 .NET Framework support](https://learn.microsoft.com/en-us/dotnet/framework/migration-guide/versions-and-dependencies)
