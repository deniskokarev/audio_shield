# STM32 Nucleo Audio Amplifier Shield

A compact, Arduino Uno R3-form-factor audio shield that amplifies the analog output of an MCU DAC. The board is designed around the STM32 NUCLEO-L476RG, using its `A2` header pin (`PA4`, DAC channel 1) as the audio source.

The current design is a single-channel (mono) output stage. It buffers the DAC signal with an OPA340 rail-to-rail op-amp, attenuates and AC-couples the signal into an LM386 audio power amplifier, and routes the amplified output through a coupling capacitor to a 3.5 mm jack.

## Features

- Arduino Uno R3 shield header layout for use with compatible STM32 Nucleo boards.
- `A2` / `PA4` / DAC channel 1 audio input on the intended NUCLEO-L476RG host.
- OPA340 unity-gain buffer to isolate the MCU DAC from the amplifier input network.
- LM386 audio power amplifier operating at its default gain of 20.
- 30 kOhm / 1 kOhm input attenuator.
- AC-coupled amplifier input and output.
- LM386 output stability network (50 nF in series with 10 Ohm to ground).
- Mono 3.5 mm audio output.
- 5 V operation from the Arduino-compatible power header.
- Two-layer PCB designed in KiCad 9.

## Signal Path

```text
STM32 DAC
A2 / PA4 / DAC1
      |
      v
OPA340 voltage follower
      |
      v
30 kOhm / 1 kOhm attenuator
      |
      v
10 uF input coupling capacitor
      |
      v
LM386 power amplifier (gain = 20)
      |
      v
10 uF output coupling capacitor
      |
      v
Mono 3.5 mm output jack
```

The OPA340 is powered from the shield's 5 V rail and is configured as a voltage follower. The LM386 gain-setting pins are left open, selecting the device's default voltage gain of 20. A 1 uF capacitor decouples the local supply.

## Board Compatibility

The shield uses the Arduino Uno R3 connector geometry, but electrical compatibility depends on the host board:

- **STM32 NUCLEO-L476RG:** the intended host. Its `A2` pin maps to `PA4`, which provides DAC channel 1.
- **Other Arduino-compatible Nucleo boards:** may work if they expose a true DAC output on `A2`, provide a compatible 5 V supply, and use the same header layout.
- **Classic Arduino Uno R3:** mechanically compatible with the header pattern, but its ATmega328P does not provide a true DAC on `A2`. PWM would require an appropriate reconstruction filter, or an external DAC would be needed.

The schematic also notes the NUCLEO-L476RG's second DAC channel on `A5` / `PA5`, but that pin is not connected in the current board. Only one audio channel is implemented.

## Output and Load Notes

The output connector uses a TRS-style physical footprint as a mono jack: the tip carries the amplified signal, the sleeve is ground, and the ring contact is unused.

The intended load impedance and target output power are not yet specified. The 10 uF output coupling capacitor produces a load-dependent low-frequency cutoff, so speaker, headphone, and line-level loads will behave differently. Verify output level, distortion, LM386 stability, and capacitor polarity with the intended load before treating the design as production-ready.

## Opening the Project

1. Open `audio_shield.kicad_pro` in KiCad 9 or a compatible newer release.
2. Open the schematic and PCB from the KiCad project manager.
3. Keep the project-local symbol and footprint libraries beside the project files so KiCad can resolve the custom Arduino shield symbol and footprint.
4. Run Electrical Rules Check (ERC) and Design Rules Check (DRC) before generating fabrication files.

## Project Files

| File or directory | Purpose |
|---|---|
| `README.md` | Project overview, hardware description, compatibility notes, and file inventory. |
| `audio_shield.kicad_pro` | Main KiCad project configuration, including schematic, PCB, ERC, DRC, routing, and text-variable settings. |
| `audio_shield.kicad_sch` | Schematic source containing the Arduino-compatible headers, OPA340 buffer, LM386 amplifier, passive components, output jack, net labels, and design notes. |
| `audio_shield.kicad_pcb` | PCB source containing the board outline, component placement, footprints, routed copper, vias, silkscreen, and copper zones. |
| `audio_shield.kicad_prl` | KiCad per-user project state, such as active layers, visibility, editor panels, and selection filters. It is not the authoritative circuit design. |
| `_ProjectSymbols.kicad_sym` | Project-local KiCad symbol library containing the custom Arduino Uno R3 shield/header symbol. |
| `_ProjectFootprints.pretty/` | Project-local KiCad footprint library directory. |
| `_ProjectFootprints.pretty/Arduino_Uno_R3_Square.kicad_mod` | Custom through-hole Arduino Uno R3 shield-header footprint with the connector geometry and reference graphics used by the PCB. |
| `sym-lib-table` | Maps the `_ProjectSymbols` nickname to the project-local symbol library. |
| `fp-lib-table` | Maps the `_ProjectFootprints` nickname to the project-local footprint library. |

## Current Scope

This repository contains the KiCad design sources and local libraries. It does not currently include firmware, a bill of materials, fabrication outputs, assembly drawings, measured performance data, or a license.
