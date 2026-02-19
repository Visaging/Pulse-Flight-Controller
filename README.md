# PULSE Flight Controller
## Why "Pulse"?
The name is inspired by two things I love:
1.  **Pulse Width Modulation (PWM):** The heartbeat signal that powers every motor in this drone.
2.  **Beatbox:** A personal touch (look at the silkscreen artwork on the top layer).

## Overview
Pulse is a completely custom flight controller that was created from the ground up. I designed the whole stack myself to truly understand how a drone works, from the low-level motor mixing logic to the physics of the gyroscope, even though it would have been simpler to simply rely on off-the-shelf hardware and Betaflight. The custom PCB designs and the particular STM32 firmware I wrote to make the silicon work are both included in this repository, which acts as documentation for that process.

## Hardware Stack
* **MCU:** **STM32F405RGT6** - Chosen because it's a beast at math and has enough pins for everything.
* **IMU:** **ICM-42688-P** - Newer and cleaner noise performance than the old MPU6000.
* **Barometer:** **BMP280** - for altitude hold.
* **Gimbal Drivers:** 2x **DRV8313** - integrated directly on the FC.
    * Instead of a heavy separate gimbal controller board, I built the drivers right onto the FC to save weight and space.
* **Power System:** - Redesigned buck converter using **LMR14030SDDAR**.
  * I initially designed it for 6S (25.2V), but realized I wanted headroom. The new design handles up to **40V** input.

## The Firmware
### Key Features:
* **1kHz Control Loop:** A strict real-time scheduler runs the physics engine every 1ms.
* **Quad-X Mixing:** Takes PID output and mathematically mixes it for the 4 motors using **TIM8** (Timer).
* **Interrupt driven I/O:** The Receiver (UART3) and Telemetry (UART4/5) run in the background via Interrupts, so the main flight loop never "freezes" waiting for data.
* **Smart Telemetry:** Streams live voltage, angles, and state to a **Raspberry Pi** companion computer.

### How to Flash It
I added a **USB-C port** and the "Holy Trinity" of buttons (BOOT + RESET) to the PCB so I don't need external dongles.
1.  Plug in USB-C.
2.  Hold **BOOT**.
3.  Click **RESET**.
4.  Release **BOOT**.
5.  Flash the `.bin` file using **STM32CubeProgrammer** (USB DFU Mode).

--- 
<details>
  <summary><h2>PCB Schematics</h2></summary>
<img width="4960" height="3507" alt="image" src="https://github.com/user-attachments/assets/44ae1a04-9f0a-4dec-affc-759dca975bb0" />
</details>

---
<details>
  <summary><h2>PCB Layout and Render</h2></summary>

## Layout
#### Overall:
<img width="800" height="788" alt="image" src="https://github.com/user-attachments/assets/adf31cc9-bd3c-450c-bd48-9a03b4856575" />

---
#### Top Layer:
<img width="807" height="789" alt="image" src="https://github.com/user-attachments/assets/e9ae0ef1-d9a7-42da-8e8a-e60f8c436533" />

---
#### Layer 2:
<img width="791" height="789" alt="image" src="https://github.com/user-attachments/assets/cde8cd00-cb11-421d-9744-4a1d67073479" />

---
#### Layer 3:
<img width="803" height="791" alt="image" src="https://github.com/user-attachments/assets/e52a7db7-8587-42fb-ba33-af2c8501d65b" />

---
#### Bottom Layer:
<img width="822" height="795" alt="image" src="https://github.com/user-attachments/assets/f5207353-3a2b-411d-8959-4afce298a374" />

---
#### Front Silkscreen:
<img width="810" height="796" alt="image" src="https://github.com/user-attachments/assets/f7a14602-14d2-4e71-bcfb-dc4a919c1601" />

---
#### Back Silkscreen:
<img width="808" height="797" alt="image" src="https://github.com/user-attachments/assets/a72383b3-2468-46f1-ae0c-754f904f68e4" />

---
## Render
#### Front:
<img width="1723" height="892" alt="image" src="https://github.com/user-attachments/assets/d4b9233f-e48f-45fa-8689-68f645ebc221" />

---
#### Back:
<img width="1723" height="892" alt="image" src="https://github.com/user-attachments/assets/c400d4ed-81da-4670-ac2f-9527101c3518" />
</details>

--- 
## Bill of Material

| Item | Description | Quantity | Unit Price ($) | Total Price ($) | URL | Running Total ($) |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| PCBA | PCB | 5 PCB, 1 PCBA | $216.41 | $216.41 | https://www.lioncircuits.com/ | $216.41  |
| Tactile Switches  | Boot and Reset Buttons | 2 | $0.0363 | $0.0726 | https://robu.in/product/ts6610tp-160gf-bxg-022-shou-han-50ma-100m%cf%89-100000-times-12v-160gf-round-button-standing-paste-spst-smd-tactile-switches-rohs/ | $216.4826 |
| TYPE-C-31-M-12 | USB-C Port | 1 | $0.42 | $0.42 | https://www.lioncircuits.com/ | $216.9026 |

<details>
  <summary>JLCPCB Order</summary>

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/15efc230-370d-4c71-9dc9-1fa7a84ac987" />
<img alt="image" src="https://github.com/user-attachments/assets/2b865b6a-f698-46ed-835a-6a73f7c1f475" />
<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/dcdbb06e-31b7-45f5-b116-649301c5f293" />
</details>
<details>
  <summary>Lion Circuit Order</summary>

<img width="1920" height="1080" alt="final" src="https://github.com/user-attachments/assets/8b207d0c-e386-4ffc-9dc9-fb86ffd4e430" />
<img width="6622" height="9362" alt="Lion Circuits_Page_1" src="https://github.com/user-attachments/assets/ff2e3589-3140-404c-bba5-9da31397aef1" />
</details>
<details>
   
  <summary>Cart</summary>

<img width="1920" height="1020" alt="image" src="https://github.com/user-attachments/assets/ddc62a58-1b4b-44b6-ad5e-a50e6e2523f6" />
</details>

## Lessons Learned
* **Power is hard:** My first schematic had a buck converter that would have fried on a 6S battery. Catching that mistake saved the project.
* **Routing matters:** Moving to 4 layers was scary at first, but it solved all my noise and heat issues.
* **Code vs. Reality:** Writing the firmware forced me to verify every single pin connection. I found out that "polling" a receiver is too slow, so I had to learn how Interrupts work.

## License: MIT
