# High-Current 4S Li-ion BMS

A hardware-only **Battery Management System** for 4-series (4S) lithium-ion packs, built around TI's **BQ77915** standalone protection IC. No microcontroller and no firmware: every protection threshold is set in hardware, so fault response is fast and deterministic. A parallel MOSFET switching stage lets it handle the high charge and discharge currents seen in robotics, UAVs and underwater vehicles.

<p align="center">
  <img width="49%" alt="BMS PCB render" src="https://github.com/user-attachments/assets/3c461364-4aa0-41c5-bd26-2262025e1ea2" />
  <img width="49%" alt="BMS PCB render" src="https://github.com/user-attachments/assets/230c1284-a347-43da-a1c5-71bc44649e1c" />
</p>

---

## Highlights

- **4S Li-ion** cell-level monitoring with RC-filtered sense lines
- **Fully analog protection:** no firmware to write, debug or fail
- **Protections:** cell over-voltage, cell under-voltage, charge over-current, discharge over-current, short circuit
- **Passive cell balancing** during charge
- **10 × power MOSFETs** (TI CSD17310Q5A) in separate charge and discharge banks
- **0.2 mΩ, 7 W metal-strip shunt** for low-loss current sensing
- **Resistor-programmed thresholds:** behaviour is fixed at design time

<p align="center">
  <img width="700" alt="Block diagram" src="https://github.com/user-attachments/assets/5b73364d-dafb-4567-b44f-ff804dac1c90" />
</p>

## Key Components

| Function | Part | Notes |
|---|---|---|
| Protection IC | TI BQ7791501PWR | Standalone 3–5S protector, TSSOP |
| Power switches | TI CSD17310Q5A × 10 | N-channel, paralleled into charge / discharge banks |
| Current shunt | 0.2 mΩ metal strip, 7 W | Low temperature coefficient |
| Balancing | Bleed resistors | Driven directly by the BQ77915 |
| Clamping | 16 V Zener, 26 V diode | Transient protection |
| Connectors | 6-pin + 4-pin headers | Cell taps and pack connections |

## How It Works

```
 Cell taps ──RC filters──► BQ77915 ──CHG──► Charge MOSFET bank ──┐
                              │                                   ├──► PACK−
 Shunt (0.2 mΩ) ──RC──────────┘──DSG──► Discharge MOSFET bank ────┘
```

1. **Monitoring:** each cell tap feeds the BQ77915 through an RC filter. The IC watches every cell voltage and the shunt voltage continuously.
2. **Over-voltage:** if any cell crosses the OV threshold, the charge path opens.
3. **Under-voltage:** if any cell drops below the UV threshold, the discharge path opens.
4. **Over-current / short circuit:** the filtered shunt voltage is compared against the configured limits, and the appropriate path is cut within the programmed delay.
5. **Balancing:** cells that reach the balance threshold early bleed current through a resistor so the others can catch up.
6. **Recovery:** paths re-enable automatically according to the IC's configured recovery mode.

Because the charge and discharge banks are independent, the BMS can, for example, stop charging an over-voltage pack while still letting it discharge.

## Design Details

<details>
<summary><b>Why a standalone analog protector?</b></summary>

A microcontroller-based BMS adds flexibility (state-of-charge estimation, communication) but also firmware complexity and more failure modes. For a protection board whose only job is keeping the pack in its safe operating area, the BQ77915 does everything in silicon, with thresholds and timing set by external resistors. The result is simple, predictable and quick to react.
</details>

<details>
<summary><b>Parallel MOSFET switching stage</b></summary>

The BQ77915 only drives gates. Paralleling several CSD17310Q5A devices cuts the effective on-resistance and spreads the current, which reduces conduction loss and per-device heating. Separate banks for charge and discharge let each path be disconnected on its own.
</details>

<details>
<summary><b>Current measurement</b></summary>

A 0.2 mΩ metal-strip shunt keeps voltage drop and dissipation low at high current while still producing a measurable signal. Short, dedicated sense traces run to RC filters at the IC inputs, which suppress switching noise and prevent nuisance trips.
</details>

<details>
<summary><b>PCB layout</b></summary>

- **Power path:** battery terminals, shunt, MOSFET array and output connectors are linked by wide copper pours with short, direct routing.
- **Sensing:** shunt sense traces are short and routed separately from the power path to avoid parasitic-resistance errors.
- **Isolation:** the protection IC and cell-sense network are physically separated from the high-current region.
- **Thermal:** large copper areas around the MOSFETs and via arrays under their pads spread heat to the opposite layer.
</details>

<p align="center">
  <img width="419" alt="Power stage detail" src="https://github.com/user-attachments/assets/c23cfae2-cb27-48c1-a9c4-803c83304e3e" />
</p>

## Repository Contents

| File | Description |
|---|---|
| `BMS.PrjPcb` | Altium Designer project (open this) |
| `BMS.SchDoc` | Schematic |
| `BMS_Rev4.PcbDoc` | PCB layout, revision 4 |
| `BMS.SchLib` / `BMS.PcbLib` | Schematic symbols and footprints |

**Tools:** Altium Designer

## Getting Started

1. Open `BMS.PrjPcb` in Altium Designer.
2. Review the threshold-setting resistors against the BQ77915 datasheet for your cell chemistry before ordering.
3. Generate Gerbers, drill files and BOM for fabrication.

> ⚠️ Connect cell balance leads in the correct order (lowest cell first) and test with a current-limited supply before using a real pack. Lithium-ion cells can be dangerous when mishandled.

## Why It Matters

Over-voltage, deep discharge and over-current all cause capacity fade, rising internal resistance and, at worst, permanent cell damage. By holding every cell inside its limits and keeping the cells balanced, this board improves pack safety, reliability and cycle life.

## Team

| Name | Role |
|---|---|
| **Afraaz Khan** | Hardware design |
| **Ansh Wadhera** | Hardware design |
