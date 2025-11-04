# z64_debugging
How to debug OoT and MM in the ares emulator with decomp and gdb

## Setup:
1. Clone the "oot" or "mm" repository as normal, following the instructions at https://github.com/zeldaret/oot/blob/main/README.md or https://github.com/zeldaret/mm/blob/main/README.md. I suggest cloning in linux/wsl rather than windows, to prevent build errors.  
2. (Optional) I recommend moving the repository to your windows directory because if the repository is on WSL, and I open it in VSCode, it is very slow to search.  
3. Build the game and make sure you get "OK" when building.  
4. Now that the build is complete, you need to make some changes to the makefile to build it with gcc instead of ido. This is required for gdb debugging.  
	- Find this line: "COMPILER ?= ido" and replace it with "COMPILER ?= gcc"  
	- In OoT, replace "OPTFLAGS := -Os -ffast-math -fno-unsafe-math-optimizations" with "OPTFLAGS := -Os -ggdb -ffast-math -fno-unsafe-math-optimizations"
	- In MM, replace "OPTFLAGS         := -Os -ffast-math -ftrapping-math -fno-associative-math" with "OPTFLAGS         := -Os -ggdb -ffast-math -ftrapping-math -fno-associative-math"  
	- In OoT, you may also want to change "VERSION ?= gc-eu-mq-dbg" to "VERSION ?= ntsc-1.0" or some other version.  
3. Copy the ".vscode" folder and the "gdb_z64overlay_load_auto.py" file from this repository to the corresponding game's home directory.  
4. Download Ares (https://ares-emu.net/download) and install it.  
5. Open Ares and go to Settings -> Debug. Enable the debugger and set port 9123 (you could use another port, but this needs to match the value in ".vscode/launch.json").  

## How to start debugging
1. Open the game's home folder ("oot/" or "mm/") in vscode.  
2. Launch the game in Ares (it may ask you to open an N64DD file after the main rom, but just hit cancel), and it should say "GDB listening" at the bottom.  
3. In VSCode, click Run -> Start Debugging. You will have to wait several seconds but eventually vscode should hit a breakpoint and Ares should be paused and say "GDB connected".  
4. Now VSCode is set up to debug the game's main code, but not the dynamically allocated overlays. We need to run a special python script so that VSCode knows when overlays are loaded in the game, and at which address they are loaded. This will allow us to put breakpoints on overlay files and step through their code. In VSCode, at the bottom, go to "DBEUG CONSOLE" and input this command: ```-exec source gdb_z64overlay_load_auto.py```. Note that the debugger must be at a breakpoint in order for this command to work.  
5. Load a new area in the game. The script will automatically update the overlays when they are loaded or unloaded ingame, so after starting the script, you will need to enter a new area for it to know where the overlays are loaded.
6. You can play the game by going in VsCode and clicking Run -> Continue. Everything is set up now. You should be able to add breakpoints, step through the code, add watches, and change memory values. Good luck debugging!  
