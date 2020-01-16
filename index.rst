.. _stm32h753i_eval_board:

ST STM32H753I Evaluation
#######################

Overview
********

The board includes an STM32H753XI SoC with a high-performance DSP, Arm Cortex-M7,
with 2MBytes of Flash memory, 1MB RAM, 480 MHz CPU, Art Accelerator, L1 cache, external memory interface,
large set of peripherals including a Crypto accelerator, with security services support.

Additionally, the board features:

– 5.7” 640×480 TFT color LCD with touchscreen
– Ethernet compliant with IEEE-802.3-2002
– USB OTG_HS and OTG_FS
– I2C compatible serial interface
– SAI audio DAC
– ST-MEMS digital microphones
– 8 M×32bit SDRAM, 1 M×16bit SRAM and 8 M×16bit NOR Flash memory
– 4 color user LEDs
– Joystick with 4-direction control and selector

.. image:: img/stm32h753i_eval.jpg
     :width: 472px
     :align: center
     :height: 352px
     :alt: STM32H753I-EVAL

More information about the board can be found at the `STM32H753I-EVAL website`_.
More information about STM32H747XIH6 can be found here:

- `STM32H753XI on www.st.com`_
- `STM32H753xx reference manual`_
- `STM32H753xx datasheet`_

Supported Features
==================

The current Zephyr stm32h753i_eval board configuration supports the following hardware features:

+-----------+------------+-------------------------------------+
| Interface | Controller | Driver/Component                    |
+===========+============+=====================================+
| NVIC      | on-chip    | nested vector interrupt controller  |
+-----------+------------+-------------------------------------+
| UART      | on-chip    | serial port-polling;                |
|           |            | serial port-interrupt               |
+-----------+------------+-------------------------------------+
| PINMUX    | on-chip    | pinmux                              |
+-----------+------------+-------------------------------------+
| GPIO      | on-chip    | gpio                                |
+-----------+------------+-------------------------------------+

Other hardware features are not yet supported on Zephyr porting.

The default configuration per core can be found in the defconfig files:
``boards/arm/stm32h753i_eval/stm32h753i_eval_defconfig``
!!!!!!!!!!!!!여기서부터는 확인 필요!!!!!!!!!!!!!!!!!!!!!
Pin Mapping
===========

STM32H753I Evaluation kit has 9 GPIO controllers. These controllers are responsible for pin muxing,
input/output, pull-up, etc.

For mode details please refer to `STM32H747I-DISCO website`_.

Default Zephyr Peripheral Mapping:
----------------------------------

- UART_1 TX/RX : PA9/PA10 (ST-Link Virtual Port Com)
- UART_8 TX/RX : PJ8/PJ9 (Arduino Serial)
- LD1 : PI12
- LD2 : PI13
- LD3 : PI14
- LD4 : PI15
- W-UP : PC13
- J-CENTER : PK2
- J-DOWN : PK3
- J-LEFT : PK4
- J-RIGHT : PK5
- J-UP : PK6

System Clock
============

The STM32H747I System Clock can be driven by an internal or external oscillator,
as well as by the main PLL clock. By default, the CPU1 (Cortex-M7) System clock
is driven by the PLL clock at 400MHz, and the CPU2 (Cortex-M4) System clock
is driven at 200MHz. PLL clock is feed by a 25MHz high speed external clock.

Serial Port
===========

The STM32H747I Discovery kit has up to 8 UARTs.
Default configuration assigns USART1 and UART8 to the CPU1. The Zephyr console
output is assigned to UART1 which connected to the onboard ST-LINK/V3.0. Virtual
COM port interface. Default communication settings are 115200 8N1.

Resources sharing
=================

The dual core nature of STM32H747 SoC requires sharing HW resources between the
two cores. This is done in 3 ways:

- **Compilation**: Clock configuration is only accessible to M7 core. M4 core only
  has access to bus clock activation and deactivation.
- **Static pre-compilation assignment**: Peripherals such as a UART are assigned in
  devicetree before compilation. The user must ensure peripherals are not assigned
  to both cores at the same time.
- **Run time protection**: Interrupt-controller and GPIO configurations could be
  accessed by both cores at run time. Accesses are protected by a hardware semaphore
  to avoid potential concurrent access issues.

Programming and Debugging
*************************

Applications for the ``stm32h747i_disco`` board should be built per core target,
using either ``stm32h747i_disco_m7`` or ```stm32h747i_disco_m4`` as the target.
See :ref:`build_an_application` for more information about application builds.

Flashing
========

Flashing operation will depend on the target to be flashed and the SoC
option bytes configuration.
It is advised to use `STM32CubeProgrammer`_ to check and update option bytes
configuration and flash ``stm32h747i_disco_m7`` and ``stm32h747i_disco_m4`` targets.

By default:

  - CPU1 (Cortex-M7) boot address is set to 0x80000000 (OB: BOOT_CM7_ADD0)
  - CPU2 (Cortex-M4) boot address is set to 0x81000000 (OB: BOOT_CM4_ADD0)

Also, default out of the box board configuration enables CM7 and CM4 boot when
board is powered (Option bytes BCM7 and BCM4 are checked).
In that configuration, Kconfig boot option ``STM32H7_BOOT_CM4_CM7`` should be selected.
Zephyr flash configuration has been set to meet these default settings.

Flashing an application to STM32H753I
---------------------------------------------

First, connect the STM32H753I Evaluation kit to your host computer using
the USB port to prepare it for flashing. Then build and flash your application.

Here is an example for the :ref:`hello_world` application.

.. zephyr-app-commands::
   :zephyr-app: samples/hello_world
   :board: stm32h753i_eval
   :goals: build

Use the following commands to flash target:

.. code-block:: console

   $ ./STM32_Programmer_CLI -c port=SWD mode=UR -w <path_to_m7_binary>  0x8000000
   $ ./STM32_Programmer_CLI -c port=SWD mode=UR -w <path_to_m4_binary>  0x8100000

Run a serial host program to connect with your board:

.. code-block:: console

   $ minicom -D /dev/ttyACM0

You should see the following message on the console:

.. code-block:: console

   Hello World! arm

Debugging
=========

You can debug an application in the usual way.  Here is an example for the
:ref:`hello_world` application.

.. zephyr-app-commands::
   :zephyr-app: samples/hello_world
   :board: stm32h753i_eval
   :goals: debug


.. _STM32H753I-EVAL website:
   http://www.st.com/en/evaluation-tools/stm32h747i-disco.html
   https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mcu-mpu-eval-tools/stm32-mcu-mpu-eval-tools/stm32-eval-boards/stm32h753i-eval.html

.. _STM32H753XI on www.st.com:
   https://www.st.com/content/st_com/en/products/microcontrollers-microprocessors/stm32-32-bit-arm-cortex-mcus/stm32-high-performance-mcus/stm32h7-series/stm32h743-753/stm32h753xi.html#resource
  
.. _STM32H753xx reference manual:
   https://www.st.com/content/ccc/resource/technical/document/reference_manual/group0/c9/a3/76/fa/55/46/45/fa/DM00314099/files/DM00314099.pdf/jcr:content/translations/en.DM00314099.pdf

.. _STM32H753xx datasheet:
   https://www.st.com/resource/en/datasheet/stm32h753xi.pdf

.. _STM32CubeProgrammer:
   https://www.st.com/en/development-tools/stm32cubeprog.html
