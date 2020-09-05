derived from https://github.com/eblot/pyftdi/

bit indices are 0-based

## USB IDs

FTDI VID: `0x0403`

 PID | Products
-----|----------
6001 | FT232, FT232R
6010 | FT2232, FT2232C, FT2232D, FT2232H
6011 | FT4232, FT4232H
6015 | FT-X, FT230X, FT231X, FT234X

### Device Properties

TX, RX are the on-chip FIFO sizes.

 bcdDevice |  TX  |  RX  | MPSSE | Model
-----------|------|------|-------|-------
 2.00      |  128 |  128 | -     | FT232AM
 4.00      |  128 |  384 | -     | FT232BM
 5.00      |  128 |  384 | yes   | FT2232C
 6.00      |  256 |  128 | -     | FT232R
 7.00      | 4096 | 4096 | yes   | FT2232H
 8.00      | 2048 | 2048 | yes   | FT4232H
 9.00      | 1024 | 1024 | yes   | FT232H
10.00      |  512 |  512 | -     | FT-X

## Modem status

16-bit value

Position | Name | Description
---------|------|------------
1-3      | ???  |
4        | CTS  | Clear to send
5        | DSR  | Data set ready
6        | RI   | Ring indicator
7        | DCD  | Data carrier detect
8        | DR   | Data ready
9        | OE   | Overrun error
10       | PE   | Parity error
11       | FE   | Framing error
12       | BI   | Break interrupt
13       | THRE | Transmitter holding register empty
14       | TEMT | Transmitter empty
15       | ERR  | Error in RCVR FIFO

## Bitmode

1 Byte. These have bitflags-like values but actually are not.

Value |  Name   | Description
------|---------|------------
 0x00 | RESET   | switch off alternative mode (default to UART)
 0x01 | BITBANG | classical asynchronous bitbang mode
 0x02 | MPSSE   | MPSSE mode, available on 2232x chips
 0x04 | SYNCBB  | synchronous bitbang mode
 0x08 | MCU     | MCU Host Bus Emulation mode
 0x10 | OPTO    | Fast Opto-Isolated Serial Interface Mode
 0x20 | CBUS    | Bitbang on CBUS pins of R-type chips
 0x40 | SYNCFF  | Single Channel Synchronous FIFO mode
 0x80 | ???     | ???

## Control requests

These are the `bRequest` values accepted by the control endpoint:

Value |           Name            | Description
------|---------------------------|-------------
 0x00 | SIO_REQ_RESET             | Reset the port
 0x01 | SIO_REQ_SET_MODEM_CTRL    | Set the modem control register
 0x02 | SIO_REQ_SET_FLOW_CTRL     | Set flow control register
 0x03 | SIO_REQ_SET_BAUDRATE      | Set baud rate
 0x04 | SIO_REQ_SET_DATA          | Set the data characteristics of the port
 0x05 | SIO_REQ_POLL_MODEM_STATUS | Get line status
 0x06 | SIO_REQ_SET_EVENT_CHAR    | Change event character
 0x07 | SIO_REQ_SET_ERROR_CHAR    | Change error character
 0x08 | ???                       | ???
 0x09 | SIO_REQ_SET_LATENCY_TIMER | Change latency timer
 0x0A | SIO_REQ_GET_LATENCY_TIMER | Get latency timer
 0x0B | SIO_REQ_SET_BITMODE       | Change bit mode
 0x0C | SIO_REQ_READ_PINS         | Read GPIO pin value (or "get bitmode")
  ... | ???                       | ???
 0x90 | SIO_REQ_READ_EEPROM       | Read EEPROM content
 0x91 | SIO_REQ_WRITE_EEPROM      | Write EEPROM content
 0x92 | SIO_REQ_ERASE_EEPROM      | Erase EEPROM content

### `SIO_REQ_RESET`

`wValue` parameter:

Value | Name | Description
------|------|-----------
0     | SIO_RESET_SIO | Reset without draining buffers
1     | SIO_RESET_PURGE_RX | Drain USB RX buffer (host-to-ftdi)
2     | SIO_RESET_PURGE_TX | Drain USB TX buffer (ftdi-to-host)

### `SIO_REQ_SET_MODEM_CTRL`

Sets DTR and/or RTS.

`wValue` parameter:

 Value |       Name       | Description
-------|------------------|------------
0x0101 | SIO_SET_DTR_HIGH |
0x0100 | SIO_SET_DTR_LOW  |
0x0202 | SIO_SET_RTS_HIGH |
0x0200 | SIO_SET_RTS_LOW  |

MSB contains the bits to modify, LSB contain the bits to set them it, so combinations of these likely work too (ie. `0x0301` to set DTR and clear RTS).

### `SIO_REQ_SET_FLOW_CTRL`

`wValue` parameter:

 Value |         Name          | Description
-------|-----------------------|----------
0x0000 | SIO_DISABLE_FLOW_CTRL | No flow control
0x0100 | SIO_RTS_CTS_HS        | RTS/CTS flow control
0x0200 | SIO_DTR_DSR_HS        | DTR/DSR flow control
0x0400 | SIO_XON_XOFF_HS       | XON/XOFF flow control

### `SIO_REQ_SET_BAUDRATE`

TODO: baud rate conversion formulas
