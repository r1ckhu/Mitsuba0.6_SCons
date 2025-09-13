# Compiling Mitsuba 0.6 (r1ckhu's fork) on Windows

This guide provides instructions for compiling the `scons-python3` branch of Mitsuba 0.6 in a Windows environment using Visual Studio (MSVC) and SCons.

## 1. Prerequisites

Before you begin, ensure you have the following software installed on your system:

*   **Git:** For cloning the repositories.
*   **Visual Studio 2017 or newer:** Make sure to include the **"Desktop development with C++"** workload during installation. This guide uses Visual Studio 2022 as an example.
*   **Python 3.x:** Required by the SCons build system. Ensure that you check the **"Add Python to PATH"** option during installation.
*   **SCons:** A Python-based build tool, which can be installed via pip.

## 2. Environment Setup

### Step 1: Get the Source Code and Dependencies

First, clone the Mitsuba 0.6 source code repository and the pre-compiled dependencies:

```bash
# Clone the main application repository
git clone https://github.com/r1ckhu/Mitsuba0.6_SCons.git --branch scons-python3

# Clone the dependencies repository
git clone https://github.com/r1ckhu/dependencies_win64.git
```

Next, rename the `dependencies_win64` folder to `dependencies` and move it inside the `Mitsuba0.6_SCons` directory. Your final directory structure should look like this:

```
Mitsuba0.6_SCons/
|-- dependencies/
|-- src/
|-- ... (other files and folders)
```

### Step 2: Install SCons

Open a terminal (Command Prompt or PowerShell) and install SCons using pip. This guide has been tested with Python 3.12 and SCons 4.9.1.

```bash
pip install scons
```

### Step 3: Configure Environment Variables

For the build tools to be found by the system, you must add the following paths to your system's `Path` environment variable.

1.  **MSVC Compiler Path**:
    *   This path points to the location of the C++ compiler (`cl.exe`).
    *   The format is typically: `[Path to Visual Studio]\VC\Tools\MSVC\[Version]\bin\Hostx64\x64`
    *   **Example**: `F:\VS_2022_Community\VC\Tools\MSVC\14.40.33807\bin\Hostx64\x64`

2.  **Visual Studio IDE Path**:
    *   This path may be required by some build tools.
    *   The format is typically: `[Path to Visual Studio]\Common7\IDE`
    *   **Example**: `F:\VS_2022_Community\Common7\IDE`

3.  **Python Scripts Path**:
    *   This is where `scons.exe` is located.
    *   The format is typically: `[Path to Python]\Scripts`
    *   **Example**: `C:\Users\YourUser\AppData\Local\Programs\Python\Python312\Scripts`

**How to Verify**
Open a **new** terminal window and type the following command:

```bash
cl
```

If you see output similar to the following (version numbers may differ), the MSVC compiler is configured correctly:

```
> cl
Microsoft (R) C/C++ Optimizing Compiler Version 19.40.33811 for x64
Copyright (C) Microsoft Corporation.  All rights reserved.

usage: cl [ option... ] filename... [ /link linkoption... ]
```

## 3. Compile Mitsuba

Choose one of the following two methods to compile the project.

### Method A: Using the Visual Studio Solution

1.  Navigate to the `Mitsuba0.6_SCons/build` directory.
2.  Open the `mitsuba-msvc2017.sln` file with Visual Studio.
3.  Build the project.

> **Note**: If you encounter **error 9009** during the build, it means that `scons.exe` was not found in your system's `Path` environment variable. Please return to **Step 2.3** to verify your configuration.

### Method B: Using the SCons Command Line

1.  From the `build/` directory, choose a configuration file based on your desired build type:
    *   `config-win64-msvc2017.py` (for a Release build)
    *   `config-win64-msvc2017-debug.py` (for a Debug build)
2.  Copy your chosen `.py` file to the root directory of `Mitsuba0.6_SCons` and rename it to `config.py`.
3.  Open a terminal in the `Mitsuba0.6_SCons` root directory and run SCons:

    ```bash
    scons
    ```
4.  You may encounter the following warning during compilation:
    ```
    scons: warning: MSVC version '15.0' was not found.
      Visual Studio C/C++ compilers may not be set correctly.
      Installed versions are: ['14.3']
    ```
    This occurs because the MSVC version specified in the config file does not match your installed version.
5.  Open the `config.py` file, find the `MSVC_VERSION` field, and change its value to the version number shown in the terminal prompt (e.g., `'14.3'` in the example above).
6.  Save the file and run the `scons` command again to complete the compilation.

## 4. Run Mitsuba

After a successful build, the executables will be located in the `Mitsuba0.6_SCons/dist/` directory. Before you can run `mtsgui.exe`, you must copy over the necessary runtime dependencies.

1.  Copy all `.dll` files from `Mitsuba0.6_SCons/dependencies/bin/` to `Mitsuba0.6_SCons/dist/`. If prompted, choose to **overwrite** the existing files.
2.  Copy the **entire contents** of the `Mitsuba0.6_SCons/dependencies/dist/` directory (the `platforms` folder) and merge them into the `Mitsuba0.6_SCons/dist/` directory.

After completing these steps, you should be able to launch Mitsuba by running `mtsgui.exe` from the `Mitsuba0.6_SCons/dist/` directory.