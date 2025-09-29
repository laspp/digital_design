# FPGA System on chip (SoC) development:

- SoC (System on Chip) a single integrated circuit that contains several components:
  - CPU: Central Processing Unit
  - simple I/O peripherals
  - special purpose hardware accelerators
  
- FPGA SoC features:
  - taior the processor (applicable to "soft" processors)
  - selecte only required peripherals
  - develop HW accelerators for computationally intensive tasks
  
- The advantages of FPGA SoC:
  - exploiting trade-offs between software and hardware
    - which part of the system should be implemented in software and which part in hardware
  - HW/SW co-design
    - concurrent development of software and hardware

- IP (Intellectual Property) cores:
  - pre-designed components that can be integrated into the FPGA design
    - e.g. UART, SPI, I2C, Ethernet, etc. 
  - ca be developed by:
    - the FPGA vendor
    - third-party vendors
    - the users themselves

- FPGA SoC development flow:
  - partition the system into software routines and hardware components
  - design user custom hardware components, if required
  - develop the hardware platform
  - develop software routines
  - integrate the software routines and hardware components
  - perform the system-level testing

- HW/SW partition:
  - decide which part of the system should be implemented in software and which part in hardware
- HW development:
  - integrate the existing IP cores to develop the hardware platform
  - Vivado employs the IP Integrator tool to integrate the IP cores
    - it is a graphical tool that allows the user to connect the IP cores and configure them
    - the tool generates the HDL code that describes the system
    - hardware specification file (XSA) is generated
      - defines the hardware platform
      - metadata about the hardware platform:
        - uttilized processor
        - memory map
        - used IP cores
  
- SW development:
  - two types of sofware:
    - system software
      - predesigned and provided with the system
    - application software
      - developed by the user
  - BSP (Board Support Package)
    - mechanism to encapsulate the system 
    - a set of drivers and libraries that allow the application software to interact with the hardware platform
  - Device drivers
    - software routines that allow the application software to interact and control the peripheral devices
    - "translator" between the application software and the HW peripherals
