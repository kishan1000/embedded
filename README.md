# STM32F401 Black Pill Firmware Portfolio

This repository contains a collection of bare-metal and Low-Layer (LL) driver implementations for the **STM32F401CCU6 Black Pill** development board. The projects are structured chronologically to document my progress in mastering embedded C, register manipulation, and hardware peripheral configuration using VS Code.

---

## 🛠️ Hardware Specification

* **Microcontroller:** STM32F401CCU6 (ARM Cortex-M4 with FPU)
* **Core Clock:** 16 MHz HSI (Internal High-Speed Oscillator default setup)
* **Flash Memory:** 256 KB
* **SRAM:** 64 KB
* **Onboard LED:** PC13 (**Active Low** — turns ON when pin is cleared, OFF when pin is set)
* **Onboard Key:** PA0 / NRST

---

## 💻 Development Toolchain

* **IDE/Editor:** VS Code (Multi-Root Workspace setup)
* **Code Generator:** STM32CubeMX (Configured for LL Driver generation)
* **Compiler:** GNU Arm Embedded Toolchain (`arm-none-eabi-gcc`)
* **Build System:** CMake / Makefile
* **Flash & Debugger:** ST-Link V2 (via Cortex-Debug extension and OpenOCD)

---

## 📂 Project Directory Structure

```text
embedded/                           # Global Git Repository Root
├── .gitignore                      # Rules to ignore build binaries
├── stm32-portfolio.code-workspace  # VS Code multi-root setup for pristine IntelliSense
├── README.md                       # Repository overview and guide
│
├── 00_STM32/                       # Pristine, blank project template (used for scaffolding new code)
│   ├── Core/                       # Fresh template source/header directories
│   ├── Drivers/                    # Core CMSIS and ST Low-Layer drivers
│   └── *.ioc                       # Baseline CubeMX chip setup blueprint
│
├── 01_BLINK/                       # Working register & LL delay-based blink project
│   ├── Core/                       # Application code modifying PC13 outputs
│   └── Drivers/                    # Copied local LL driver library dependencies
│
├── 02_GPIO_INPUT/                  # [Upcoming] Polling and EXTI button reading using 00_STM32 template
└── 03_PWM_GENERATION/              # [Upcoming] Timer configuration basics for LED dimming
```

---

## 🚀 Getting Started

### Prerequisites

To compile and debug any of the sub-projects, ensure you have the following installed on your machine:

1. `arm-none-eabi-gcc` toolchain added to your system environment variables.
2. `CMake` (version 3.20+) or standard `make` utility.
3. `OpenOCD` (for hardware flashing through ST-Link).

### Compilation Guide

1. Double-click and open the `stm32-portfolio.code-workspace` file in VS Code.
2. Open your terminal and change directory to the targeted project folder:

   ```bash
   cd 01_BLINK
   ```

3. Generate the build files and compile using CMake:

   ```bash
   cmake -B build -G "MinGW Makefiles" # Or use "Unix Makefiles" depending on your OS
   cmake --build build
   ```

---

## 📝 Learning Logs & Insights

### Project 01: BLINK

* **Objective:** Toggle the onboard PC13 LED using direct register definitions combined with standard LL utility helper delays.
* **Key Learnings:**
  * **Clock Gating:** Failing to set the proper bit inside the `RCC->AHB1ENR` register isolates the entire peripheral from receiving clock ticks, rendering any subsequent configuration calls to `GPIOC->MODER` completely useless. On the STM32F401, Port C corresponds to Bit 2 (`1 << 2`).
  * **Active Low Circuitry:** The onboard LED circuit configuration operates inversely to normal logic conventions; pulling the designated pin **LOW** establishes the electrical path to ground, turning the LED **ON**.
