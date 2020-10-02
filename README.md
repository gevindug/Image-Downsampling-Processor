# Image-Downsampling-Processor
Design and Implementation of RISC related processor using VERILOG HDL and XILINX SPARTAN-6 FPGA


## compileme.py
a little-higher level asm compiler (don't rely solely on this for error detection)

usage:

python compileme.py <input file name(required)> <output file names(optional)>

#### Features:

1. jump statements can be used with labels as destination
2. immediate constants for add, sub, mul and div instructions will be resolved automatically
3. immediate addressing for load and store instructions will be resolved automatically
4. uartsend can be directly used to send a string of characters

#### example:

load 4096

:apple add r1 129

jmpdec :apple

uartsend :hi

#### will compile into

move mbr r15 //0.load 4096

move ac r14

move zr ac

add zr 1

shl 12

move ac mbr

load 0

move r15 mbr

move r14 ac //end

add r1 2 //1.:apple add r1 256

add zr 127

add zr 127 //end

jmpdec 9

move ac r15 //3.uartsend :hi

move zr ac

add zr 104

move ac uarttx

uartsend

nop

move zr ac

add zr 105

move ac uarttx

uartsend

nop

move r15 ac //end


## processor_top_module.v
top module of the processor. should include two block rams;
1. iram->16x4096 bits (12 bit address width)
2. dram->8x131072 bits (17 bit address width)

## ISA
### INSTRUCTIONS

1. NOP ------> NO OPERATION ------> 0000
2. ADD[R][CONST] ------> AC<-AC+([R]+[CONST]) ------> 0001
3. SUB[R][CONST] ------> AC<-AC-([R]+[CONST]) ------> 0010
4. MUL[R][CONST] ------> AC<-AC*([R]+[CONST]) ------> 0011
5. DIV[R][CONST] ------> AC<-AC/([R]+[CONST]) ------> 0100
6. SHR<8'b0>[N] ------> SHIFT RIGHT N BITS ------> 0101
7. SHL<8'b0>[N] ------> SHIFT LEFT N BITS ------> 0110
8. LOAD[M] ------> MDR <-[M+2*MBR] ------> 0111
9. STORE[M] ------> [M+2*MBR] <-MDR ------> 1000
10. JUMP[INST] ------> JUMP TO [INST] ------> 1001
11. JMPZ[INST] ------> JUMP TO [INST] IF Z FLAG IS HIGH ------> 1010
12. JMPDEC[INST] ------> JUMP TO [INST] AND DECREMENT LR BY ONE IF LRZ IS LOW ------> 1011
13. MOVE[S][D] ------> [D]<-[S] ------> 1100
14. UARTSEND ------> WAIT FOR UART OUTPUT TO COMPLETE ------> 1101
15. UARTREAD ------> WAIT FOR UART INPUT TO COMPLETE ------> 1110

### DATA WIDTH:
OPCODE ------> 4 BITS

[R] ------> 5 BITS

[CONST] ------> 7 BITS

[N] ------> 4 BITS

[M] ------> 12 BITS

[INST] ------> 12 BITS

[S], [D] ------> 5 BITS


### FLAGS

1. ------> Z ------> AC IS ZERO FLAG
2. ------> LRZ ------> LR IS ZERO FLAG
3. ------> TXBUSY ------> UART TX BUSY FLAG
4. ------> RXREADY ------> UART RX READY FLAG



### REGISTERS

1. PC ------> PROGRAM COUNTER 
2. IR ------> INSTRUCTION REGISTER
3. ZR ------> ZERO REGISTER ------> 00000 
4. MBR ------> MEMORY BASE REGISTER ------> 00001
5. MDR ------> MEMORY DATA REGISTER ------> 00010
6. UARTTX ------> UART TX REGISTER ------> 00011
7. UARTRX ------> UART RX REGISTER ------> 00100
8. AC ------> ACCUMULATOR ------> 00101
9. LR ------> LOOP REGISTER ------> 00110
10. R0-R15 ------> GP REG ------> 1XXXX (XXXX from 0000 to 1111)
