# Project Assignments

Please find below the list of project assignments for the semester.

Please note, that project assignments substitute oral exams. If you do not complete the project assignments, you will need to take an oral exam to pass the course. 

## Assignment 1: Audio subsystem in FproV2 SoC (Taken)

- Implement the audio subsystem and integrate it inside the FproV2 system. The system should have the following functionality:
  - The audio buffer should that can contain one audio recording 
  - Core for generating audio noise and blending with the input stream  
  - Core for smoothing: implement a simple FIR filter that performs smoothing 
  - The source of the audio system should be  to the audio buffer 
  - Develop the driver for the core and develop the application that will showcase the functionality of an audio subsystem 
  - Number of students per group: up to 2

## Assignment 2: I2C core integration in FproSoC (Taken)
- Add I2C controller core to the Fpro system:
    - Develop the I2C controller, which will read data from the temperature sensor  
    - Integrate the I2C core inside the MMIO subsystem 
    - Develop the driver for the core and application that will showcase the functionality of a system
    - Number of students per group: up to 2

## Assignment 3: FproV2 SoC implementation in Chisel (Taken)

- Implementing the FproV2 SoC in Chisel 
    - Develop the FproV2 SoC from scratch in Chisel language, which consists of only the memory subsystem 
    - Get with the design flow in Chisel and compare the implementation we conducted in class 
    - Note: The UART system should also be added to the implemented SoC 
    - Number of students per group: up to 2

## Assignment 4: Video subsystem in FproV2 SoC (Taken)

- Add the video subsystem and integrate it inside the FproV2 system. The system should have the following functionality:
  - Core for generating patterns: implement a core that will generate different patterns (e.g., color bars, checkerboard, etc.)
    - the user should be able to select the pattern via MMIO registers
  - core for rgb to grayscale conversion
    - the user should be able to enable/disable the conversion via MMIO registers
  - core for sprite overlay: implement a core that will overlay a sprite on top of the video stream
    - the user should be able to set the sprite position via MMIO registers
    - the sprite data should be stored in a memory buffer 
  - The source of the video system should be to the framebuffer memory
  - Number of students per group: up to 3

## Assignment 5: Building SD card controller in FproSoC (Taken)
- Add SD card controller core to the FproV2 system
  - the core should be able to read data from the SD card and store it in the memory
  - Integrate the SD core inside the MMIO subsystem
  - Develop the driver for the core and application that will showcase the functionality of a system
  - Number of students per group: up to 2

## Assignment 6: keyboard controller in FproV2 SoC (Taken)
- Add a keyboard controller core to the FproV2 system
  - the core should be able to read data from the keyboard store it in the memory
  - Integrate the keyboard core inside the MMIO subsystem
  - Develop the driver for the core and application that will showcase the functionality of a system
  - Number of students per group: up to 2

## Assignment 7: Quad SPI controller core integration in FproV2 SoC

- Add a Quad SPI controller core to the FproV2 system
  - the core should be able to read/write data from/to the Quad SPI flash memory
  - Integrate the Quad SPI core inside the MMIO subsystem
  - Develop the driver for the core and application that will showcase the functionality of a system
  - Number of students per group: up to 3

## Assignment 8: Developing Posit arithmetic core (Taken)
- Develop a Posit arithmetic core that supports multiplication and addition operations
  - The core should support different Posit configurations (e.g., 8-bit, 16-bit, etc.)
  - Integrate the Posit core inside the MMIO subsystem
  - Develop the driver for the core and application that will showcase the functionality of a system
  - Number of students per group: up to 2

## Assignment 9: Simulation using CoCotb library  (Taken)

- Select a digital design project (e.g., UART, SPI, I2C, etc.)
- Develop the testbench for the selected project using the CoCotb library
- Analyze the simulation results and verify the functionality of the design
- Number of students per group: up to 2

## Assignment 10: Transactional Verification using UVM methodology in Forastero
- Employ UVM methodology to create a comprehensive verification environment for the FproV2 SoC.
- Develop UVM components such as drivers, monitors, and scoreboards to facilitate effective verification.
- Execute simulations to validate the functionality and performance of the FproV2 SoC.
- Number of students per group: up to 2

## Assignment 11: Chipyard SoC development
- Utilize the Chipyard framework to design and implement a custom SoC.
- Integrate various peripherals and cores into the SoC using Chipyard's modular components.
- Test and validate the functionality of the SoC through simulation and synthesis.
- Number of students per group: up to 3

##  Assignment 12: Your project idea
- Propose your project idea related to digital design/SoC development
- The project should be approved by the instructor
- Number of students per group: up to 3



