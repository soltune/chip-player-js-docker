# Chipplayer JS soltune fork - Docker

Lets' create your own [Chip Player JS (soltune's fork)](https://github.com/soltune/chip-player-js) music library easily by Docker!

## Preparation 

### Install Docker
First, install Docker Engine onto your host OS before proceeding to this instruction.

- Linux
    - [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)
    - [Debian](https://docs.docker.com/install/linux/docker-ce/debian/)
    - [Fedora](https://docs.docker.com/install/linux/docker-ce/fedora/)
    - [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
- OSX
    - [Docker Desktop for Mac](https://docs.docker.com/docker-for-mac/)
- Windows
    - [Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/)

As I personally don't have Windows, this project may not be work on it ...

### Install Git
Install Git or any good clients onto your host OS if you have not done yet.  
I suggest you to visit [GitHowTo](https://githowto.com/) if you're a newbie.

## How to Setup

### 1. Clone project
After cloning this repository, create www directory and firebaseConfig.js (from firebaseConfig.example.js) like this.

```sh
$ git clone https://github.com/soltune/chip-player-js-docker.git
$ cd chip-player-js-docker
$ mkdir www
$ cp ./build/firebaseConfig.sample.js ./build/firebaseConfig.js
```

### 2. Prepare Firebase account and Edit your settings **(Optional)**
Prepare [Firebase account](https://console.firebase.google.com/u/0/?hl=ja) to save your settings on the player.  
Although using Firebase is free, please note that Firebase requires domain name to be functional. 
So this step can be skipped if you run this project on your localhost(or your local network) only.

Just edit firebaseConfig.js in ./build/ directory with your favourite text editor.

### 3. Prepare rhythm files
S98, PMD and FMP require additional samples to play rhythm tracks.  
Just place the following files on ./build/rhythm/ directory. (each names are case sensitive!)

- 2608_BD.WAV
- 2608_HH.WAV
- 2608_RIM.WAV
- 2608_SD.WAV
- 2608_TOM.WAV
- 2608_TOP.WAV
- ym2608_adpcm_rom.bin

### 4. Prepare additional soundfonts **(optional)**
By adding soundfonts into ./build/soundfonts/ directory improve sound quality for midi files.
Available .sf2 files can be found at [MIDIPlayer.js](https://github.com/soltune/chip-player-js/blob/develop/src/players/MIDIPlayer.js#L10).

### 5. Place music files
Put chiptune files into ./catalog directory to create your own music library!  
The player supports the following file types;

| ext |                       summary                       |
|-----|-----------------------------------------------------|
| ay  | Amstrad CPC, ZX Spectrum                            |
| sgc | Coleco Vision, Game Gear, Sega Master System        |
| nsf, nsfe | NES, Famicom. VRC6/VRC7/MMC5/FDS/N163 multichip supported|
| gbs | Gameboy                                             |
| spc | Super Nintendo, Super Famicom                       |
| hes | TG-16, PC-Engine                                    |
| kss | MSX, Sega Master System, Game Gear                  |
| vgm, vgz | Sound log taken from Sega Game Gear, Genesis, Mega Drive, Master System and many others |
| s98 | Sound log taken from Japanese PCs: NEC PC-9801, PC-8801, Sharp X1 and others. (version S98, S98V2 and S98V3)|
| mdx | Sound driver for X68000. Additional PCM(pdx) also supported. |
| m, m2, mz | PMD: Sound driver for PC-9801, PC-8801. Supported PCM files are: pvi, pzi, pps, ppc and p86 |
| opi, ovi, ozi | FMP: Sound driver for PC-9801. Supported PCM file is pvi. |
| psf, minipsf | Playstation                                |
| psf2, minipsf2 | Playstation 2                            |
| 2sf, mini2sf | Nintendo DS                                |
| mid, midi | Standard MIDI File                            |
| it  | Impulse Tracker Music Modules                       |
| mod | Amiga MODule file format (ProTracker, NoiseTracker) |
| s3m | ScreamTracker 3 Modules                             |
| xm  | FastTracker II Music Modules                        |
| v2m | V2 Synthesizer System                               |

This step also can be done later. Refer [Update Catalog](#Update Catalog) section for the details.

### 6. First build on Docker
Ok, build whole resources on Docker by launching build container on ./build/ directory;

```sh
cd ./build/ && docker-compose up
```

This step will take a few minutes(around 10 minutes on MBP 2013), so please be patient until the following message shown and return to prompt.

```sh
chipplayerjs-build_1  | Done in 141.81s.
build_chipplayerjs-build_1 exited with code 0
$  <- return to prompt
```

### 7. Launch Web Servers on Docker
Finally, let's launch both nginx and nodejs servers;

```sh
$ cd ../server/ && docker-compose up -d
```

To browse your library, open [http://localhost:5000/](http://localhost:5000/) with your browser.

## Administrations
### Launch Servers
When you restart your host OS you'll need relaunch the servers on Docker.

```sh
$ cd ./server/ && docker-compose up -d
```

### Stop Servers
To stop your servers, just like the following;

```sh
$ cd ./server/ && docker-compose down
```

### Update Chip Player JS and Your Catalog
The catalog must be updated when there're any changes(add/remove/modify) in ./catalog directory.
This procedure also update Chip Player JS itself.

```sh
$ cd ./build/ && docker-compose up && cd ../server/ && docker-compose restart
```

## Sample Music

- Fearofdark
    - [Hopeless Romantic (nsf)](https://fearofdark.bandcamp.com/album/the-coffee-zone)

- Pedipanol
    - [Another World - Smurfs' Nightmare (PMD)](https://soundcloud.com/pedipanol/pc-98-another-world-smurfs-nightmare)
    - [Adventuring with Onee-chan (PMD)](https://soundcloud.com/pedipanol/onee)

- w7n
    - [Himitsu Dolls (nsf)](https://famitracker.org/fcp2015/results-cover.html)
    - [Luka Luka Night Fever (nsf)](http://www.bilibili.com/video/av2279197/)
