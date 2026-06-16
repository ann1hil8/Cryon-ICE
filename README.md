# Cryon-ICE: High-Performance RISC-V Target Emulator & Hardware Debugger

Cryon-ICE is a high-speed real-time hardware debugger and target emulation platform built on the Microchip PolarFire SoC FPGA architecture. The system serves as an advanced bidirectional interface bridge designed to intercept, analyze, and process target system execution signals with sub-nanosecond timing precision. 

The architecture bridges a multi-core 64-bit Linux-capable RISC-V subsystem and flexible programmable fabric directly to an external emulation pod interface via high-density card edge connectors.

---

## Technical Architecture Overview

### 1. System Function & Target Application
* **Real-Time Emulation:** Provides deterministic, ultra-low-latency tracking and signal injection for vintage and modern processing buses.
* **Signal Intercept:** Actively hooks into address, data, and control structures of external hardware targets through dedicated, direction-controlled level shifters.
* **Bus Topologies:** Supports a universal expansion bus configuration, currently mapped to a physical PCIe card edge connector profile to interface directly with modular target emulation pods.

### 2. Primary Processing Core
The platform core utilizes the **Microchip PolarFire SoC FPGA** which integrates a hardened coherent CPU cluster alongside non-volatile programmable logic fabric.

* **Exact Part Number:** `MPFS025TC-FCVG484I` (Industrial Temperature Variant, 25K Logic Elements).
* **Microprocessor Sub-System (MSS):** * 1x RV64IMAC Monitor Core (E51 Performance Profile).
  * 4x RV64GC Application Cores (U54 Performance Profile, coherent execution cluster).
  * Hardened memory controllers supporting secure boot, asset protection, and full operating system execution (Linux).
* **Reference Baselines:** Hardware schematics and subsystem initialization fabrics are cross-referenced from the **BeagleV-Fire** open-hardware specification and the **MPFS-ICICLE-KIT** (PolarFire SoC Icicle Development Kit) base architectures.

### 3. Physical Mechanical Package
* **Package Designation:** `FCVG484` (Fine Pitch Flip-Chip Ball Grid Array).
* **Physical Package Envelope:** 19.0 mm x 19.0 mm square molded body package size.
* **Pin Matrix Array:** 484 physical active solder spheres mapped to a 22 x 22 structural matrix grid.
* **Ball Pitch ($P$):** 0.8 mm center-to-center pad spacing. This allows routing single escape tracks using conventional, cost-effective mechanical micro-drills rather than high-density interconnect (HDI) laser-vias.

### 4. PCB Design & Layout Constraints

#### Thermal and Decoupling Void
The physical layout strips away all copper landing pads within the center core of the 22 x 22 BGA matrix configuration, mirroring the physical depopulated ring profile of the `FCVG484` package silicon. This provides two critical routing advantages:
* **Decoupling Optimization:** Low-inductance 0.1 uF ceramic decoupling capacitors are placed directly on the bottom side of the PCB (Layer 4/6), directly beneath the processor power balls to suppress high-frequency transients.
* **Thermal Sink:** The internal center cavity is flooded with a continuous, unbroken Ground copper polygon pour on the outer layer, functioning as an integrated cooling block connected directly to internal system return layers.

#### Physical Layer Stackup Blueprint
The system requires a minimum of a 4-Layer or 6-Layer impedance-controlled configuration:
* **Layer 1 (Top/F.Cu):** Microstrip routing paths, BGA outer escape rings, high-speed transceiver runs, and level-shifter landing pads.
* **Layer 2 (Inner 1/In1.Cu):** Continuous, uninterrupted Ground ($GND$) reference plane. Provides immediate shield parameters for high-density breakout tracks.
* **Layer 3 (Inner 2/In2.Cu):** Split Power Plane ($V_{\text{DD}}$ Core Logic, $V_{\text{DDA}}$ Transceiver Supplies, and 3.3 V / 5.0 V I/O Peripheral Rails).
* **Layer 4 (Bottom/B.Cu):** Passives, decoupling capacitors under the BGA core cavity, and secondary escape routing structures.

---

## CAD Design Rules Matrix (KiCad Netclasses)

The board layout is constrained by specific routing rules configured within the project netclass matrix. Tracks are auto-routed and routed manually using single-ended configurations without generating layout violations.

| Net Class Name | Clearance | Track Width | Via Size | Via Drill | uVia Size | uVia Drill | DP Width | DP Gap |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `BGA_Escape` | 0.127 mm | 0.127 mm | 0.450 mm | 0.200 mm | 0.450 mm | 0.200 mm | 0.127 mm | 0.150 mm |
| `Universal_Bus` | 0.150 mm | 0.200 mm | 0.600 mm | 0.300 mm | 0.600 mm | 0.300 mm | 0.200 mm | 0.200 mm |
| `Power_GND` | 0.200 mm | 0.600 mm | 0.800 mm | 0.400 mm | 0.800 mm | 0.400 mm | 0.600 mm | 0.250 mm |
| `Default` | 0.200 mm | 0.250 mm | 0.600 mm | 0.300 mm | 0.600 mm | 0.300 mm | 0.250 mm | 0.250 mm |

### Routing Mechanics
* **BGA Escape Plan:** Escape tracks (0.127 mm width) break out through the diagonal open channels between the BGA pads (0.400 mm pad diameter). Vias are placed inside the center void of each 4-pad diamond cluster to preserve spatial clearances.
* **Bus Expansion:** Upon exiting the physical boundary of the BGA footprint package, traces transition via the `Universal_Bus` netclass rules to 0.200 mm widths to improve trace reliability, signal propagation characteristics, and manufacturing yields over long runs.

---

## Future Roadmap Configurations

### I/O Channel Expansion
* **Target Scale:** Planned upgrade to scale the universal emulation bus up from 48 lines to **64 universal bidirectional I/O channels**.
* **Impact:** Requires mapping additional General Purpose I/O (GPIO) banks from the PolarFire fabric and scaling up the number of transceiver/buffer ICs.

### Alternate Pod Interconnect Profiles
* **Connector Variant Modularity:** Evaluation of replacing or augmenting the baseline standard PCIe card-edge footprint with multi-lane high-density, high-speed connector options (e.g., Amphenol PCIe M.2 Gen 3/Gen 4 card edge or specialized mezzanine interconnects) to decrease overall pod size profiles while protecting high-speed signal integrity.E
