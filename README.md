# Universal J-Link Adapter

![Shortcut](https://img.shields.io/badge/website-adapter.brechtve.be-yellow)
![License](https://img.shields.io/badge/licence-CERN%20OHL%20v.1.2-blue)
![GitHub last commit](https://img.shields.io/github/last-commit/Fescron/universal-jlink-adapter.svg)
![GitHub Release Date](https://img.shields.io/github/release-date/Fescron/universal-jlink-adapter.svg)
![GitHub release](https://img.shields.io/github/release/Fescron/universal-jlink-adapter.svg)

<br/>

<img src="documentation/pictures/v4/jlink-adapter-v4.png" alt="Universal J-Link adapter on J-Link EDU debugger">

<br/>

| Quick access links                                                                                                                                                                        |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adapter v4.0 **schematic** (PDF)](hardware/jlink-tagConnect-adapter-v4/jlink-tagConnect-adapter-v4.pdf) // [Adapter v4.0 **gerber-files**](hardware/jlink-tagConnect-adapter-v4/Gerber/) |
| [Basic **J-Link Commander** usage and using **Segger Real-Time Transfer (RTT)** (instead of UART) for real-time debugging](JLinkExe-SeggerRTT-debugging-TipsTricks.md)                    |

<br/>

<img align="right" src="documentation/pictures/v4/pcb-back-v4.png" height="500" alt="Back of the adapter board">

This multifunctional adapter board, primarily designed for use with a [J-Link debugger](https://www.segger.com/products/debug-probes/j-link/) (but others which follow the standard 20-pin JTAG/SWD pinout also work), allows the user to have the standard [9 pin Cortex-M SWD/JTAG pinout](https://www.segger.com/products/debug-probes/j-link/accessories/adapters/9-pin-cortex-m-adapter/) on both a 0.1" (2.54mm) and 0.05" (1.27mm) male pinheader. With additional jumpers it is possible to have **extra functionality** like **UART debugging though the J-Link's VCOM** on pins which normally aren't used in some modes, if the probe's hardware supports this. Good **labeling of all pin numbers and their corresponding functionality** also help to speed-up the embedded-development process. Additional information regarding the pinout of *default* SWD-headers as well as the voltage output of the J-Link debugger are also printed on the board, along with the pin-numbers of the [10 pin Tag-Connect header](http://www.tag-connect.com/TC2050-IDC).

Furthermore, the adapter PCB can **power the target board** through the `VTREF` connection. For this to work a **DC-DC converter or LDO regulator** can be plugged into the female 3-pin socket on the adapter board to allow the J-Link's 5V-Supply to be converted to, for example 3.3V. Various 3-pin voltage conversion boards are supported due to solder-jumpers available on the back of the adapter, allowing for the `VOUT`and `GND`pins to be swapped around. The 5V voltage can also be supplied *as-is* on a header pin by changing a jumper position.

Finally a physical reset button (with optional 100 Ω series-resistor and 100 nF filter capacitor) can be used to manually pull the reset line low, even if no debugger is connected. This eliminates the need to add a reset button to the target board and thus saves valuable board space.

<img align="left" src="documentation/pictures/v2/cables.png" height="260" alt="Headers and corresponding cables">

The **0.05" (1.27mm) header** is recommended for when the target board is mostly connected to the debugger and the board in question also has a header of this size. The **0.1" (2.54mm) IDC header** is recommended for use with either regular jumper-wires or a [10 pin Tag-Connect adapter cable](http://www.tag-connect.com/TC2050-IDC). The latter allows target boards to be programmed without having to need a dedicated programming connector mounted, and thus saves on cost.

The figure on the left depicts the `TagConnect 2050` (*no-legs*) cable on the top-right, next to its corresponding footprint. Below this is a 0.05" header with its corresponding cable.

<br/>
<br/>

## 0 - Table of Contents

<img align="right" src="documentation/pictures/v4/pinout.png" height="240" alt="Headers and corresponding cables">

- [Universal J-Link Adapter](#universal-j-link-adapter)
  - [0 - Table of Contents](#0---table-of-contents)
  - [1 - Adapter functionality](#1---adapter-functionality)
    - [1.1 - Supply power to the target board (5V and/or other voltages)](#11---supply-power-to-the-target-board-5v-andor-other-voltages)
    - [1.2 - Additional functionality on "unused" pins using jumpers (and corresponding tips and tricks)](#12---additional-functionality-on-unused-pins-using-jumpers-and-corresponding-tips-and-tricks)
  - [2 - Serial flashing](#2---serial-flashing)
  - [3 - Layout guidelines for target boards](#3---layout-guidelines-for-target-boards)
  - [4 - BOM](#4---bom)
  - [5 - KiCad debug header symbols](#5---kicad-debug-header-symbols)

<br/>

## 1 - Adapter functionality

Aside from the ability to easily make connections to target boards using the well-labeled pinout and corresponding functionality of both output-headers, the following paragraphs give more information regarding additional features this adapter can provide.

<br/>

:pencil: **Note:** The pinout silkscreen has the following syntax:

- `... | ...`: The same (J-Link debugger) pin has different SWD or JTAG functionality
- `... / ...`: A jumper can be used to route one or the other signal to this pin
- `(...)`: This pin-signal deviates from the default SWD or JTAG pinout

<br/>

### 1.1 - Supply power to the target board (5V and/or other voltages)

<img align="left" src="documentation/pictures/v4/converter-pin5-headers.png" height="120" alt="Converter and pin-5 headers">

As previously stated, a **DC-DC converter or LDO regulator** can be plugged into the 3-pin female header on this adapter with the labels `JP1 JP2 5V`. This would allow the adapter board to supply power through the `VTREF`-line (`PIN 1`) to the target board with the correct voltage, derived from the **5V supply of the J-Link debugger**. The 5V voltage can **also be made available *as-is* on** `PIN 5` of the output headers, by placing a jumper in the correct position of the header labeled `GND 5 5V`. With the same jumper `GND` can also be made available on `PIN 5`. Both the voltage-converter socket and `PIN 5`-jumper are depicted on the figure on the left.

If the user wishes to **not power the target board using** `VTREF`, for example in case of it already receiving power from a battery or external supply, the **voltage converter should simply not be plugged in**. The J-Link debugger will still receive the correct I/O voltage as a logic-level reference if the MCU's supply voltage is present on `VTREF` (`PIN 1`).

<img align="left" src="documentation/pictures/v4/ldo-jumpers.png" height="120" alt="Voltage-converter pinout jumpers">

If the **pinout of the 3-pin voltage converter doesn't match** `VIN - VOUT - GND`, the last two pins can be changed around by swapping the solder joints on `JP1` and `JP2` on the bottom of the PCB. This is also also depicted on the figure on the left. If the present copper traces are cut on both jumpers and new solder connections are made on the opposite side, the pinout `VIN - GND - VOUT` will be selected.

<br/>

The **5V output needs to be enabled on the J-Link** using the [J-Link Commander](https://kb.segger.com/J-Link_Commander) software (`JLink.exe/JLinkExe`). The following command needs to be executed for the 5V supply to be always enabled:

```shell
Power On perm
```

<br/>

<img align="right" src="documentation/pictures/v4/pin5-current-jumpers.png" height="130" alt="Current-measurement jumpers">

If the target board is powered via a voltage-converter on the adapter PCB, a jumper labeled `VTREF 1` below the 0.1" header allows for a **current meter** to be inserted between the converter and target board. This way its power usage can be monitored, for example in case the J-Link probe doesn't have this functionality build-in. If 5V is supplied to the target board through `PIN 5`, a jumper in this location can also be substituted for a current meter. Both headers are depicted on the figure on the right.

<br/>

### 1.2 - Additional functionality on "unused" pins using jumpers (and corresponding tips and tricks)

In addition to the *default* SWD or JTAG pinouts it is also possible to add extra functionality to normally *unused* pins of the 02x05 headers:

<img align="right" src="documentation/pictures/v4/jumpers.png" height="180" alt="Jumpers which allow for additional functions on pins">

- :pushpin: **PIN 5** is normally connected to `GND`.
  - On the [Segger 10 pin Needle Adapter](https://www.segger.com/products/debug-probes/j-link/accessories/adapters/10-pin-needle-adapter/) this pin outputs the J-Link's `5V-Supply`. A jumper on the adapter board can be used to select this, which was already mentioned in the previous section.
  - :bulb: **TIP:** See the [following section](#2---serial-flashing) to use this pin for **serial programming**.
- :pushpin: **PIN 6** is normally used for the `SWO` or `TDO` connection. A jumper labeled `SWO TDO 6`, which can be seen on the figure on the right, can however be used to disconnect the debugger from the target board.
  - :bulb: **TIP:** Disconnecting the pin from the debugger can enable the MCU's output pin to be used as a GPIO debug signal. It can for example be set high if some code is running, and low when this is not the case. Simply remove the jumper and add a jumper-wire on the pin labeled `6`.
- :pushpin: **PIN 7** is on the [standard 9-pin Cortex-M SWD/JTAG pinout](https://www.segger.com/products/debug-probes/j-link/accessories/adapters/9-pin-cortex-m-adapter/) *not populated* since this is the **key**.
  - **SWD mode:** A jumper labeled `RTCK 7 JL.RX` can be used to connect this pin to the J-Link's **VCOM** `RX` pin. This way UART debugging can be used in conjunction with SWD.
    - :bulb: **TIP:** The jumper can also be removed, and a separate UART `TXD` signal from a target board can be connected to the J-Link's `RX` input by connecting a jumper-wire to the `JL.RX` pin.
    - :pencil: **NOTE:** It is possible VCOM needs to be enabled on the probe. This can be done using [J-Link Commander](https://kb.segger.com/J-Link_Commander) (`JLinkExe/JLink.exe`) by executing the following command (VCOM will start working after the next power-cycle):
      ```shell
      VCOM enable
      ```
  - **JTAG mode:** The same jumper can also be used to connect this pin to `RTCK` if necessary.
- :pushpin: **PIN 8**
  - **SWD mode:** Normally this pin is *not connected*, but a jumper labeled `JL.TX TDI 8` allows it to be connected to the J-Link's **VCOM** `TX` pin.
    - :bulb: **TIP:** The jumper can also be removed, and a separate UART `RXD` signal from a target board can be connected to the J-Link's `TX` output by connecting a jumper-wire to the `JL.TX` pin.
  - **JTAG mode:** On Segger's [9 pin](https://www.segger.com/products/debug-probes/j-link/accessories/adapters/9-pin-cortex-m-adapter/) and [19 pin](https://www.segger.com/products/debug-probes/j-link/accessories/adapters/19-pin-cortex-m-adapter/) Cortex-M adapters `PIN 9` is connected to `TDI`.
    - :bulb: **TIP:** The `TDI` signal of a J-Link debugger can be set *low* or *high* using **J-Link Commander** by executing `TDI0` and `TDI1` respectively, even if there is no target connection active. This signal can thus be used as a GP(I)O line to, for example, an MCU input pin.
  - :pencil: **NOTE:** `JL.TX` and `TDI` are for both modes located on the same physical J-Link pin. This means the jumper should normally not need to be disconnected when using the adapter in one or the other mode.
- :pushpin: **PIN 9**
  - **SWD mode:** Normally this pin is *not connected*. Sometimes a target board however uses this pin as `GNDdetect`, so it can (as the name implies) detect the presence of a debugger. With a jumper labeled `nTRST 9 GND` this pin can be connected to `GND` to enable this functionality.
    - :bulb: **TIP:** When `PIN 9` is connected to a microcontroller pin which can act as an output, the adapter can be used to *break-out* this pin to another instrument for, for example, code-timing analysis. Simply remove the jumper and add a jumper-wire on the pin labeled `9`.
  - **JTAG mode:** On Segger's [9 pin](https://www.segger.com/products/debug-probes/j-link/accessories/adapters/9-pin-cortex-m-adapter/) and [19 pin](https://www.segger.com/products/debug-probes/j-link/accessories/adapters/19-pin-cortex-m-adapter/) Cortex-M adapters this pin can be connected to `nTRST` using a solder jumper. On this adapter board this can be done using a regular jumper.
    - :bulb: **TIP:** The `nTRST` signal of a J-Link debugger can be set *low* or *high* using **J-Link Commander** by executing `TRST0` and `TRST1` respectively, even if there is no target connection active. This signal can thus be used as a GP(I)O line to, for example, an MCU input pin.
- :pushpin: **PIN 10** is used to reset the target board. A jumper is present to disconnect this line if necessary.
  - :bulb: **TIP:** The `nRESET` signal of a J-Link debugger can be set *low* or *high* using **J-Link Commander** by executing `R0` and `R1` respectively, even if there is no target connection active.<br/><img align="right" src="documentation/pictures/v4/reset-circuit.png" alt="Reset button circuit">
  - An `nRESET` button is also present on the adapter board to manually pull the reset line low. An optional (100 Ω) series-resistor `R1` and (100 nF) filter capacitor C1 can be mounted on the bottom of the board, both in the 0603 package. They are depicted on the figure on the right. To *activate* the functionality of `R1` jumper `JP10` has to be cut.

<br/>

:bulb: **TIP:** `ShowHWStatus` or `ST` can be executed in **J-Link Commander** to show the current `VTREF` voltage as well as the signal-level of the `SWCLK|TCK`, `TDI`, `SWO|TDO`, `SWDIO|TMS`, `nRESET` (`TRES`) and `nTRST` pins, even if there is no target connection active.

Additionally a two-pin `GND` header, next to the `JL.TX TDI 8` jumper, also facilitates some additional ground-wire connections for general debug connections.

<br/>

<img src="documentation/pictures/v4/adapter.png" alt="Front of the adapter board">

<br/>

## 2 - Serial flashing

<img align="right" src="documentation/pictures/v4/serial-flashing.png" height="270" alt="Universal J-Link Adapter Board and Macropedal for Serial Flashing">

Due to the availability of the jumpers on the adapter PCB certain pins can also be used to detect if a target board is plugged in. A board such as the [Macropedal](https://github.com/Fescron/macropedal) can for example be used to check if `pin 5` is, via a target board, connected to ground. If this is the case, the *Macropedal* can enter a command in a terminal to flash firmware to a board. This way one after the other board can be programmed in a **series production** scenario. The target board can even be powered by the adapter itself for a complete *stand-alone* setup. The figure on the right depicts such an arrangement. Refer to [Segger J-Link debugging tips & tricks - 2 - Scripting](JLinkExe-SeggerRTT-debugging-TipsTricks.md#2---scripting) for more information.

<br/>

## 3 - Layout guidelines for target boards

It is advised to add the following passives to the *target board* for protection and stability. This is also mentioned on the [adapter schematic](hardware/jlink-tagConnect-adapter-v4/jlink-tagConnect-adapter-v4.pdf).

- 10 kΩ pullup on `SWDIO|TMS`, `TDO` and `TDI` (and the target board's `RXD` line)
- 10 kΩ pulldown on `SWCLK|TCK`
- 33 to 100 Ω in-line current-limiting resistors on `TXD` and `RXD` lines
  - This can be important for when UART is available on a (0.1") external header, and is less important if this is made available on the SWD-header. 100 pF capacitors (*after* the series resistors) can also help with some filtering and ESD mitigation.

<br/>

## 4 - BOM

| Component              | Footprint                                                | Manufacturer `Partnumber`      | Ordering                                                                                                                 |
| ---------------------- | -------------------------------------------------------- | ------------------------------ | ------------------------------------------------------------------------------------------------------------------------ |
| J1                     | 02x10 0.1" (2.54mm) Right Angle Keyed Female IDC Socket  | Sullins `SFH11-PBPC-D10-RA-BK` | [digikey.be](https://www.digikey.be/product-detail/en/sullins-connector-solutions/SFH11-PBPC-D10-RA-BK/S9205-ND/1990098) |
| J2                     | 02x05 0.1" Male Key-Shrouded IDC Header                  | Samtec `TST-105-01-L-D`        | [digikey.be](https://www.digikey.be/en/products/detail/samtec-inc/TST-105-01-L-D/2685818)                                |
| J3                     | 02x05 0.05" (1.27mm) Male Key-Shrouded Header            | Samtec `FTSH-105-01-L-D-K`     | [digikey.be](https://www.digikey.be/en/products/detail/samtec-inc/FTSH-105-01-L-D-K/2649979)                             |
| J4                     | 01x03 0.1" Female IDC Socket                             | Samtec `SSW-103-01-G-S`        | [digikey.be](https://www.digikey.be/en/products/detail/samtec-inc/SSW-103-01-G-S/1112104)                                |
| J5, JP4, JP5, JP8, JP9 | 01x02 0.1" Male IDC Header                               | Samtec `TSW-102-07-L-S`        | [digikey.be](https://www.digikey.be/en/products/detail/samtec-inc/TSW-102-07-L-S/1101425)                                |
| JP3, JP6, JP7          | 01x03 0.1" Male IDC Header                               | Samtec `TSW-103-07-L-S`        | [digikey.be](https://www.digikey.be/en/products/detail/samtec-inc/TSW-103-07-L-S/1101424)                                |
| SW1                    | 6.5mm Pitch Through-hole Switch                          | C&K `PTS636SL43 LFS`           | [digikey.be](https://www.digikey.be/en/products/detail/c-k/PTS636SL43-LFS/10071714)                                      |
| R1                     | 100 Ω 0603 1% (1 kΩ should also work)                    | Yageo `RC0603FR-07100RL`       | [digikey.be](https://www.digikey.be/en/products/detail/yageo/RC0603FR-07100RL/726888)                                    |
| C1                     | 100 nF 0603 10% 50V X7R                                  | Yageo `CC0603KRX7R9BB104`      | [digikey.be](https://www.digikey.be/en/products/detail/yageo/CC0603KRX7R9BB104/2103082)                                  |
|                        | 2.54mm Pitch Jumpers                                     | Samtec `SNT-100-BK-G`          | [digikey.be](https://www.digikey.be/en/products/detail/samtec-inc/SNT-100-BK-G/1756763)                                  |
|                        | 02x05 0.05" IDC Cable, 6 inch (15 cm) Long               | Samtec `FFSD-05-D-06.00-01-N`  | [digikey.be](https://www.digikey.be/en/products/detail/samtec-inc/FFSD-05-D-06-00-01-N/1106577)                          |
|                        | TagConnect 10 pin IDC to Needles Without Legs (`...-NL`) | TagConnect `TC-2050-IDC-NL`    | [digikey.be](https://www.digikey.be/product-detail/nl/tag-connect-llc/TC2050-IDC-NL/TC2050-IDC-NL-ND/2605367)            |
|                        | TagConnect 10 pin IDC to Needles                         | TagConnect `TC-2050-IDC`       | [digikey.be](https://www.digikey.be/en/products/detail/tag-connect-llc/TC2050-IDC/2605366)                               |

<br/>

## 5 - KiCad debug header symbols

On the repository [brechtve-kicad-things](https://github.com/Fescron/brechtve-kicad-things) symbols are available where the additional functionality, selectable using the jumpers, is depicted for both SWD and JTAG modes. The *regular* pinouts are put into brackets. The symbols can be found in the library [`BrechtVE_DebugHeader.lib`](https://github.com/Fescron/brechtve-kicad-things/blob/master/Libraries/BrechtVE_DebugHeader.lib).

The symbols to be linked to a **0.05" (1.27mm) header** on the target board are the following:

<div style="text-align: center">
<img src="documentation/pictures/v2/symbols.png" height="120" alt="SWD/JTAG symbols for 0.05inch header">
</div>

<br/>

Symbols are also supplied to be linked to a **TagConnect 2050 footprint**. The pin numbers and corresponding functions are mostly the same as for the previously mentioned SWD/JTAG connectors, but the direction in which the pins are laid out on the PCB is different. This is reflected in other symbols:

<div style="text-align: center">
<img src="documentation/pictures/v2/symbols-tagConnect.png" height="133" alt="SWD/JTAG symbols for TagConnect 2050 footprint">
</div>

<br/>

For reference these symbols, along with additional information regarding jumpers and layout guidelines, can be found on the [adapter schematic](hardware/jlink-tagConnect-adapter-v4/jlink-tagConnect-adapter-v4.pdf). A lot more information regarding [KiCad](https://www.kicad.org/) itself is also available on the repository [brechtve-kicad-things](https://github.com/Fescron/brechtve-kicad-things).
