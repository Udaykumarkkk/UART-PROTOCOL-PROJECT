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


📂 Files Included
UART_tx.sv – UART Transmitter module
<img width="822" height="578" alt="tx" src="https://github.com/user-attachments/assets/329b31a8-896f-44d5-98b1-c70863085501" />

UART_rx.sv – UART Receiver module
<img width="867" height="807" alt="RX" src="https://github.com/user-attachments/assets/c542a0c8-ef77-4388-a563-fe2cd6cde2aa" />

UART_top.v – Top-level module connecting TX and RX
<img width="685" height="812" alt="top" src="https://github.com/user-attachments/assets/b26ec69a-dd20-4610-ae11-d29f0f056b85" />

UART_tb.sv – Testbench to simulate the UART protocol
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
