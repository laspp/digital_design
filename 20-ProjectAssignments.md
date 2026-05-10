# Project Assignments

Please find below the list of project assignments for the semester.

Please note, that project assignments substitute oral exams. If you do not complete the project assignments, you will need to take an oral exam to pass the course. 

## Assignment 1: Audio subsystem in FproV3 SoC 

- Implement the audio subsystem and integrate it inside the FproV2 system. The system should have the following functionality:
  - The audio buffer should that can contain one audio recording 
  - Core for generating audio noise and blending with the input stream  
  - Core for smoothing: implement a simple FIR filter that performs smoothing 
  - The source of the audio system should be  to the audio buffer 
  - Develop the driver for the core and develop the application that will showcase the functionality of an audio subsystem 
  - Number of students per group: up to 2


## Assignment 2: FproV3 SoC implementation in Chisel

- Implementing the FproV3 SoC in Chisel 
    - Develop the FproV3 SoC from scratch in Chisel language, which consists of only the memory subsystem 
    - Get with the design flow in Chisel and compare the implementation we conducted in class 
    - Note: The UART system should also be added to the implemented SoC 
    - Number of students per group: up to 2

## Assignment 3: Video subsystem in FproV3 SoC

- Add the video subsystem and integrate it inside the FproV3 system. The system should have the following functionality:
  - Core for generating patterns: implement a core that will generate different patterns (e.g., color bars, checkerboard, etc.)
    - the user should be able to select the pattern via MMIO registers
  - core for rgb to grayscale conversion
    - the user should be able to enable/disable the conversion via MMIO registers
  - core for sprite overlay: implement a core that will overlay a sprite on top of the video stream
    - the user should be able to set the sprite position via MMIO registers
    - the sprite data should be stored in a memory buffer 
  - The source of the video system should be to the framebuffer memory
  - Number of students per group: up to 2

## Assignment 4: Building SD card controller in FproSoC
- Add SD card controller core to the FproV3 system
  - the core should be able to read data from the SD card and store it in the memory
  - Integrate the SD core inside the MMIO subsystem
  - Develop the driver for the core and application that will showcase the functionality of a system
  - Number of students per group: up to 2

## Assignment 5: keyboard controller in FproV3 SoC
- Add a keyboard controller core to the FproV3 system
  - the core should be able to read data from the keyboard store it in the memory
  - Integrate the keyboard core inside the MMIO subsystem
  - Develop the driver for the core and application that will showcase the functionality of a system
  - Number of students per group: up to 2

## Assignment 6: Quad SPI controller core integration in FproV3 SoC

- Add a Quad SPI controller core to the FproV2 system
  - the core should be able to read/write data from/to the Quad SPI flash memory
  - Integrate the Quad SPI core inside the MMIO subsystem
  - Develop the driver for the core and application that will showcase the functionality of a system
  - Number of students per group: up to 2

## Assignment 7: I2C core integration in FproSoC 
- Add I2C controller core to the Fpro system:
    - Develop the I2C controller, which will read data from the temperature sensor  
    - Integrate the I2C core inside the MMIO subsystem 
    - Develop the driver for the core and application that will showcase the functionality of a system
    - Number of students per group: up to 2

## Assignment 8: Chipyard SoC development
- Utilize the Chipyard framework to design and implement a custom SoC.
- Integrate various peripherals and cores into the SoC using Chipyard's modular components.
- Test and validate the functionality of the SoC through simulation and synthesis.
- Number of students per group: up to 2

##  Assignment 9: Your project idea
- Propose your project idea related to digital design/SoC development
- The project should be approved by the instructor
- Number of students per group: up to 2



