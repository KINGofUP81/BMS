# BMS

# High Current 4S Lithium-Ion Battery Management System (BMS)

This project presents the hardware design of a high-current Battery Management System (BMS) developed for 4-Series (4S) Lithium-Ion battery packs. The design is intended for applications requiring reliable battery protection under high charge and discharge currents, such as robotics, UAVs, autonomous underwater vehicles (AUVs/ROVs), and other embedded systems.
<img width="800" height="500" alt="image" src="https://github.com/user-attachments/assets/3c461364-4aa0-41c5-bd26-2262025e1ea2" />
<img width="800" height="500" alt="image" src="https://github.com/user-attachments/assets/230c1284-a347-43da-a1c5-71bc44649e1c" />



The BMS is built around the Texas Instruments BQ7791501PWR, a standalone analog battery protection IC that continuously monitors individual cell voltages and pack current without requiring a microcontroller. Protection thresholds and operating modes are configured through external resistor networks, resulting in a hardware-programmable system with deterministic operation and fast response to fault conditions.

To support high-current applications, the protection outputs control a parallel MOSFET switching stage capable of handling significantly higher currents than the protection IC alone.
<img width="700" height="400" alt="image" src="https://github.com/user-attachments/assets/5b73364d-dafb-4567-b44f-ff804dac1c90" />


# Features
Supports 4-Series (4S) Lithium-Ion battery packs
High-current charge and discharge protection
Hardware-based battery protection (No firmware required)
Passive cell balancing
Precision current sensing
Parallel MOSFET switching stage
Hardware configurable protection thresholds
Compact PCB optimized for high-current applications
Main Protection Controller

At the heart of the BMS is the Texas Instruments BQ7791501PWR, a standalone battery protection IC specifically designed for multi-cell lithium-ion battery packs.

The IC continuously monitors the voltage of each individual cell and the current flowing into and out of the battery pack. Based on these measurements, it independently controls the charging and discharging MOSFETs to protect the battery from abnormal operating conditions.

Unlike microcontroller-based BMS architectures, the BQ77915 performs all protection functions entirely in hardware, providing fast fault response and eliminating the need for firmware development.

The protection parameters, including voltage thresholds, balancing behavior, and timing characteristics, are configured using external resistor networks, allowing the operating characteristics to be permanently defined during hardware design.

# Cell Voltage Monitoring

Each battery cell is connected directly to the BQ77915 through dedicated voltage sensing inputs. RC filters are incorporated at every sensing node to suppress high-frequency noise and transient disturbances before they reach the protection IC.

The BQ77915 continuously monitors the voltage of each cell independently. If any cell exceeds the configured over-voltage threshold, the charging path is disconnected to prevent overcharging. Similarly, if any cell falls below the under-voltage threshold, the discharge path is disabled to protect the battery from deep discharge.

Continuous cell-level monitoring ensures that every cell remains within its recommended operating voltage range, improving both battery safety and cycle life.

# Passive Cell Balancing

To compensate for small variations between individual lithium-ion cells, the BMS incorporates a passive balancing circuit controlled directly by the BQ77915.

During charging, when one cell reaches the balancing threshold before the others, the balancing circuit diverts a small current around that cell through an external resistor. This allows the remaining cells to continue charging until all cells reach nearly the same voltage.

Although passive balancing dissipates excess energy as heat, it provides a simple, reliable, and cost-effective method for maintaining cell voltage uniformity and maximizing the usable capacity of the battery pack.

# High Current Switching Stage

Since the BQ77915 is designed only to control external switches, the high-current path is implemented using multiple Texas Instruments CSD17310Q5A N-Channel MOSFETs connected in parallel.

Connecting several MOSFETs in parallel reduces the effective ON resistance of the switching stage while distributing the load current among multiple devices. This lowers conduction losses, improves thermal performance, and enables the BMS to safely operate at significantly higher current levels.

Separate MOSFET banks are used for the charging and discharging paths, allowing the BQ77915 to independently disconnect either path depending on the detected fault condition.

# Current Measurement

Pack current is measured using a 0.2 mΩ precision metal strip shunt resistor rated for 7 W of continuous power dissipation.

The extremely low resistance minimizes voltage drop across the battery path while still generating a measurable voltage proportional to the current flowing through the pack.

The voltage across the shunt resistor is filtered using dedicated RC networks before being supplied to the current sensing inputs of the BQ77915. These filters reduce electrical noise and prevent false triggering of the protection circuitry during high-current switching events.

The use of a precision, low-temperature-coefficient shunt resistor ensures consistent current measurement accuracy over a wide operating temperature range.

# Protection Features

The BMS continuously monitors the battery pack and automatically disconnects the charging or discharging path whenever an unsafe operating condition is detected.

The implemented protection features include:

Cell Over-Voltage Protection
Cell Under-Voltage Protection
Charge Over-Current Protection
Discharge Over-Current Protection
Short-Circuit Protection
Passive Cell Balancing
Automatic Fault Recovery (as supported by the configured hardware mode)

These hardware-based protection mechanisms ensure that the battery always operates within its safe electrical limits, helping to prevent damage caused by abnormal charging or discharging conditions.

# Power Stage Design

The power stage has been designed to support high-current operation while minimizing conduction losses and thermal stress.

Wide copper pours are used throughout the charge and discharge paths to reduce resistance and improve current carrying capability. The parallel MOSFET arrangement further distributes current across multiple devices, reducing the power dissipated in each individual transistor.

Large solder pads and copper areas surrounding the MOSFETs also assist in spreading heat across the PCB during continuous operation.
<img width="419" height="202" alt="image" src="https://github.com/user-attachments/assets/c23cfae2-cb27-48c1-a9c4-803c83304e3e" />


# PCB Layout and Design Considerations

The PCB layout has been designed with a strong focus on current handling capability, signal integrity, and thermal management.

The power stage has been grouped into a compact section where the parallel MOSFETs are positioned close together, minimizing the resistance and inductance of the high-current paths. Wide copper pours are used between the battery terminals, shunt resistor, MOSFET array, and output connectors to reduce conduction losses while improving current distribution.

The 0.2 mΩ precision shunt resistor is placed directly in the main current path, with short sensing traces routed to the BQ77915 through dedicated RC filters. This layout minimizes measurement errors caused by parasitic resistance and electrical noise.

Sensitive analog circuitry, including the cell voltage sensing network and protection IC, is physically separated from the high-current switching region to reduce noise coupling and improve measurement accuracy.

To improve thermal performance, large copper pours surround the MOSFET array, allowing heat to spread across the PCB. Multiple thermal vias beneath the MOSFET pads provide a low thermal resistance path between the top and bottom copper layers, reducing junction temperature during sustained high-current operation.

Together, these layout practices improve electrical efficiency, measurement accuracy, and long-term reliability of the BMS under demanding operating conditions.

# Battery Safety and Lifetime Enhancement

Lithium-ion batteries are highly sensitive to excessive voltage, current, and temperature. Operating outside the recommended limits can lead to accelerated capacity loss, increased internal resistance, reduced cycle life, and, in severe cases, permanent cell damage.

By continuously monitoring each individual cell and automatically disconnecting the battery during abnormal operating conditions, the BMS ensures that the battery pack remains within its safe operating area. Passive cell balancing further maintains voltage uniformity across all cells, reducing imbalance over repeated charge cycles.

Together, these protection mechanisms help maximize battery safety, improve long-term reliability, and extend the overall service life of the battery pack
