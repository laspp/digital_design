# Developing APB based SoC platform


- In this section, we will develop a simple SoC platform based on the APB protocol 
  - our inspiration lies in the [FPRO platform](https://www.amazon.com/FPGA-Prototyping-SystemVerilog-Examples-MicroBlaze/dp/1119282667)


- Features:
  - simple: easy to use and understand
  - portable: all the IP cores are developed from scratch
  - functional: can be used for real-world applications, but not as powerful as commercial platforms
  - educational: designed for educational purposes
  
- The platform consists of:
  - processor module
  - APB bridge and bus 
  - Interconnect 
  - I/O peripherals (GPIO, UART, etc.)


## Processor module:

- any 32-bit RISC processor can be used
- On the class, we use the [MicroBlaze processor](https://www.amd.com/en/products/software/adaptive-socs-and-fpgas/microblaze.html) and [MicroBlaze MCS](https://www.amd.com/en/products/software/adaptive-socs-and-fpgas/mb-mcs.html)
- Microblaze MCS (Microcontroller system):
  - complete computer system centered around the MicroBlaze processor
  - besides the processor, it includes RAM and IO module with standard set of microcontroller interfaces
  - all the components are constructed from FPGA resources
- Microblaze processor
  - 32-bit processor with RISC architecture
  - "soft" processor, i.e. implemented in the FPGA fabric
  - can be customized to the user requirements
    - e.g. floating-point unit, memory management unit, caches, etc.
    - AXI protocol for communicating with the peripherals
    - Note: the MicroBlaze MCS does not support the AXI protocol


## Memory-mapped I/O (MMIO) I/O devices:

- MMIO scheme: use load and store instructions to access the memory-mapped registers
  - I/O devices and memory are mapped to the same address space
  
- I/O register map:
  - I/O device is just a set of registers
    - each register is mapped to a specific address
    - each register depicts a specific function of the device

### I/O address space of the FPRO SoC platform:

- Our system MMIO subsystem specification
  - provides space for 64 I/O devices or slots (0-63)
  - each device has 32 registers (0-31)
  - each register is 32-bit wide
  - the address of the register is 22-bit wide:
    -  ADDRESS[12:6] - address of slot 
    -  ADDRESS[6:0] - address of register

### Interface with the bus

- Main task in SoC is to integrate the custom logic into the system

- To attach the custom logic to the bus and access I/O core, the following steps are required:
  1. Add a wrapper around the custom logic to form a compatible I/O core that can interact with the bus
  2. Update system-level decoding (write operation) and multiplexing (read operation) logic to circuit to indetify and access the I/O core

- Wrapping circuit : 
  - enables core to be addressed by the bus
  - makes the core as a small memory-mapped device
  - contains:
    - decoding circuit for writing into the registers
    - multiplexing circuit for reading from the registers

-  System-level decoding and multiplexing logic:
   - used to identify the I/O core
   - multiplexes the data bus to read the data from the I/O core


## APB bridge 

- The bridge will intercept the MCS I/O bus signals and generate the according request to the APB master 
  - read or write request
- The bridge will also handle the response from the APB master and generate the appropriate signals for the MCS I/O bus 

### MCS I/O bus protocol

- signals involved in the MCS I/O bus protocol:
  - `io_address_strobe`: address strobe signal, used to indicate that the address is valid
  - `io_write_strobe`: write strobe signal to initiate a write operation
  - `io_read_strobe`: read strobe signal to initiate a read operation
  - `io_address`: 32-bit wide address signal used to identify the destination I/O core attached to the MCS I/O bus
  - `io_write_data`: 32-bit wide data signal, used to write the data to the destination I/O core
  - `io_read_data`: 32-bit wide data signal, used to obtain the data from the destination I/O core
  - `io_ready`: indicates whether transaction has been received
  - `io_byte_enable`: which byte of the `io_write_data` data is valid


<img src="./img/10/io_bus.png" alt="LUT Implementation" style="width:400px;"/>


Write operation:
  1. Master device places the address and data on the bus and asserts the address and write strobe signal
  2. Slave assets the `io_ready` signal, when the transaction is received
  3. Optionally, the master can assert the `io_byte_enable` signal to indicate which byte of the data is valid

Read operation:
  1. Master device places the address on the bus and asserts the address and read strobe signal
  2. Slave gets request and places the data on the `io_read_data` line 
  3. Slave assets the `io_ready` signal, when the read data is available


### APB bridge implementation

1. First, we need to decode the MCS I/O bus signals to determine whether the transaction should be initiated
    - Our whole SoC platform will be act as a single slave on the MCS I/O bus
```verilog
// The APB subsystem acts as slave on I/O bus. It starting address is 0xC000_0000
logic mcs_bridge_enable;
assign mcs_bridge_enable = (io_address[31:24] == BRG_BASE[31:24]);
```

2. Next we concentrate on the command generation
   - Indetify whether the transaction is a read or write operation
   - generate command for the APB master
   - generate the valid signal for the APB master
```verilog
    // Command generation
    // write_req is equal to one when io_write_strobe is asserted and io_read_strobe is not asserted
    logic write_req;
    assign write_req = io_write_strobe & ~io_read_strobe;

    // Command generation
    assign o_cmd = {write_req, io_write_data, io_address};

    // We will use a address_strobe to generate the valid signal. 
    // regardless of read or write, address_strobe is always asserted when there is a valid address
    assign o_valid = io_addr_strobe & mcs_bridge_enable; // do not generate any request if we did not select our system
```
3. We handle the response from the APB master and generate the appropriate signals for the MCS I/O bus
   - send read data to the MCS I/O bus if the transaction does not have an error (i_resp[DW] == 0)
   - send ready signal to the MCS I/O bus to indicate that read data are available
```verilog
    // Response handling
    assign io_read_data = i_rdata;
    assign io_ready = i_ready;
        always_comb begin
        io_read_data = i_resp[DW-1:0];
        if (i_resp[DW]) begin
            io_read_data = 32'hDEADFA17; 
        end
    end

    // Ready signal
    assign io_ready = i_ready & mcs_bridge_enable;
```

## Interconnect

- Connects the multiple master, memory, and I/O cores (slaves)
- Manages the data flow between the components
- Interconnect will be based on centralized arbitration/decode scheme
  - Arbitration: decides which master can access the bus
  - Decoding: decides which slave is accessed by the master
    - In our case, we have only one master (processor), so we don't need arbitration
  
- For this example, we will use a simple APB interconnect
  - connects the APB master (bridge) to multiple APB slaves (peripherals)
  - handles the address decoding and data multiplexing
  - supports up to 64 APB slaves 
    - each slave is assigned a unique address range

- System level decoding and multiplexing logic:
  - Decoding logic: decodes the address from the master to select the appropriate slave
    - Based on the address, the decoding logic generates the PSEL signal for the selected slave.
    - The rest of the slaves will have their PSEL signal deasserted.
  - Multiplexing logic: multiplexes the data from the selected slave to the master
    - The data from the selected slave is routed to the master based on the PSEL signal.
    - The data from the other slaves is ignored.

### Developing the interconnect

- Notation:
  - Mp<SignalName> - signal from/to master
  - Sp<SignalName> - signal from/to slave
    - Each signal is an array of signals, one for each slave
    ```verilog
    // forwarding signals: MpADDR, MpENABLE, MpWRITE, MpWDATA
    genvar i;
    for (i = 0; i < NUM_PERIPHERALS; i++) begin : gen_addr
        assign SpADDR[i] = MpADDR;
        assign SpENABLE[i] = MpENABLE;
        assign SpWRITE[i] = MpWRITE;
        assign SpWDATA[i] = MpWDATA;
    end
    ```

- Parameters:   
    - NUM_REG_PERIPHERAL - number of registers per peripheral
    - NUM_PERIPHERALS - number of peripherals (slaves)
    - DW - data width
    - AW - address width


- First we forward the signals from the master to all slaves
  - PADDR, PENABLE, PWRITE, PWDATA

```verilog
    // forwarding signals: MpADDR, MpENABLE, MpWRITE, MpWDATA
    genvar i;
    for (i = 0; i < NUM_PERIPHERALS; i++) begin : gen_addr
        assign SpADDR[i] = MpADDR;
        assign SpENABLE[i] = MpENABLE;
        assign SpWRITE[i] = MpWRITE;
        assign SpWDATA[i] = MpWDATA;
    end
```

- Here we use a generate block to create the forwarding logic for each slave
  - This block creates NUM_PERIPHERALS instances of the forwarding logic
    - It makes our code more scalable and easier to maintain
    - This is not classical for loop, but a generate loop
      - this mean it will be unrolled during synthesis
  
- Next, we generate the PSEL signal for each slave based on the address from the master
  - We refer to this as system-level decoding logic
  - The PSEL signal is generated based on the address from the master
  - The address bits used for selecting the peripheral are: (log2(NUM_PERIPHERALS) + log2(NUM_REG_PERIPHERAL) + 2 -1): log2(NUM_REG_PERIPHERAL) + 2
    - The +2 is because the registers are word-aligned (4 bytes)

```verilog
    localparam MSB = $clog2(NUM_PERIPHERALS);
    logic [MSB - 1 : 0] baseAddr;
    // Extracting base address from MpADDR
    localparam baseAddr_MSB = ($clog2(NUM_PERIPHERALS) + $clog2(NUM_REG_PERIPHERAL) + 2) - 1;
    localparam baseAddr_LSB = $clog2(NUM_REG_PERIPHERAL) + 2;
    assign baseAddr = MpADDR[baseAddr_MSB : baseAddr_LSB];

    // Decoder: Generate SpSEL signal (one-hot encoding)
    assign SpSEL = (MpSELx) ? (1 << baseAddr) : 0;
```
    - Here we extract the base address from the master address and use it to generate the PSEL signal
    - We use one-hot encoding for the PSEL signal, where only one bit is set to 1, indicating the selected slave

- Finally, we need to handle the read data and ready signals from the slaves to the master
  - We refer to this as system-level multiplexing logic
  - The read data and ready signals from the slaves are multiplexed to the master based on the PSEL signal
  - We use a combinational always block to implement the multiplexing logic

```verilog
    // MUX for the signals: MpRDATA, MpREADY, MpSLVERR
    always_comb begin : readData
        MpREADY = SpREADY[baseAddr];
        MpSLVERR = SpSLVERR[baseAddr];
        // Read data
        MpRDATA = 0;
        if (!MpWRITE) begin
            MpRDATA = SpRDATA[baseAddr];
        end
    end
```

## Developing I/O cores as APB slaves

- Main task in SoC is to integrate the custom logic into the system

- To attach the custom logic to the bus and access I/O core, the following steps are required:
  1. Add a wrapper around the custom logic to form a compatible I/O core that can interact with the bus
  2. Update system-level decoding (write operation) and multiplexing (read operation) logic to circuit to indetify and access the I/O core

- Wrapping circuit : 
  - enables core to be addressed by the bus
  - makes the core as a small memory-mapped device
  - contains:
    - decoding circuit for writing into the registers
    - multiplexing circuit for reading from the registers

-  System-level decoding and multiplexing logic:
   - used to identify the I/O core
   - multiplexes the data bus to read the data from the I/O core

### Developing APB slave 

- needs to be in compliance with the slot interface

- For this example, we will develop a simple APB slave that represents a memory-mapped 64-bit timer peripheral
  
- The timer has three registers:
    - Configuration register (offset 0x00): used to configure the timer
      - write-only register
      - bit 0: enable/disable the timer
      - bit 1: reset the timer
    - Count low register (offset 0x04): contains the lower 32 bits of the timer count
      - read-only register
    - Count high register (offset 0x08): contains the upper 32 bits of the timer count
      - read-only register

- In this example, we presume that:
  - Writing to the peripheral requires one cycle
  - Reading from the peripheral requires two cycles (one cycle to set up the read and one cycle to read the data)
  
- The design of the APB slave includes:
  - Wrapping logic to interface with the APB bus
    - State machine to manage the read and write operations
    - Decoding logic to select the appropriate register based on the address
  - Logic that implements the timer functionality
  - Logic to generate the APB slave error signal


#### State machine implementation

- Define the state machine for the APB slave
```verilog
typedef enum logic [1:0] {
    IDLE,
    READ,
    WRITE,
    R_FINISH
} state_apb;
```
- Transition between states
```verilog
    // State Transition
    always_ff @(posedge pCLK) begin
        if (!pRESETn) begin
            state <= IDLE;
        end
        else begin
            state <= next_state;
        end
    end
```

- Next state logic
```verilog
    // Next State Logic
    always_comb begin
        next_state = state;
        pREADY_next = 1'b0;
        case (state)
            IDLE: begin
                if (read_req) begin
                    next_state = READ;
                end else if (write_req) begin
                    pREADY_next = 1'b1;
                    next_state = WRITE;
                end
            end
            WRITE: begin
                pREADY_next = 1'b0;
                next_state = IDLE;
            end
            READ: begin
                pREADY_next = 1'b1;
                next_state = R_FINISH;
            end
            
            R_FINISH: begin
                next_state = IDLE;
                pREADY_next = 1'b0;
            end
        endcase
    end
```


- Decode the state to generate the APB signals

```verilog
    // Next State Logic
    always_comb begin
        next_state = state;
        pREADY_next = 1'b0;
        case (state)
            IDLE: begin
                if (read_req) begin
                    next_state = READ;
                end else if (write_req) begin
                    pREADY_next = 1'b1;
                    next_state = WRITE;
                end
            end
            WRITE: begin
                pREADY_next = 1'b0;
                next_state = IDLE;
            end
            READ: begin
                pREADY_next = 1'b1;
                next_state = R_FINISH;
            end
            
            R_FINISH: begin
                next_state = IDLE;
                pREADY_next = 1'b0;
            end
        endcase
    end
```

#### Register decoding logic

- Write logic to decode the address and write to the appropriate register

```verilog
    always_ff @(posedge pCLK) begin
        if (!pRESETn) begin
            config_reg <= 64'b0;
        end
        else begin
            if (state == WRITE && pENABLE) begin
                case (pADDR[6:0])
                    `CONF: config_reg <= pWDATA;
                    default: ;
                endcase
            end
        end
    end
```

- Read logic to decode the address and read from the appropriate register

```verilog
    always_ff @(posedge pCLK) begin
        if (!pRESETn) begin
            pRDATA <= 32'b0;
        end else begin
            if (state == R_FINISH && pENABLE) begin
                case (pADDR[6:0])
                    `COUNT_L: pRDATA <= count_reg[31:0];
                    `COUNT_H: pRDATA <= count_reg[63:32];
                    default:  pRDATA <= 32'b0;
                endcase
            end else begin
                pRDATA <= 32'b0;
            end
        end
    end
```

#### Timer functionality

```verilog
    always_ff @(posedge pCLK) begin
        if (!pRESETn) begin
            count_reg <= 64'b0;
        end else begin
            if(config_reg[0]) begin
                if(config_reg[1]) begin
                    count_reg <= 0;
                end else begin
                    count_reg <= count_reg + 1;
                end
            end
        end
    end
```

#### Generating APB slave error signal 

- In this example, we generate the slave error signal if we read from a read only register or write to a write-only register

```verilog
    // write fail, occurs when we write to an invalid address
    logic write_fail;
    assign write_fail = (pENABLE && pREADY && pSEL && write_req && pADDR[6:0] != `CONF) ? 1'b1 : 1'b0;

    // read fail, occurs when we read an invalid address
    logic read_fail;
    assign read_fail = (pENABLE && pREADY && pSEL && read_req && pADDR[6:0] != `COUNT_HIGH && pADDR[6:0] != `COUNT_LOW ) ? 1'b1 : 1'b0;

    assign pSLVERR = write_fail | read_fail;
```