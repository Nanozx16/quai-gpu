# quai-gpu

quai-gpu-miner is an ProgPoW GPU mining worker: with quai-gpu-miner you can mine Quai, which relies on an ProgPoW-based Proof of Work thus including Ethereum ProgPoW and others. This is the actively maintained version of quai-gpu-miner. It originates from the ethminer project. Check the original ProgPoW implementation and EIP-1057 for specification.

Features
OpenCL mining
Nvidia CUDA mining
realistic benchmarking against arbitrary epoch/DAG/blocknumber
on-GPU DAG generation (no more DAG files on disk)
stratum mining without proxy
OpenCL devices picking
farm failover (getwork + stratum)
Ethereum-based ProgPoW implementations supported only, doesn't support previous ethash version or Bitcoin-based forks.
Table of Contents
Install
Usage
Examples connecting to pools
Build
Continuous Integration and development builds
Building from source
Maintainers & Authors
Contribute
F.A.Q.
Install
Releases

Standalone executables for Linux, macOS and Windows are provided in the Releases section. Download an archive for your operating system and unpack the content to a place accessible from command line. The quai-gpu-miner is ready to go.

Builds	Release
Last	GitHub release
If you have trouble with missing .dll or CUDA errors, please install the latest version of CUDA drivers or report to project maintainers.

Usage
The quai-gpu-miner is a command line program. This means you launch it either from a Windows command prompt or Linux console, or create shortcuts to predefined command lines using a Linux Bash script or Windows batch/cmd file. For a full list of available command, please run:

quai-gpu-miner --help
Examples connecting to pools
Connecting to MinerMore Testnet:

./quai-gpu-miner -P stratum+tcp://<wallet>.worker@rvnt.minermore.com:4505 or

quai-gpu-miner.exe -P stratum+tcp://<wallet>.worker@rvnt.minermore.com:4505

Build
After cloning this repository into quai-gpu-miner, it can be built with commands like:

Ubuntu / OSX
cd quai-gpu-miner
git submodule update --init --recursive
mkdir build
cd build
cmake .. -DETHASHCUDA=ON -DETHASHCL=ON -DAPICORE=ON
make -sj $(nproc)
Windows
Prerequisites:
Install Visual Studios (2019) (with the additional installation package "C++ Cmake Tools for Windows)
Install latest perl to C:\Perl (https://www.perl.org/get.html) Follow the steps outlined and the default perl installtion should work
Building via Visual Studios Command Line:
Open "Developer Command Prompt for VS 2019"

Open StartMenu and search for "Developer Command Prompt for VS 2019"
Follow these steps:
cd C:\Users\USER_NAME\PATH_TO_KAWPOW\quai-gpu-miner
git submodule update --init --recursive
mkdir build
cd build
cmake -G "Visual Studio 16 2019" -A X64 -H. -Bbuild -DETHASHCL=ON -DETHASHCUDA=ON -DAPICORE=ON ..
cd build
cmake --build . --config Release
(Yes, two nested build/build directories.)

Building via Visual Studios GUI (This build doesn't seem to work for some 20XX Nvidia cards)
Open Visual Studios
Open CMakeLists.txt file with File->Open->CMake
Wait for intelligence to build the cache (this can take some time)
Build the project (CTRL+SHIFT+B) or find the build command in the menu
ProgPoW can be tuned using the following parameters. The proposed settings have been tuned for a range of existing, commodity GPUs:

PROGPOW_PERIOD: Number of blocks before changing the random program
PROGPOW_LANES: The number of parallel lanes that coordinate to calculate a single hash instance
PROGPOW_REGS: The register file usage size
PROGPOW_DAG_LOADS: Number of uint32 loads from the DAG per lane
PROGPOW_CACHE_BYTES: The size of the cache
PROGPOW_CNT_DAG: The number of DAG accesses, defined as the outer loop of the algorithm (64 is the same as Ethash)
PROGPOW_CNT_CACHE: The number of cache accesses per loop
PROGPOW_CNT_MATH: The number of math operations per loop
The value of these parameters has been tweaked between version 0.9.2 (live on the Gangnam testnet) and 0.9.3 (proposed for Ethereum adoption). See this medium post for details.

Parameter	0.9.2	0.9.3	KAWPOW
PROGPOW_PERIOD	50	10	3
PROGPOW_LANES	16	16	16
PROGPOW_REGS	32	32	32
PROGPOW_DAG_LOADS	4	4	4
PROGPOW_CACHE_BYTES	16x1024	16x1024	16x1024
PROGPOW_CNT_DAG	64	64	64
PROGPOW_CNT_CACHE	12	11	11
PROGPOW_CNT_MATH	20	18	18
KAWPOW_EPOCH_LENGTH = 7500

Maintainers & Authors
Discord

The list of current and past maintainers, authors and contributors to the quai-gpu-miner project. Ordered alphabetically. Contributors statistics since 2015-08-20.

Name	Contact	
Jeremy Anderson	@Blondfrogs	---
Traysi	@traysi	--
Andrea Lanfranchi	@AndreaLanfranchi	ETH: 0xa7e593bde6b5900262cf94e4d75fb040f7ff4727
EoD	@EoD	
Genoil	@Genoil	
goobur	@goobur	
Marius van der Wijden	@MariusVanDerWijden	ETH: 0x57d22b967c9dc64e5577f37edf1514c2d8985099
Paweł Bylica	@chfast	ETH: 0x8FB24C5b5a75887b429d886DBb57fd053D4CF3a2
Philipp Andreas	@smurfy	
Stefan Oberhumer	@StefanOberhumer	
ifdefelse	@ifdefelse	
Won-Kyu Park	@hackmod	ETH: 0x89307cb2fa6b9c571ab0d7408ab191a2fbefae0a
Ikmyeong Na	@naikmyeong	
Contribute
All bug reports, pull requests and code reviews are very much welcome.

License
Licensed under the GNU General Public License, Version 3.

F.A.Q
Why is my hashrate with Nvidia cards on Windows 10 so low?
The new WDDM 2.x driver on Windows 10 uses a different way of addressing the GPU. This is good for a lot of things, but not for ETH mining.

For Kepler GPUs: I actually don't know. Please let me know what works best for good old Kepler.
For Maxwell 1 GPUs: Unfortunately the issue is a bit more serious on the GTX750Ti, already causing suboptimal performance on Win7 and Linux. Apparently about 4MH/s can still be reached on Linux, which, depending on ETH price, could still be profitable, considering the relatively low power draw.
For Maxwell 2 GPUs: There is a way of mining ETH at Win7/8/Linux speeds on Win10, by downgrading the GPU driver to a Win7 one (350.12 recommended) and using a build that was created using CUDA 6.5.
For Pascal GPUs: You have to use the latest WDDM 2.1 compatible drivers in combination with Windows 10 Anniversary edition in order to get the full potential of your Pascal GPU.
Why is a GTX 1080 slower than a GTX 1070?
Because of the GDDR5X memory, which can't be fully utilized for ETH mining (yet).

Are AMD cards also affected by slowdowns with increasing DAG size?
Only GCN 1.0 GPUs (78x0, 79x0, 270, 280), but in a different way. You'll see that on each new epoch (30K blocks), the hashrate will go down a little bit.

Can I still mine ETH with my 2GB GPU?
Not really, your VRAM must be above the DAG size (Currently about 2.15 GB.) to get best performance. Without it severe hash loss will occur.

What are the optimal launch parameters?
The default parameters are fine in most scenario's (CUDA). For OpenCL it varies a bit more. Just play around with the numbers and use powers of 2. GPU's like powers of 2.

What does the --cuda-parallel-hash flag do?
@davilizh made improvements to the CUDA kernel hashing process and added this flag to allow changing the number of tasks it runs in parallel. These improvements were optimised for GTX 1060 GPUs which saw a large increase in hashrate, GTX 1070 and GTX 1080/Ti GPUs saw some, but less, improvement. The default value is 4 (which does not need to be set with the flag) and in most cases this will provide the best performance.

What is quai-gpu-miner's relationship with Genoil's fork?
Genoil's fork was the original source of this version, but as Genoil is no longer consistently maintaining that fork it became almost impossible for developers to get new code merged there. In the interests of progressing development without waiting for reviews this fork should be considered the active one and Genoil's as legacy code.

Can I CPU Mine?
No, use geth, the go program made for ethereum by ethereum.

CUDA GPU order changes sometimes. What can I do?
There is an environment var CUDA_DEVICE_ORDER which tells the Nvidia CUDA driver how to enumerates the graphic cards. The following values are valid:

FASTEST_FIRST (Default) - causes CUDA to guess which device is fastest using a simple heuristic.
PCI_BUS_ID - orders devices by PCI bus ID in ascending order.
To prevent some unwanted changes in the order of your CUDA devices you might set the environment variable to PCI_BUS_ID. This can be done with one of the 2 ways:

Linux:

Adapt the /etc/environment file and add a line CUDA_DEVICE_ORDER=PCI_BUS_ID
Adapt your start script launching quai-gpu-miner and add a line export CUDA_DEVICE_ORDER=PCI_BUS_ID
Windows:

Adapt your environment using the control panel (just search setting environment windows control panel using your favorite search engine)
Adapt your start (.bat) file launching quai-gpu-miner and add a line set CUDA_DEVICE_ORDER=PCI_BUS_ID or setx CUDA_DEVICE_ORDER PCI_BUS_ID. For more info about set see here, for more info about setx see here
nvrtc64_102_0.dll not found...
Error: The code execution cannot be processed because nvrtc64_102_0.dll was not found.
or
error while loading shared libraries: libnvrtc.so.10.2: cannot open shared object file: No such file or directory
You have to upgrade your Nvidia drivers. Install cuda 10.2.
