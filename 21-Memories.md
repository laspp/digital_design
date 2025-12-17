#  Modeling memories with SystemVerilog

## Random-access memory 

- Key aspects of RAM modules: 
  - Depth: number of memory locations.
  - Width: number of bits in each memory location.
  - Number of ports: number of read and write ports.
  - Direction of ports: read or write.
  - Read and write operations: synchronous or asynchronous.
  - Simultaneous read and write operations 

### Synchronous dual port RAM
  - two ports for memory access.
  - each condusts read or write operation, independently from each other.
    - has its own address, data input, and data output.
    ```verilog
    module sync_dual_port_RAM #(
    parameter DATA_WIDTH = 8,
    parameter ADDR_WIDTH = 8
    ) (
        input logic clock,
        input logic [ADDR_WIDTH-1:0] addr_a,
        input logic [ADDR_WIDTH-1:0] addr_b,
        input logic [DATA_WIDTH-1:0] datain_a,
        input logic [DATA_WIDTH-1:0] datain_b,
        input logic we_a,
        input logic we_b,
        output logic [DATA_WIDTH-1:0] dataout_a,
        output logic [DATA_WIDTH-1:0] dataout_b
    );
    ```
   - memory array of words is implemented as a 2D array of logical vectors  
      ```verilog
      logic [DATA_WIDTH-1:0] mem [0:2**ADDR_WIDTH-1];
      ```
   - separate process for different ports 
     - check if write enable signal is high, then write the data to the memory array.
     - read the data from the memory array and assign it to the output.
     - Dynamic index is used to access the memory array. 
  
      ```verilog
      always_ff @(posedge clock) begin
      if (we_a) begin
          mem[addr_a] <= datain_a;
      end
        dataout_a <= mem[addr_a];
      end

      always_ff @(posedge clock) begin
          if (we_b) begin
              mem[addr_b] <= datain_b;
          end
          dataout_b <= mem[addr_b];
      end
      ```
  - Initialize the array with the data from the file.
    - $readmemh() function is used to read the data from the file and store it in the memory array.
    - The data is stored in hexadecimal format in the file.
    - The memory array is initialized with the data from the file in the initial block.
    - The memory array is accessed using the address from the input port.
    - The data from the memory array is assigned to the output port.
    ```verilog
      initial begin
          $readmemh("ram_init.txt", mem);
      end
    ```
    
### Simple dual-port RAM

  - two ports: one for read and one for write.
  - Interface:
    ```verilog
    module sync_simple_dual_port_RAM #(
        parameter DATA_WIDTH = 8,
        parameter ADDR_WIDTH = 8
    )  (
        input logic clock,
        input logic [ADDR_WIDTH-1:0] addr_r,
        input logic [ADDR_WIDTH-1:0] addr_w,
        input logic [DATA_WIDTH-1:0] datain,
        input logic we,
        output logic [DATA_WIDTH-1:0] dataout
    );
    ```
    
  - One process for both ports.
    ```verilog
    always_ff @(posedge clock) begin
        if (we) begin
            mem[addr_w] <= datain;
        end
        dataout <= mem[addr_r];
    end
    ```
        
### Single-port RAM

  - one port for read and write.
  - Interface:
    ```verilog
    module sync_single_port_RAM #(
        parameter DATA_WIDTH = 8,
        parameter ADDR_WIDTH = 8
    )  (
        input logic clock,
        input logic [ADDR_WIDTH-1:0] addr,
        input logic [DATA_WIDTH-1:0] datain,
        input logic we,
        output logic [DATA_WIDTH-1:0] dataout
    );
    ```
    
  - One process for both read and write operations.
    ```verilog
    always_ff @(posedge clock) begin
        if (we) begin
            mem[addr] <= datain;
        end
        dataout <= mem[addr];
    end
    ```
  

## Read-only memory

- Read-only memory is a memory that can only be read.
  - pure combinational circuit.
  - emulated with combinational logic.
    - the real ROM is implemented with transistors.
- Two types of ROMs:
  - Lookup table: stores the output values for all possible input values.
  - Example: 7-segment display ROM.
    - The 7-segment display ROM stores the output values for all possible input values.
    - The input values are the 4-bit binary numbers from 0 to 15.
    - The output values are the 7-bit binary numbers that represent the segments of the 7-segment display.
    - The 7-segment display ROM is implemented as a case statement that maps the input values to the output values.
    ```verilog
    module rom_case(
        input logic [3:0] addr,
        output logic [6:0] data
    );
    always_comb begin : 7segDisplay
            case (addr)
                4'h0: data = 7'b1000000; // 0
                4'h1: data = 7'b1111001; // 1
                4'h2: data = 7'b0100100; // 2
                4'h3: data = 7'b0110000; // 3
                4'h4: data = 7'b0011001; // 4
                4'h5: data = 7'b0010010; // 5
                4'h6: data = 7'b0000010; // 6
                4'h7: data = 7'b1111000; // 7
                4'h8: data = 7'b0000000; // 8
                4'h9: data = 7'b0010000; // 9
                4'ha: data = 7'b0001000; // A
                4'hb: data = 7'b0000011; // b
                4'hc: data = 7'b1000110; // C
                4'hd: data = 7'b0100001; // d
                4'he: data = 7'b0000110; // E
                4'hf: data = 7'b0001110; // F
                default: data = 7'b1111111; // off
            endcase
    end
    endmodule
    ```
  - Content-addressable memory: stores the output values for a subset of input values.
    
    ```verilog
        logic [DATA_WIDTH-1:0] hex_rom [0:2**ADDR_WIDTH-1];
        
        // read content from file and store in memoryß
        initial begin
            $readmemh("rom_init.txt", hex_rom);
        end

        assign dataout_a = hex_rom[addr_a];ß
    ```