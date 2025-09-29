# Digital integrated circuits 

- In simple terms, a miniature low-cost circuit consisting of active and passive elements fabricated on a single-crystal die.

- Based on technology, we can divide Digital ICs (Integrated Circuits) into:
  - Bipolar (BJT)
  - Unipolar -> MOS, CMOS
  - Mixed (BiCMOS)
  
> Bipolar Junction Transistors compared to CMOS
> - Operate at higher frequencies 
> - Better gain
> - Higher output currents
> - However, exhibit higher energy consumption (static consumption is not equal to zero), larger dimensions, and are slower

- Scale of integration: measure of the complexity of digital circuits  
  - Small Scale Integration (SSI): up to 100 basic elements 
  - Medium Scale Integration (MSI): 100 to 1000 basic elements 
  - Large Scale Integration (LSI): 1000 to 10000 basic elements 
  - Very Large Scale Integration (VLSI): 10000 to 100000 basic elements 
  - Ultra Large Scale Integration (ULSI): 100000 to 1000000 basic elements
  - Wafer Scale Integration

- Moore's Law 

## Design methodologies 

- We can divide Digital ICs into: 
  - Standard Integrated Circuits (SIC)
  - Application Specific Integrated Circuits (ASIC)
  - Standard ASIC (SASIC)
  
### Standard Integrated Circuits: 

- Targeted for an unknown customer, e.g. 
  - The end-user does not have any influence on the design 
- Manufactured in large quantities (mass-produced) -> lower costs 
- First SICs were not suited for end-users 
  - Weld SICs together to form the desired system
- Software programmable SICs
  - Programming offers higher flexibility to the end-users
    - User only needs to software program the required processor 
  - Software bottleneck
    - CPUs can be used for different purposes but how effectively 
  
### Application specific integrated circuits 

- Target specific application/field 
- Two types: 
  - Full custom 
  - Semi custom
  
#### Full custom 

- Specially built for a specific user 
- Designed according to the customer's needs e.g. systolic arrays, genome analysis, accelerator 
- Design is strongly confidential 
- High price per product
  - Design cost same as SIC but smaller amount of products 

#### Semi custom 

- Predesigned or preprocessed chips 
- Manufacturer has on board multiple tested modules, which are connected 
- Based on user requirements, the manufacturer connects them into design, does not start from scratch 
- Can be adapted to multiple users, it is much cheaper per sold item.
- Differ three types: 
  - Standard cells: represent basic, elementary logic gates and memory elements 
  - Macrocells: larger logic blocks, such as MUX, adders, multipliers, registers and even microprocessors 
  - Gate Array: the basic elements are gates or groups of transistors.
    - Not connected, connected when manufactured 

<!---  

## Design cycle 

### The Y chart 

- Defines different perspectives digital design (axis) and various level of abstractions (concentric circles)

<img src="./img/02/YDiagrama.png" alt="PN Junction" width="300">

- Behavioral perspective: what a circuit or system does, not how it is actually built
  - black box method
  - e.g. adder is module for adding. Not interested how is it implemented (CLA, Kogge-Stone, simple adder)
  
- Structural perspective: looking at building blocks is composed and how they connect to each other. 
  - indetify elements of an adder: half/full adders, carry generation circuits p and g 
  
- Physical point of view: how the various HW components and wires are arranged in space availaible on semiconductor chip. 


### Major stages in VLSI design 

- There a number of several steps required for an efficient VLSI design cycle

1. System-level design: Biggest influence on the final outcome 
   - specify functionality, operating conditions, and desired characteristics 
   - partition the systems functionality into subtsks 
   - decide on interfaces and protocols for data exchange
   - decide on data formats, operating modes etc. 

2. Algorithm design: meet the the data and/or signal processing requirments defined before with a series of computations in view with their implementation in HW
   - employ the algorithm for simulation purposes
   
3. Arhitecture design: major part in building the design. VLSI architects essentially decide on the necessary hardware resources and organize their interplay in such a way as to implement a known computational algorithm under the performance, cost, power, and other constraints imposed by the target application. Two steps: 
   - High-level architecture design: capturing the higher levels of abstractions for desired architere, e.g. partition the design, decide on paralelism, opportunities for pipelining, cell library selection etc. 
   - Register transfer level (RTL); circuit is modeled as a collection of storage elements interconnected by purely combinational circuits: how to implement arithmetic, use hardwired control logic or microcode, scheduling, binding etc. 

4. Logic design: The translation into a gate-level netlist and its optimization are largely automatic. 
    - Synthesis major steps:
      - parse design 
      - elaborate design: convert to structural design 
      - logic optimization 
      - Mapping:
        - First map the higher level cells with the design and convert them into lower representation 
        - convert the lower representation into standard cells 
         
5. Design verifiaction and testing.
   - The most visited step. You will spend about 80% of your time testing your design.     

### Physical design 

#### Glossary: 

| Term                | Definition                                                                 |
|---------------------|----------------------------------------------------------------------------|
| Standard cell              | building block that implements a logic gate      |
| Macro cell               |  building bloc that is typical larger and more complex than the standard cells (subdesign or full custom block like RAM)|
| Terminal               |  logical connection part of a module or cell    |
| Net      | The logical/signal interconnection between instances      |
|PDK| Process design kit - the standard cell with all other specific data needed to design chip|
|.gds| the design converted to the interval language of lasre writing equipment in a format commongly used to transfer IC design layout data.|
|Die area| Total silicon area available to place pads and core cells|
|power planning| create the power distribution network|



#### Floorplanning 

- Concered with organizing the major circuit blocks into a rectangular area as small as possible while at the same time limiting the interconnection delays 

#### Place & route 

- Each cell gets assigned a specific location on the die before the wires are placed.

- Global placement: takes a chip-wide problem and tries to make sure every cell is roughly in a good spot.
  > What makes it good: good placement minimizes area, keeps the design routable, and delivers the highest possible frequency.

- Detailed placement: looks at the individual cells and makes sure they are in a legal spot.
- Estimating the delay:
  - estimates the effects of the wiring through the parasitics.
  - interpreting the delay of a wire.
- Clock tree synthesis: distributes the clock.
- Global routing:
  - divides the design into routing cells.
  - each cell keeps track of how many wires go through it.
- Timing repairs:
  - meets our requirements.
- Detailed routing:
  - implements the routing plan with geometry fixes, and so on.
  
#### Physical design verification 

- Goal: physical layout database that essentially contains the geometrical info for all design layers.
  - This (mask data) is used by the IC foundry as a blueprint for manufacturing.
- Relies on a number of software tools:
  - DRC (Design Rule Check): examines the conformity of the layout with geometric rules imposed by the target process.
  - Layout extraction: obtains the actual circuit netlist in preparation for the next stage.
  - LVS (Layout vs schematic): compares the layout against the desired one.
  - Post-layout timing verification.
  - Post-layout simulation.

--->

## SASIC 

### Configuration technologies 

- **Static memory**: The key element is an electronic switch (TG, three-state buffer, pass transistors) that gets turned on or off under the influence of configuration bits. The configuration bits are typically stored in SRAM.
- **UV-erasable memory**: The key element is a special MOSFET with a second gate electrode, sandwiched between the actual gate and bulk material (floating gate). A strong lateral field between the drain and source injects electrons into the floating gate. Because the electrons cannot escape, they are trapped in the floating gate. If you want to erase it, you need to apply UV light.
- **Electrically erasable memory**: The key element is a MOSFET with a floating gate that can be electrically erased.
- **Fuse or antifuse**
  - Fuses: narrow bridges of conducting materials that blow when a programming current is forced through.
  - Antifuse: thin dielectrics which separate two conducting layers. After applying programming voltage, the antifuses rupture, thereby establishing a conductive channel.
  - Programming is permanent.

### Organization of hardware resources 

- Simple programmable logic devices (SPLD):
  - Consist of one or two programmable logic planes: AND plane and OR plane.
  - Remember: we represent each function as a sum of products.
  - Vertical lines: inputs and their inverted values (2*n, n - number of input variables).
  - Horizontal lines: represent product terms.
  
  - PAL - Programmable Array Logic:
    - AND plane is configurable.
    - OR plane is fixed.
  
  - PLA - Programmable Logic Arrays:
    - Both AND and OR planes are configurable.
  
  - PLE - Programmable Logic Element:
    - AND plane is fixed: it consists of all product terms (2^n).
    - OR plane is configurable: connect only terms that evaluate to true in the truth table.

<img src="./img/02/SPLD.png" alt="PN Junction" width="600">


- Complex programmable logic devices 
  - Expand the idea behind SPLD by providing many of them on a single chip.
  - Example: [Coolrunner 2](https://docs.amd.com/v/u/en-US/ds090) from Xilinx.
  - Coolrunner 2 architecture:
    - I/O blocks: connect the board to the external world.
    - Functional blocks: 32.
    - AIM (Advanced Interconnect Matrix): connects the functional blocks to each other and to the I/O blocks.
  - Functional blocks:
    - PAL:
      - 56 product terms.
      - 16 OR terms.
    - Additional elements:
      - XOR for controlled inverting of input signals.
      - FF that can be configured as:
        - D flip-flop.
        - Latch.
        - T flip-flop.
  
<img src="./img/02/CPLD.png" alt="PN Junction" width="600">





