# OLED-Video-Library-Player
ESP32-S3 OLED video library with FFAT storage and button navigation and also video playback

# OLED Video Project

A compact ESP32-S3 OLED video player using a 1.3-inch 128×64 SH1106 OLED display.

The project converts videos into a custom binary format and plays them directly from the ESP32-S3's flash storage using FFAT.

## Hardware

* ESP32-S3 N16R8
* 1.3-inch SH1106 128×64 OLED
* Push button
* 16 MB flash
* FFAT storage

### OLED Connections

| OLED | ESP32-S3 |
| ---- | -------- |
| SDA  | GPIO 8   |
| SCL  | GPIO 9   |

### Button

* Button connected to GPIO 4
* Uses the ESP32 internal pull-up
* Button connects between GPIO 4 and GND

## Current Features

### Video Library

The Library scans FFAT for available `.bin` video files and displays them on the OLED.

Features:

* Automatically detects `.bin` video files
* Displays multiple videos in a scrollable list
* Supports up to 50 detected videos
* Shows 5 videos at a time
* Scrollbar when more than 5 videos are available
* `.bin` extension is hidden from the displayed filename
* Single click moves through the video list
* Double click opens the selected video
* Long press returns from the video player to the library

### Video Player

The Player reads the video information directly from each video's header.

Features:

* 128×64 OLED playback
* Dynamic FPS read from the video file
* Supports different video frame rates
* Tested with approximately 24.89 FPS, 29.97 FPS, 30 FPS and 60 FPS videos
* Pause and resume
* Restart video
* Long press to return to the library
* Automatically loops when the video reaches the end
* Uses FFAT flash storage
* Videos can be renamed without changing the player code
* Multiple videos can be stored and selected from the Library

## Video Format

Videos are converted into a custom `.bin` format.

Each file contains:

1. A 20-byte header
2. Raw 128×64 OLED frame data

Each frame uses:

* Resolution: 128×64
* Frame size: 1024 bytes
* OLED page-format data

The header stores:

* File identifier
* Width
* Height
* FPS
* Frame count
* Frame size

The Player reads these values automatically when opening a video.

## Video Conversion

The video converter is used separately to convert a normal video into the `.bin` format required by the ESP32-S3.

The general workflow is:

1. Select a video.
2. Run the converter.
3. The converter produces a `.bin` file.
4. Place the `.bin` file inside the Arduino sketch's `data` folder.
5. Upload the FFAT filesystem.
6. Upload/run the Player.
7. Select the video from the OLED Library.

The converter and ESP32 player are kept separate so that videos can be converted independently of the firmware.

## Controls

| Action                     | Function            |
| -------------------------- | ------------------- |
| Single click               | Move to next video  |
| Double click               | Open selected video |
| Single click while playing | Pause / resume      |
| Double click while playing | Restart video       |
| Long press                 | Return to library   |

The exact button behavior is handled by the custom button gesture logic in the Player.

## Storage

Videos are stored in the ESP32-S3's flash filesystem using FFAT.

The current partition configuration uses approximately 9–10 MB of storage for FFAT on the 16 MB flash.

The Player automatically scans the filesystem for files ending in `.bin`.

For example:

```text
video.bin
video1.bin
video2.bin
video3.bin
```

The `.bin` extension is used internally but is hidden from the OLED Library.

## Tested Playback

The Player has been tested with videos using different frame rates, including:

* ~24.89 FPS
* ~29.97 FPS
* 30 FPS
* 60 FPS

Longer videos have also been tested, including files several megabytes in size.

The Player uses the FPS stored in each video file instead of assuming a fixed frame rate.

## Project Components

The project currently contains separate components for:

### OLED Video Converter

Converts normal video files into the custom `.bin` format.

### OLED Video Library

Provides the video selection interface and scans FFAT for available videos.

### OLED Video Player

Handles video playback, timing, controls, looping, pause/resume and restarting.

## Current Workflow

The current system is intentionally simple and mostly manual:

**Video → Converter → `.bin` file → FFAT `data` folder → ESP32-S3 → OLED**

The ESP32-S3 does not need the original MP4/video file. It only needs the converted `.bin` file.

