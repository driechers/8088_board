IBM PC Compatable memory and io

power supply on ibmpc is 7A 5V 2A 12v. Use 24 pin ATX power supply. I have  40A 5v, 25A 12v and more!

256k eeprom
32K extra eeprom (16k for bios location, 16k for unused memroy location
256k sram
Interrupt Controller 8259
System Timer 8253
8255 for debug / compat
8088 Debug Breakout traces if easy in eda

No DMA controller for now breakout traces for isa signals instead

clock 14.31818 /3A = 4.787 for uP and ISA expander 8284
8bit ISA expansion slot
	match system bus latches in ibmpc
	provision for 8288 bus controller
	add jumper traces to select between demultiplexed bus config and fully buffered system with 8288
	Breakout wait state signals
	tranceiver just latch? 8286?

UART
	8252 uart should be sw compatable with 8250 
		use 0x03F8-0x03FF and irq 4


# Connectors

24 pin ATX connector
3 +? pin female uart. Compatable with existing cable. Fully break out 8252 interface
8 Bit ISA connector
EEPROM ZIF connector
Custom debug connectors
	24 pin system bus
	12 pin 8255 ports
	?? pin wait signals
Bus mode jumpers 3?

# SAM

0x00000 - 0x3C000 256k SRAM provisioned for 8 61256 / 84256 / AS6C62256 (all same pinout)
0xC0000 - 0xFBFFF 256k EEPROM provisioned for 8 AT28C256 chips
0xFC000 - 0xFDFFF 8k EEPROM
	socket for AT28C64B 28pin ZIF and make a personality module for X2816AD for test
	causing it to repeat in memory 4 times
	sacraficial if too complex
0xFE000 - 0xFFFFF 8k EEPROM
	socket for AT28C64B 28pin ZIF and make a personality module for X2816AD for test
	causing it to repeat in memory 4 times

	Can I simplify demux by using 256k eeproms and switches to disable MSBs
	of 2nd eeprom to make it act like 16k in compatability mode?
	perhaps just use lower bits of AT28c256 and use another bit somewere also slected by demux or just leave disconnected?

# IO Address Map
0x020 - 0x021 Interrupt controller 8259A
0x040 - 0x043 Timer 8253
0x060 - 0x063 PPI 8255
0x3F8 - 0x3FF 8252

Expansion unit?


# BOM

## Base Components

1 - 8088
1 - 8259 Interrupt controller
1 - 8253 Timer
1 - 8255 PPI Port
1 - 8288 bus controller (provisioned)
8 - 61256 SRAM 4 84256 and leave 4 provisioned
9 - AT28C256 EEPROM

## IO Selection

A[15:7] | A6 | A5 | A4 | A3 | A2 | A1 | A0 | 8259 CS | 8253 CS | 8255 CS 
0       |  0 |  0 |  0 |  0 |  0 |  0 |  0 |        1|        1|       1
0       |  0 |  0 |  0 |  0 |  0 |  0 |  1 |        1|        1|       1
0       |  0 |  0 |  0 |  0 |  0 |  1 |  0 |        1|        1|       1
0       |  0 |  0 |  0 |  0 |  0 |  1 |  1 |        1|        1|       1

0       |  0 |  1 |  0 |  0 |  0 |  0 |  0 |        0|        1|       1
0       |  0 |  1 |  0 |  0 |  0 |  0 |  1 |        0|        1|       1
0       |  0 |  1 |  0 |  0 |  0 |  1 |  0 |     1->0|        1|       1 - Changed to dont care and use line decoder
0       |  0 |  1 |  0 |  0 |  0 |  1 |  1 |     1->0|        1|       1 - Changed to dont care and use line decoder

0       |  1 |  0 |  0 |  0 |  0 |  0 |  0 |        1|        0|       1
0       |  1 |  0 |  0 |  0 |  0 |  0 |  1 |        1|        0|       1
0       |  1 |  0 |  0 |  0 |  0 |  1 |  0 |        1|        0|       1
0       |  1 |  0 |  0 |  0 |  0 |  1 |  1 |        1|        0|       1

0       |  1 |  1 |  0 |  0 |  0 |  0 |  0 |        1|        1|       0
0       |  1 |  1 |  0 |  0 |  0 |  0 |  1 |        1|        1|       0
0       |  1 |  1 |  0 |  0 |  0 |  1 |  0 |        1|        1|       0
0       |  1 |  1 |  0 |  0 |  0 |  1 |  1 |        1|        1|       0

2 - 4078 8input nors for A15:7
1 - 4011 NAND to combine 4078, provide inverter and feed E1 and E2 innverted by IO/M
1 - 74154 4 to 16 line decoder. on A[8:5] Break out all unused signals to custom expander for future dma and other ports
	TODO feed F to CS of 8250 and add logic to E1
	TODO run over logic

4012 2 4 input nand
8 input not


## Memory selection
1 - 4011 quad NAND
    2 - NAND gates (active low ce) 1 for ramen for A[19:18] == 00 and one for romen for A[19:18] == 11
    2 - not gates for romen A[19:18]
2 - 74154, LS or HC? 4 to 16 line decoder to select chip chip using A[17:14] E1 connected to IO/M  E2 connected to ROMEN / RAMEN


4011 Nands for sure needed


Chips to buy:
SRAM
EEPROM
8288 bus controller
