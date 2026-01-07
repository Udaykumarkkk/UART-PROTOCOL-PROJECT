# UART-PROTOCOL-PROJECT
ART Protocol Project implements a custom reliable data transmission protocol with packet framing, sequencing, ACK/NACK handling, and retransmission support. The project demonstrates protocol design, RTL implementation, and simulation, making it suitable for embedded systems, FPGA, and VLSI communication learning applications.


# UART_project
A simple and complete UART protocol design in Verilog with transmitter, receiver, and testbench. Simulated using Vivado. Ideal for learning serial communication design and HDL simulation. Sure! Here's a detailed and polished README.md description for your UART project on GitHub:

# BLOCK DIAGRAM
<img width="1073" height="575" alt="diagram" src="https://github.com/user-attachments/assets/c80fc747-65e4-4dd0-be6c-24ffbde83b9a" />


🟢 UART Protocol in Verilog
This repository contains a complete Verilog implementation of the UART (Universal Asynchronous Receiver/Transmitter) protocol. It includes both the transmitter (TX) and receiver (RX) modules along with a testbench to verify functionality. Designed and simulated using Xilinx Vivado.

📌 Features
✅ Fully synthesizable UART TX and RX modules
✅ Baud rate generator with configurable parameters
✅ Simple FSM-based design for both TX and RX
✅ 1 start bit, 8 data bits, 1 stop bit format
✅ Testbench to verify TX → RX functionality
✅ Written in clean, modular Verilog/System Verilog HDL
✅ Simulated and verified in Vivado

🧠 Parameters
Parameter       	Description             	Default:
clk_freq	     Clock frequency in Hz      	1000000
baud_rate	      UART baud rate	             9600

# SYNTHESIS DIAGRAM
<img width="1302" height="857" alt="synthesis" src="https://github.com/user-attachments/assets/81b78917-3a12-4ebc-85e6-56531b238586" />


📂 Files Included
UART_tx.sv – UART Transmitter module
# code
module uart_tx(
    input clk, baud_tick,
    input tx_start,
    input [7:0] tx_data,
    output reg tx,
    output reg tx_done
);
    reg [3:0] bit_cnt;
    reg [9:0] shift;
    always @(posedge clk) begin
        if (tx_start) begin
            shift <= {1'b1, tx_data, 1'b0}; // stop,data,start
            bit_cnt <= 0;
            tx_done <= 0;
        end else if (baud_tick) begin
            tx <= shift[0];
            shift <= shift >> 1;
            bit_cnt <= bit_cnt + 1;
            if (bit_cnt == 9)
                tx_done <= 1;
        end
    end
endmodule
<img width="822" height="578" alt="tx" src="https://github.com/user-attachments/assets/329b31a8-896f-44d5-98b1-c70863085501" />

UART_rx.sv – UART Receiver module
#code
module uart_rx(
    input clk, baud_tick,
    input rx,
    output reg [7:0] rx_data,
    output reg rx_done
);
    reg [3:0] bit_cnt;
    reg [7:0] shift;
    always @(posedge clk) begin
        if (!rx && bit_cnt == 0) begin
            bit_cnt <= 1; // start detected
        end else if (baud_tick && bit_cnt > 0) begin
            shift <= {rx, shift[7:1]};
            bit_cnt <= bit_cnt + 1;
            if (bit_cnt == 8) begin
                rx_data <= shift;
                rx_done <= 1;
                bit_cnt <= 0;
            end
        end
    end
endmodule

<img width="867" height="807" alt="RX" src="https://github.com/user-attachments/assets/c542a0c8-ef77-4388-a563-fe2cd6cde2aa" />

UART_top.v – Top-level module connecting TX and RX
#code
module uart_top(
    input clk, reset,
    input rx,
    input tx_start,
    input [7:0] tx_data,
    output tx,
    output [7:0] rx_data,
    output rx_done
);
    wire baud_tick;
    baud_gen bg(clk, reset, baud_tick);
    uart_tx tx1(clk, baud_tick, tx_start, tx_data, tx, );
    uart_rx rx1(clk, baud_tick, rx, rx_data, rx_done);
endmodule
<img width="685" height="812" alt="top" src="https://github.com/user-attachments/assets/b26ec69a-dd20-4610-ae11-d29f0f056b85" />

UART_tb.sv – Testbench to simulate the UART protocol
#testbench code
`timescale 1ns/1ps

module uart_tb;

  // -------------------------
  // Parameters
  // -------------------------
  parameter CLK_PERIOD = 20;     // 50 MHz clock
  parameter BAUD_RATE  = 9600;
  parameter BIT_TIME   = 104160; // 1/9600 sec in ns

  // -------------------------
  // Signals
  // -------------------------
  logic clk;
  logic rst_n;
  logic rx;
  logic tx;
  logic [7:0] tx_data;
  logic tx_start;
  logic tx_busy;
  logic [7:0] rx_data;
  logic rx_done;

  // -------------------------
  // DUT Instantiation
  // -------------------------
  uart_top dut (
    .clk(clk),
    .rst_n(rst_n),
    .rx(rx),
    .tx(tx),
    .tx_data(tx_data),
    .tx_start(tx_start),
    .tx_busy(tx_busy),
    .rx_data(rx_data),
    .rx_done(rx_done)
  );

  // -------------------------
  // Clock Generation
  // -------------------------
  always #(CLK_PERIOD/2) clk = ~clk;

  // -------------------------
  // UART RX Stimulus Task
  // -------------------------
  task uart_rx_send(input [7:0] data);
    integer i;
    begin
      // Start bit
      rx = 0;
      #(BIT_TIME);

      // Data bits (LSB first)
      for (i = 0; i < 8; i++) begin
        rx = data[i];
        #(BIT_TIME);
      end

      // Stop bit
      rx = 1;
      #(BIT_TIME);
    end
  endtask

  // -------------------------
  // Initial Block
  // -------------------------
  initial begin
    // Initialize
    clk = 0;
    rst_n = 0;
    rx = 1;
    tx_data = 0;
    tx_start = 0;
    // Reset
    #100;
    rst_n = 1;
    // -------------------------
    // Test Case 1: RX Test
    // -------------------------
    $display("UART RX Test Started");
    uart_rx_send(8'h55);
    wait(rx_done);
    $display("RX Data Received = %h", rx_data);
    // -------------------------
    // Test Case 2: TX Test
    // -------------------------
    #200000;
    $display("UART TX Test Started");
    tx_data  = 8'hA3;
    tx_start = 1;
    #40;
    tx_start = 0;
    wait(!tx_busy);
    $display("TX Completed");
    // -------------------------
    // Finish
    // -------------------------
    #200000;
    $stop;
  end

endmodule

<img width="581" height="453" alt="tb1" src="https://github.com/user-attachments/assets/2ac334d8-de22-49cd-a880-912e1a69746f" />

<img width="456" height="593" alt="tb2" src="https://github.com/user-attachments/assets/1dc98aec-0b84-4d43-8187-b0d2d262d16e" />

<img width="467" height="565" alt="tb3" src="https://github.com/user-attachments/assets/c75c6bd1-1cd6-47a5-9371-e523b0c297a1" />

<img width="640" height="652" alt="tb4" src="https://github.com/user-attachments/assets/e7abff0b-2ee2-4a1f-9848-89eac89ab388" />



▶️ Simulation
The testbench does the following:

Sends random 8-bit data using the TX module.
Captures serial output and verifies it at RX.
Logs sent and received data to monitor correctness.



<img width="1913" height="646" alt="wave form" src="https://github.com/user-attachments/assets/32d9a645-3779-40f3-826b-21766785ed94" />
