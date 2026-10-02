# ESP32-C3 OLED Animations

A collection of monochrome OLED animations designed for the **ESP32-C3 Super Mini** and a **0.96-inch 128×64 SSD1306 I²C OLED display**.

The repository is designed to keep the project simple: every animation is an individual `.ino` file placed directly in the repository root, while this `README.md` contains the complete hardware, software, wiring, programming, animation, troubleshooting, and contribution documentation.

---

## 📌 Project Overview

This project demonstrates how an ESP32-C3 Super Mini can drive a 128×64 SSD1306 OLED and display frame-by-frame monochrome animations.

Animations are stored as bitmap frames in program memory and displayed sequentially on the OLED.

Example animations in this collection include:

- 🐱 **Scuba Cat**
- 🕷️ **Spider-Man**
- 🔴 **Red John**
- ➕ Additional animations can be added directly to the repository root

The goal is to provide a simple collection of ready-to-upload `.ino` animations that can be used with the same ESP32-C3 + SSD1306 hardware setup.

---

# 📁 Repository Structure

The repository intentionally uses a **flat structure**.

```text
ESP32-OLED-Animations/
│
├── README.md
├── Scuba_Cat.ino
├── SpiderMan.ino
├── Red_John.ino
├── Animation_04.ino
├── Animation_05.ino
├── ...
│
└── LICENSE
```

There are no separate folders required for individual animations.

Each animation can be opened directly in Arduino IDE.

---

# 🧰 Hardware Requirements

## Main Hardware

| Component | Specification |
|---|---|
| Microcontroller | ESP32-C3 Super Mini |
| Board version | V1.6.1 / V1601 |
| Display | 0.96-inch OLED |
| Resolution | 128 × 64 pixels |
| Display controller | SSD1306 |
| Communication | I²C |
| OLED address | `0x3C` |
| Logic voltage | 3.3 V |

The OLED used with this project is a **128×64 monochrome SSD1306 I²C display**.

---

# 🔌 Wiring

Use the following wiring for the ESP32-C3 Super Mini.

| OLED | ESP32-C3 Super Mini |
|---|---|
| GND | GND |
| VDD / VCC | 3V3 |
| SCL / SCK | GPIO 4 |
| SDA | GPIO 5 |

### Wiring Diagram

```text
       0.96" SSD1306 OLED
       ┌───────────────┐
       │               │
GND ───┤ GND           │
3V3 ───┤ VDD / VCC     │
GPIO4 ─┤ SCL / SCK     │
GPIO5 ─┤ SDA           │
       │               │
       └───────────────┘
              │
              │
       ESP32-C3 Super Mini
```

### Important

Do not interchange SDA and SCL.

This project uses:

```text
SDA = GPIO 5
SCL = GPIO 4
```

The Arduino code therefore initializes I²C with:

```cpp
Wire.begin(5, 4);
```

---

# 🖥️ OLED Configuration

The display configuration used throughout this project is:

```cpp
#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
#define SCREEN_ADDR 0x3C
```

The display object is:

```cpp
Adafruit_SSD1306 display(
    SCREEN_WIDTH,
    SCREEN_HEIGHT,
    &Wire,
    OLED_RESET
);
```

The display is initialized with:

```cpp
display.begin(
    SSD1306_SWITCHCAPVCC,
    SCREEN_ADDR
);
```

---

# 📦 Required Arduino Libraries

Install these libraries using the Arduino IDE Library Manager:

### 1. Adafruit GFX Library

Provides graphics functions such as:

- `drawBitmap()`
- `drawPixel()`
- text rendering
- shapes
- lines
- display graphics support

### 2. Adafruit SSD1306

Provides communication and control for SSD1306 OLED displays.

---

# 💻 Arduino IDE Setup

Recommended Arduino IDE configuration:

| Setting | Value |
|---|---|
| Board | `ESP32C3 Dev Module` |
| USB CDC On Boot | `Enabled` |
| Upload Speed | `921600` |
| Flash Mode | Default |
| Partition Scheme | Default |
| Port | ESP32-C3 COM port |

If uploading at `921600` is unreliable, try:

```text
460800
```

or:

```text
115200
```

---

# ⬇️ Installing the ESP32 Board Package

If ESP32 boards are not already installed:

1. Open Arduino IDE.
2. Open **File → Preferences**.
3. Add the Espressif ESP32 board manager URL if required.
4. Open **Tools → Board → Boards Manager**.
5. Search for:

```text
esp32
```

6. Install the ESP32 package provided by Espressif.
7. Select:

```text
ESP32C3 Dev Module
```

---

# 📚 Installing the Libraries

Open:

```text
Sketch
→ Include Library
→ Manage Libraries
```

Search for:

```text
Adafruit GFX Library
```

Install it.

Then search for:

```text
Adafruit SSD1306
```

Install it.

---

# 🚀 Uploading an Animation

## Step 1 — Connect the Hardware

Connect the OLED:

```text
OLED GND → ESP32 GND
OLED VDD → ESP32 3V3
OLED SCL → ESP32 GPIO4
OLED SDA → ESP32 GPIO5
```

## Step 2 — Connect USB

Connect the ESP32-C3 Super Mini to the computer.

## Step 3 — Select Board

Select:

```text
ESP32C3 Dev Module
```

## Step 4 — Select Port

Select the COM port corresponding to the ESP32-C3.

## Step 5 — Open an Animation

For example:

```text
Scuba_Cat.ino
```

## Step 6 — Verify

Click:

```text
Verify
```

## Step 7 — Upload

Click:

```text
Upload
```

If the board requires boot mode, follow the board's BOOT/RESET procedure while uploading.

---

# 🧪 Test the OLED Before Running Animations

If you are unsure whether the display is working, use this minimal test program:

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

Adafruit_SSD1306 oled(128, 64, &Wire, -1);

void setup() {
  Wire.begin(5, 4);

  oled.begin(SSD1306_SWITCHCAPVCC, 0x3C);

  oled.clearDisplay();
  oled.setTextSize(2);
  oled.setTextColor(WHITE);
  oled.setCursor(10, 25);
  oled.print("TEST");
  oled.display();
}

void loop() {
}
```

If the OLED displays:

```text
TEST
```

the basic wiring, I²C pins, display address, and SSD1306 communication are working.

---

# ⚙️ How the Animation System Works

The OLED has:

```text
128 × 64 pixels
```

Every pixel is either:

```text
ON
```

or:

```text
OFF
```

because the display is monochrome.

A complete frame therefore requires:

```text
128 × 64 / 8 = 1024 bytes
```

So one full-screen 128×64 monochrome bitmap requires:

```text
1024 bytes
```

or:

```text
1 KB
```

approximately.

---

# 🖼️ Bitmap Frames

A typical animation frame is stored like this:

```cpp
const uint8_t PROGMEM frame0[1024] = {
    // bitmap data
};
```

Another frame:

```cpp
const uint8_t PROGMEM frame1[1024] = {
    // bitmap data
};
```

The animation then stores pointers to the frames:

```cpp
const uint8_t* frames[] = {
    frame0,
    frame1,
    frame2,
    frame3
};
```

The current frame is selected using an index:

```cpp
frames[frameIdx]
```

---

# 💾 Why `PROGMEM` Is Used

The ESP32 has limited RAM compared with the total amount of animation data that can be stored in the program.

Using:

```cpp
PROGMEM
```

stores bitmap data in program/flash memory rather than unnecessarily occupying normal runtime RAM.

Example:

```cpp
const uint8_t PROGMEM frame0[1024] = {
    ...
};
```

This is especially useful when an animation contains many frames.

---

# 🖥️ Displaying a Frame

A frame can be displayed with:

```cpp
display.clearDisplay();

display.drawBitmap(
    0,
    0,
    frames[frameIdx],
    128,
    64,
    SSD1306_WHITE
);

display.display();
```

The coordinates:

```text
0, 0
```

place the bitmap at the top-left corner.

The bitmap dimensions are:

```text
128 × 64
```

which fills the entire OLED.

---

# 🔄 Animation Loop

The animation works by displaying one frame, waiting for the required interval, and then displaying the next frame.

A typical structure is:

```cpp
static uint8_t frameIdx = 0;
static unsigned long lastMs = 0;

if (millis() - lastMs >= 83) {
    lastMs = millis();

    display.clearDisplay();

    display.drawBitmap(
        0,
        0,
        frames[frameIdx],
        128,
        64,
        SSD1306_WHITE
    );

    display.display();

    frameIdx = (frameIdx + 1) % numFrames;
}
```

Using `millis()` instead of a long blocking `delay()` makes it easier to add other functionality later.

---

# ⏱️ Frame Rate

The animation speed is controlled by the frame interval.

For example:

```cpp
83 ms
```

between frames is approximately:

```text
1000 / 83 ≈ 12 FPS
```

A lower interval makes the animation faster.

Example:

```cpp
50 ms
```

is approximately:

```text
20 FPS
```

A higher interval makes the animation slower.

Example:

```cpp
100 ms
```

is approximately:

```text
10 FPS
```

---

# 🎬 Scuba Cat

## Animation

`Scuba_Cat.ino`

This animation displays the Scuba Cat-style monochrome OLED sequence.

The repository can contain either a manually/generated bitmap animation or a frame sequence extracted from a source animation, depending on the version of the `.ino` file being used.

### Generated Scuba Animation

The supplied generated animation code contains:

```text
106 frames
```

with:

```text
128 × 64
```

bitmap frames.

The animation timing in that generated code is:

```text
83 ms/frame
```

which corresponds to approximately:

```text
12 FPS
```

The frame sequence cycles continuously.

The generated source uses frames from:

```text
frame0
```

through:

```text
frame105
```

and displays them sequentially.

---

# 🕷️ Spider-Man

## Animation

`SpiderMan.ino`

The Spider-Man animation follows the same general architecture:

```text
ESP32-C3
   ↓
I²C
   ↓
SSD1306 OLED
   ↓
Bitmap Frame
   ↓
Next Bitmap Frame
   ↓
Repeat
```

The exact frame count and timing should be treated as properties of the current `.ino` file because different versions of an animation may contain different numbers of frames.

---

# 🔴 Red John

## Animation

`Red_John.ino`

The Red John animation uses the same SSD1306 bitmap rendering method.

It can be uploaded using the same hardware and Arduino IDE configuration described in this README.

No wiring changes are required between animations.

---

# ➕ Adding a New Animation

To add another animation:

1. Create a new Arduino sketch.
2. Make sure it uses the same OLED configuration.
3. Convert the animation into 128×64 monochrome bitmap frames.
4. Store the frames in `PROGMEM`.
5. Add the frames to the animation frame array.
6. Set the required frame interval.
7. Save the file directly in the repository root.

Example:

```text
ESP32-OLED-Animations/
├── README.md
├── Scuba_Cat.ino
├── SpiderMan.ino
├── Red_John.ino
└── My_New_Animation.ino
```

---

# 🧱 Basic Animation Template

A new animation can start with this structure:

```cpp
#include <Wire.h>
#include <Adafruit_GFX.h>
#include <Adafruit_SSD1306.h>

#define SCREEN_WIDTH 128
#define SCREEN_HEIGHT 64
#define OLED_RESET -1
#define SCREEN_ADDR 0x3C

Adafruit_SSD1306 display(
    SCREEN_WIDTH,
    SCREEN_HEIGHT,
    &Wire,
    OLED_RESET
);

const uint8_t PROGMEM frame0[1024] = {
    // 128x64 bitmap
};

const uint8_t PROGMEM frame1[1024] = {
    // 128x64 bitmap
};

const uint8_t* frames[] = {
    frame0,
    frame1
};

const uint8_t numFrames = 2;

void setup() {
    Serial.begin(115200);

    Wire.begin(5, 4);

    if (!display.begin(
        SSD1306_SWITCHCAPVCC,
        SCREEN_ADDR
    )) {
        Serial.println(F("SSD1306 allocation failed"));

        while (true) {
        }
    }

    display.setRotation(0);
    display.clearDisplay();
    display.display();
}

void loop() {
    static uint8_t frameIdx = 0;
    static unsigned long lastMs = 0;

    if (millis() - lastMs >= 83) {
        lastMs = millis();

        display.clearDisplay();

        display.drawBitmap(
            0,
            0,
            frames[frameIdx],
            SCREEN_WIDTH,
            SCREEN_HEIGHT,
            SSD1306_WHITE
        );

        display.display();

        frameIdx++;

        if (frameIdx >= numFrames) {
            frameIdx = 0;
        }
    }
}
```

Replace the example bitmap data with the actual frames.

---

# 🧮 Memory Considerations

Each complete 128×64 monochrome frame is:

```text
1024 bytes
```

Therefore:

```text
10 frames  ≈ 10 KB
50 frames  ≈ 50 KB
100 frames ≈ 100 KB
```

This is why large animations should use:

```cpp
PROGMEM
```

for their frame data.

The actual compiled program size will also include:

- Arduino framework
- Adafruit GFX
- Adafruit SSD1306
- animation code
- bitmap data
- other program resources

---

# 🎞️ Creating Bitmap Animations

A video or GIF can be converted into individual monochrome frames.

The general workflow is:

```text
Video / GIF
     ↓
Extract Frames
     ↓
Crop to OLED Content
     ↓
Resize to 128×64
     ↓
Convert to Monochrome
     ↓
Threshold / Clean Image
     ↓
Convert to C Bitmap Array
     ↓
Store in PROGMEM
     ↓
ESP32-C3
     ↓
SSD1306
```

For best results:

- Keep important artwork inside the 128×64 boundary.
- Use strong black/white contrast.
- Remove unnecessary background noise.
- Keep frame alignment consistent.
- Avoid unnecessary frames if flash usage becomes large.
- Choose a frame interval appropriate for the animation.

---

# 🧹 Improving Animation Quality

If an animation looks unstable or flickers:

### 1. Check frame alignment

Every frame should have exactly the same:

```text
128 × 64
```

dimensions and positioning.

### 2. Check thresholding

If converting from a grayscale/video source, an unsuitable threshold can remove important details.

### 3. Reduce noise

Small isolated pixels can make the OLED image look noisy.

### 4. Adjust frame rate

Try:

```cpp
50
```

```cpp
70
```

```cpp
83
```

or:

```cpp
100
```

milliseconds depending on the desired appearance.

### 5. Avoid unnecessary screen updates

Only update the OLED when the next animation frame is actually required.

---

# 🔧 Troubleshooting

## OLED Is Completely Blank

Check:

```text
GND → GND
VDD → 3V3
SCL → GPIO4
SDA → GPIO5
```

Then verify:

```cpp
Wire.begin(5, 4);
```

and:

```cpp
0x3C
```

---

## `SSD1306 allocation failed`

Check:

1. OLED power.
2. SDA/SCL wiring.
3. OLED I²C address.
4. SSD1306 library installation.
5. Correct display dimensions:

```cpp
128, 64
```

---

## OLED Works With Test Code but Animation Does Not

If the basic `TEST` program works, the hardware is probably communicating correctly.

Check:

- bitmap array declaration
- frame dimensions
- frame pointer array
- frame count
- animation loop
- flash/program size
- compilation output

---

## Wrong I²C Pins

This project uses:

```cpp
Wire.begin(5, 4);
```

That means:

```text
SDA = GPIO5
SCL = GPIO4
```

Do not use Arduino Uno's:

```text
A4 / A5
```

configuration directly on the ESP32-C3.

---

## Wrong I²C Address

This project uses:

```text
0x3C
```

If your specific OLED uses a different address, the address must be changed in:

```cpp
display.begin(
    SSD1306_SWITCHCAPVCC,
    0x3C
);
```

---

# 🔌 Upload Problems

If Arduino IDE cannot upload:

### Try these upload speeds

First:

```text
921600
```

If unreliable:

```text
460800
```

Then:

```text
115200
```

Also verify that the correct COM port is selected.

If required, use the board's BOOT and RESET controls to enter upload mode.

---

# 🖥️ Serial Monitor

The animations themselves do not require Serial Monitor.

Debug messages can be enabled with:

```cpp
Serial.begin(115200);
```

For example:

```cpp
Serial.println("OLED started");
```

If Serial output is unavailable or inconsistent, the OLED animation can still operate normally because OLED communication is independent of the Serial Monitor.

---

# 🔄 Changing Animation Speed

Find:

```cpp
if (millis() - lastMs >= 83)
```

Change `83` to another value.

Examples:

| Interval | Approx. FPS |
|---:|---:|
| 40 ms | 25 FPS |
| 50 ms | 20 FPS |
| 60 ms | 16.7 FPS |
| 70 ms | 14.3 FPS |
| 83 ms | 12 FPS |
| 100 ms | 10 FPS |
| 125 ms | 8 FPS |
| 200 ms | 5 FPS |

These values are approximate because actual display update time also contributes to the effective frame rate.

---

# 🔁 Changing the Number of Frames

If an animation has:

```cpp
frame0
frame1
frame2
frame3
```

the frame array can be:

```cpp
const uint8_t* frames[] = {
    frame0,
    frame1,
    frame2,
    frame3
};
```

and:

```cpp
const uint8_t numFrames = 4;
```

If another frame is added:

```cpp
frame4
```

add it to the array and change:

```cpp
const uint8_t numFrames = 5;
```

The frame count must match the actual frames available.

---

# 🔁 Looping

The animation is normally made to loop continuously:

```cpp
frameIdx = (frameIdx + 1) % numFrames;
```

This means that after the final frame, playback returns to:

```text
frame0
```

and starts again.

---

# 📐 Display Rotation

The default orientation is:

```cpp
display.setRotation(0);
```

If required, the display can use another rotation:

```cpp
display.setRotation(1);
display.setRotation(2);
display.setRotation(3);
```

However, bitmap animations are normally generated for one specific orientation, so changing rotation may make the artwork appear incorrectly oriented.

---

# 🧪 Hardware Test Checklist

Before troubleshooting an animation, verify:

```text
[ ] ESP32-C3 is detected
[ ] Correct COM port selected
[ ] OLED receives 3.3 V
[ ] OLED GND connected
[ ] OLED SDA connected to GPIO5
[ ] OLED SCL connected to GPIO4
[ ] OLED address is 0x3C
[ ] Adafruit GFX installed
[ ] Adafruit SSD1306 installed
[ ] Board set to ESP32C3 Dev Module
[ ] USB CDC On Boot enabled
[ ] Test sketch displays TEST
```

Once all of these are working, upload the desired animation.

---

# 🧩 Common Mistakes

### Mistake 1 — Connecting SDA/SCL to Arduino Uno pins

The original animation generators may provide Arduino Uno instructions such as:

```text
SDA = A4
SCL = A5
```

That is not the wiring used by this ESP32-C3 project.

Use:

```text
SDA = GPIO5
SCL = GPIO4
```

and:

```cpp
Wire.begin(5, 4);
```

---

### Mistake 2 — Supplying the OLED incorrectly

For this project use:

```text
OLED VDD → ESP32 3V3
```

and:

```text
OLED GND → ESP32 GND
```

---

### Mistake 3 — Wrong display size

The animations are designed for:

```text
128 × 64
```

Do not use:

```cpp
128, 32
```

for these full-screen bitmap animations.

---

### Mistake 4 — Wrong address

The project uses:

```text
0x3C
```

---

### Mistake 5 — Incorrect frame count

If an animation has 106 frames, the code must reference all 106 frames and use:

```cpp
const uint8_t numFrames = 106;
```

An incorrect frame count can cause missing frames or invalid memory access.

---

# 🗂️ Animation Collection

| File | Animation | Display | Controller | Interface |
|---|---|---|---|---|
| `Scuba_Cat.ino` | Scuba Cat | 128×64 | SSD1306 | I²C |
| `SpiderMan.ino` | Spider-Man | 128×64 | SSD1306 | I²C |
| `Red_John.ino` | Red John | 128×64 | SSD1306 | I²C |
| `Animation_04.ino` | Additional animation | 128×64 | SSD1306 | I²C |
| `Animation_05.ino` | Additional animation | 128×64 | SSD1306 | I²C |

This table can be updated whenever new animations are added.

---

# 🛠️ Project Architecture

The complete system can be represented as:

```text
                    ┌─────────────────────┐
                    │   Animation .ino    │
                    │                     │
                    │  Bitmap Frames      │
                    │  Frame Timing       │
                    │  Animation Loop     │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │     ESP32-C3        │
                    │                     │
                    │ SDA → GPIO5         │
                    │ SCL → GPIO4         │
                    └──────────┬──────────┘
                               │
                         I²C @ 0x3C
                               │
                               ▼
                    ┌─────────────────────┐
                    │  SSD1306 OLED       │
                    │                     │
                    │     128 × 64        │
                    │    Monochrome       │
                    └─────────────────────┘
```

---

# 📋 Software Architecture

Each animation follows this basic sequence:

```text
Initialize Serial
       ↓
Initialize I²C
       ↓
Initialize SSD1306
       ↓
Load frame references
       ↓
Select current frame
       ↓
Draw bitmap
       ↓
Refresh OLED
       ↓
Advance frame index
       ↓
Wait for frame interval
       ↓
Repeat
```

---

# 🧠 Why This Repository Uses `.ino` Files

Each animation is intentionally kept as a separate Arduino sketch.

This allows users to:

- Open one animation at a time.
- Upload without changing the main project.
- Modify frame timing.
- Replace bitmap frames.
- Add their own animations.
- Experiment with the display code.

There is no requirement for a complicated build system.

---

# ➕ Adding Your Own Animation

Recommended naming format:

```text
Character_Name.ino
```

Examples:

```text
Batman.ino
IronMan.ino
Naruto.ino
SpiderMan.ino
Scuba_Cat.ino
```

Keep the file directly in the root of the repository.

After adding it, update the animation table in this README.

---

# 🤝 Contributing

Contributions can include:

- New OLED animations
- Improved bitmap conversions
- Better frame timing
- Display optimizations
- Bug fixes
- Documentation improvements
- New ESP32-compatible animation techniques

When adding an animation:

1. Keep the resolution at `128×64`.
2. Use the same GPIO configuration unless there is a documented reason to change it.
3. Use the SSD1306 I²C interface.
4. Keep bitmap data in `PROGMEM`.
5. Add the `.ino` file to the repository root.
6. Add the animation to the table in this README.
7. Mention any special requirements.

---

# ⚠️ Artwork and Copyright

Some animations may depict characters, scenes, logos, or artwork owned by third parties.

This repository is intended as a technical/educational demonstration of:

- ESP32 programming
- OLED displays
- bitmap graphics
- animation playback
- embedded systems

Third-party names, characters, artwork, and trademarks remain the property of their respective owners.

If an animation is based on third-party artwork, users should consider the applicable copyright, trademark, licensing, and distribution requirements before redistributing it commercially.

---

# 📜 License

Unless otherwise specified for a particular animation or asset, the source code of this repository can be released under the license included in:

```text
LICENSE
```

Third-party artwork and assets are not automatically covered by the repository's software license.

---

# 🚀 Future Improvements

Possible future additions:

- More OLED animations
- Automatic frame-rate configuration
- Button-controlled animation selection
- Multiple animation modes
- Random animation playback
- Animation speed control
- Brightness control
- SSD1306 display effects
- Sprite-based animation
- Compressed frame storage
- SD-card-based animation playback
- Web-based animation uploader
- Wi-Fi animation transfer
- OTA animation updates
- Menu system for selecting animations

---

# ⭐ Quick Start

For someone who just wants to run an animation:

### Hardware

```text
ESP32-C3 Super Mini
        +
0.96" 128×64 SSD1306 OLED
```

### Wiring

```text
OLED GND → ESP32 GND
OLED VDD → ESP32 3V3
OLED SCL → ESP32 GPIO4
OLED SDA → ESP32 GPIO5
```

### Arduino IDE

```text
Board:
ESP32C3 Dev Module

USB CDC On Boot:
Enabled

Upload Speed:
921600
```

### Libraries

```text
Adafruit GFX Library
Adafruit SSD1306
```

### Code

```cpp
Wire.begin(5, 4);
```

OLED address:

```cpp
0x3C
```

Then open any animation:

```text
Scuba_Cat.ino
```

```text
SpiderMan.ino
```

```text
Red_John.ino
```

and upload it.

---

# 🔗 Project Summary

**Controller:** ESP32-C3 Super Mini V1.6.1 / V1601  
**Display:** 0.96-inch SSD1306 OLED  
**Resolution:** 128×64  
**Color:** Monochrome  
**Interface:** I²C  
**I²C Address:** `0x3C`  
**SDA:** GPIO5  
**SCL:** GPIO4  
**Frame Size:** 1024 bytes per 128×64 monochrome frame  
**Animation Storage:** `PROGMEM`  
**Graphics Library:** Adafruit GFX  
**Display Library:** Adafruit SSD1306  

---

## Made for ESP32 + OLED experimentation

A simple collection of bitmap animations for the ESP32-C3 Super Mini and 128×64 SSD1306 OLED.

Add a new `.ino`, upload it, and animate.
