# How to get Cmake and Make to Work Natively in Windows 10

**Important Note: You will need to run most commands as an Administrator in the Command Prompt. Windows is pretty strict.**

## :one: Install Chocolately

[Official Instructions](https://chocolatey.org/install)

### Why? We need a package manager

Linux has apt-get by default
```
sudo apt-get <packageName>
```

Mac has Homebrew
```
brew install <packageName>
```

Windows has Chocolately
```
choco install <packageName>
```

## :two: Recommend you install Windows Terminal and Powershell

[Official Windows Terminal Instructions](https://docs.microsoft.com/en-us/windows/terminal/install)

The default Command Prompt is shit. :poop: Please stop using it on your personal machine. It's like trying to build a house without power tools. We have technology.

[Official Powershell Instructions](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell-on-windows?view=powershell-7.1)

Set your default shell to Powershell. Don't use "Powershell Core"... that's a weird Microsoft project to try to get Linux and Mac users to switch to Powershell.

## :three: Install the GCC Compiler (aka. MinGW)

**Note: You need to be running Terminal as an Administrator in order to do choco install.**

MinGW-64 is the Windows equivalent of GCC. It even comes with the gdb debugger! :heart_eyes:

```
choco install mingw
```

## :four: Install Cmake and Make

```
choco install cmake
```

```
choco install make
```

Add `cmake` as a command in your Environment Variables. There is a bug in the installer and it doesn't automatically create a command alias for you.

1. Search for `Environment Variables` in Windows
2. Click on `Environment Variables` button
3. In the upper window (user variables) click on `New`
4. Variable name = `cmake`
5. Variable path = `C:\Program Files\CMake\bin`
    - Please verify this path on your own computer
    - You will know this is the correct bin folder if it contains `cmake.exe`
6. Click `OK` and `OK` to finish

Restart your Terminal and run as Administrator to refresh the background variables and commands.

## :five: Download and Install SFML

[SFML Downloads Page](https://www.sfml-dev.org/download/sfml/2.5.1/)

1. Download GCC 7.3.0 MinGW (DW2) - 32-bit
    - Place the folder somewhere safe like `C:\Users\yourname\Documents\`.
    - We don't want the Visual C++ versions.
    - The 32-bit version worked on my Windows 10 64-bit computer.

2. Download all files from this Github repository: https://github.com/jeanmimib/sfml-mingw64 
3. In the terminal, go into this /sfml-mingw64 folder
4. Type this command. It will create a nupkg file.
    ```
    choco pack
    ```
5. Type this command. It will install SFML using the GCC (MinGW). The dot means "here", and since you're in the same folder as the nupkg file, choco will find it.
    ```
    choco install sfml-mingw64 -s .
    ```

Restart your Terminal and run as Administrator to refresh the background variables and commands.

## :six: Configure your IDE

1. I use Visual Studio Code, so you will need to find the equivalent in your IDE.
2. Install the C/C++ extension (by Microsoft) because we want all the coding power we can get.
3. Open up your project.
4. Open up the command palette with `ctrl + shift + p`.
5. Type `edit configuration`.
6. Select `edit configuration (ui)`.
7. Change the following configurations:

    | Configuration Name | Setting                                        |
    |--------------------|------------------------------------------------|
    | Compiler Path      | C:/ProgramData/chocolatey/bin/gcc.exe          |
    | IntelliSense mode  | windows-gcc-x64                                |
    | Include Path       | ${workspaceFolder}/**                          |
    |                    | C:\Users\yourname\Documents\SFML-2.5.1\include |
    | C++ standard       | C++ standard                                   |

8. Here we have 2 include paths. (1) The project's include and (2) SFML's include. If you eventually have more external libraries, add them in "Include Path" as a new line.
9. Your IDE IntelliSense should now be happy. The red lines should go away because now it can find the .hpp files.

## :seven: Write your CMakeLists.txt

We are not going to use any absolute file paths. This usually leads to errors because the compiler can't find stuff.

```
cmake_minimum_required(VERSION 3.10)

project(
    App             # Name of our application
    VERSION 1.0     # Version of our software
    LANGUAGES CXX)  # Language that we are using

set(CMAKE_CXX_STANDARD 17)

include_directories("./include/")

find_package(SFML 2.5.1 COMPONENTS graphics window system REQUIRED)

add_executable(${PROJECT_NAME} ./src/App.cpp ./src/Draw.cpp ./src/Command.cpp ./src/main.cpp)

target_link_libraries(${PROJECT_NAME} sfml-graphics sfml-window sfml-system)
```

## :eight: Copy/paste all the MinGW-64 .dll files into your project `/bin` folder.
  - For some reason, `make` cannot automatically find the GCC compiler's .dll files.
  - So lets copy/paste all the .dll files in our project `/bin` folder.
  - My GCC .dll files were here: `C:\ProgramData\chocolatey\lib\mingw\tools\install\mingw64\bin`

## :nine: Build your Project

  - Go into your project `/bin` folder
  - Type `cmake ..`
  - Type `make`
  - Pray that it works :pray:
  - Try running the `App.exe`
