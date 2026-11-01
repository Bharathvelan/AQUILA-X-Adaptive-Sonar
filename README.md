# AQUILA-X

## Closed-Loop Low-Power Adaptive Software-Defined Sonar Transmitter Payload for Autonomous Underwater Vehicles (AUVs)


An intelligent, energy-aware and reconfigurable sonar transmitter
payload designed to adapt its transmission parameters to changing
underwater environments.

------------------------------------------------------------------------

## 1. Project Overview

AQUILA-X is a proposed intelligent, low-power, real-time Adaptive
Software-Defined Sonar Transmitter Payload designed for Autonomous
Underwater Vehicles (AUVs).

Conventional sonar transmitters generally operate using predefined
transmission parameters such as center frequency, bandwidth, pulse
duration and transmission amplitude. However, underwater acoustic
conditions continuously change due to depth, temperature, salinity,
turbidity, ambient noise, multipath propagation and reverberation.

A fixed transmission configuration may not provide suitable performance
under all underwater conditions.

AQUILA-X addresses this limitation through a closed-loop adaptive
transmission architecture. It combines environmental sensing,
acoustic-channel analysis, mission requirements, transducer operating
constraints and battery status to determine suitable transmission
parameters for subsequent sonar pings.

The proposed platform combines an embedded control unit, deterministic
digital waveform generation, a high-speed DAC, an analog
signal-conditioning chain and an underwater acoustic transducer.

**Core objective:** Maximize useful underwater sensing performance per
unit of electrical energy while maintaining reliable and safe transducer
operation.

## 2. Problem Statement

**Official Problem Statement:** Development of a Low-Power, Real-Time
Adaptive Software-Defined Sonar Transmitter Payload for Autonomous
Underwater Vehicles (AUVs).

-   **Organization:** Ministry of Earth Sciences (MoES)
-   **Department:** National Institute of Ocean Technology (NIOT)
-   **Category:** Hardware
-   **Theme:** Robotics and Drones

### Existing Challenge

Traditional sonar transmitters commonly operate with fixed or manually
configured transmission parameters. Underwater environments are dynamic:

-   Temperature and salinity influence sound propagation.
-   Depth and pressure affect underwater operating conditions.
-   Turbidity and suspended particles can influence acoustic propagation
    and scattering.
-   Ambient noise reduces effective signal-to-noise ratio.
-   Multipath propagation and reverberation can degrade target
    detection.
-   Higher-frequency acoustic signals generally experience greater
    attenuation over distance than lower-frequency signals.

A fixed-frequency and fixed-power strategy may therefore waste energy or
fail to provide suitable sensing performance when environmental
conditions change.

### Proposed Approach

AQUILA-X evaluates available environmental and system feedback and
selects an appropriate waveform configuration for the next transmission.

## 3. Key Objectives

1.  Develop a software-defined sonar transmitter capable of generating
    multiple configurable acoustic waveforms.
2.  Implement real-time environmental data acquisition and adaptive
    transmission control.
3.  Optimize center frequency, bandwidth, pulse duration and amplitude
    within validated hardware limits.
4.  Reduce unnecessary transmission energy through mission-aware
    control.
5.  Implement deterministic waveform generation using hardware timing
    and DMA-supported data movement.
6.  Monitor transducer and amplifier operating conditions to improve
    hardware protection.
7.  Establish a closed-loop feedback mechanism for continuous next-ping
    optimization.
8.  Develop a modular payload architecture suitable for future AUV
    integration.

## 4. Proposed System Architecture

AQUILA-X uses a partitioned embedded architecture in which the
microcontroller manages decision-making and the FPGA handles
deterministic waveform generation.

``` text
              UNDERWATER ENVIRONMENT
                       |
                       v
          +---------------------------+
          | Environmental Sensors     |
          | Temperature               |
          | Pressure / Depth          |
          | Salinity / Conductivity   |
          | Turbidity                 |
          | Acoustic Feedback         |
          +-------------+-------------+
                        |
                        v
          +---------------------------+
          | Sensor Acquisition        |
          | ADC / Digital Interfaces  |
          | Data Validation           |
          +-------------+-------------+
                        |
                        v
          +---------------------------+
          | STM32H7 Control Unit      |
          | Sensor Fusion             |
          | Confidence Estimation     |
          | Mission Management        |
          | Adaptive Decision Logic   |
          | Energy Monitoring         |
          | Safety Management         |
          +-------------+-------------+
                        |
                        v
          +---------------------------+
          | FPGA Waveform Engine      |
          | NCO / DDS                 |
          | LFM Chirp Generation      |
          | Geometric Sweep           |
          | Phase-Coded Pulses        |
          | Hardware Timing           |
          +-------------+-------------+
                        |
                        v
          +---------------------------+
          | High-Speed DAC            |
          +-------------+-------------+
                        |
                        v
          +---------------------------+
          | Analog Signal Chain       |
          | Reconstruction Filter    |
          | Driver / Gain Stage       |
          | Power Amplifier           |
          | Impedance Matching        |
          +-------------+-------------+
                        |
                        v
          +---------------------------+
          | Underwater Transducer     |
          +-------------+-------------+
                        |
                        v
              ACOUSTIC TRANSMISSION
                        |
                        v
          +---------------------------+
          | Feedback Measurement      |
          | Acoustic Response         |
          | Voltage / Current         |
          | Temperature               |
          | Signal Quality            |
          +-------------+-------------+
                        |
                        v
               NEXT-PING OPTIMIZATION
                        |
                        +---- Feedback to STM32H7
```

*Note: A hydrophone and external data-acquisition system may be used for
acoustic feedback during controlled prototype tests. A complete
operational AUV implementation requires suitable onboard acoustic
feedback hardware.*

## 5. Core Innovation and Technical Features

### 5.1 Closed-Loop Next-Ping Optimization

The central innovation is feedback-driven transmission control. Instead
of selecting parameters only from environmental sensor readings, the
system evaluates information from previous transmissions.

The adaptive controller considers environmental conditions, previous
ping performance, acoustic-channel characteristics, transducer operating
limits, electrical energy consumed, battery availability and mission
requirements. The selected configuration is then used for the next
transmission.

### 5.2 Confidence-Aware Adaptive Control

Sensor readings and acoustic measurements may contain noise, uncertainty
or invalid data. AQUILA-X evaluates the reliability of available
information before changing transmission parameters.

-   **High confidence:** Optimize within validated limits.
-   **Medium confidence:** Apply conservative parameter adjustments.
-   **Low confidence:** Retain a validated safe profile or perform a
    justified low-energy probe.
-   **Invalid or unsafe input:** Inhibit transmission and report the
    fault.

### 5.3 Low-Energy Acoustic Channel Probing

When channel conditions are uncertain, AQUILA-X can use a short,
low-energy probe waveform to gather additional information. The
controller evaluates whether the expected information justifies the
energy and time cost.

### 5.4 Transducer Digital Twin and Health Monitoring

A lightweight measured operating model stores baseline characteristics
such as:

-   Impedance magnitude and phase.
-   Operating voltage and current.
-   Electrical power consumption.
-   Transducer temperature.
-   Frequency-dependent response.

New measurements can be compared against the baseline to identify
abnormal operating behaviour. This is a measured operating model, not a
claim of a complete physics-based simulation.

### 5.5 Energy-Aware Optimization

The controller can adjust transmission amplitude, pulse duration, ping
repetition interval and waveform selection according to mission
requirements and battery availability.

Electrical energy per ping can be estimated by:

\[ E\_{`\text{ping}`{=tex}}=`\int`{=tex}\_{t_0}\^{t_1}V(t)I(t),dt \]

This measures electrical energy at the monitored supply point; it is not
automatically equal to radiated acoustic energy.

### 5.6 Mission-Aware Adaptive Modes

  -----------------------------------------------------------------------
  Mission Mode                        Primary Objective
  ----------------------------------- -----------------------------------
  High-Resolution Mapping             Prioritize suitable bandwidth and
                                      resolution

  Long-Range Search                   Prioritize propagation and
                                      detection requirements

  Battery-Critical                    Minimize unnecessary energy
                                      consumption

  Noisy Environment                   Prioritize robust transmission and
                                      signal quality

  Transducer Protection               Maintain operation within validated
                                      hardware limits
  -----------------------------------------------------------------------

### 5.7 Compute-Aware Adaptation

Lightweight decision logic is used when the environment is stable. More
advanced analysis and optional probing can be activated when uncertainty
or significant environmental changes are detected.

## 6. Software-Defined Waveform Generation

AQUILA-X is designed to support multiple programmable waveform types.

  -----------------------------------------------------------------------
  Waveform                            Purpose
  ----------------------------------- -----------------------------------
  Linear Frequency Modulated (LFM)    Configurable bandwidth and
  Chirp                               pulse-compression applications

  Geometric / Logarithmic Sweep       Frequency exploration across a
                                      selected operating band

  Continuous Wave (CW)                Calibration and controlled
                                      frequency testing

  Phase-Coded Pulse                   Configurable phase sequences for
                                      advanced waveform experiments

  Adaptive Multi-Segment Waveform     Future extension for
                                      mission-specific profiles
  -----------------------------------------------------------------------

### LFM Chirp Generation

An LFM chirp changes its instantaneous frequency linearly over the
selected pulse duration.

\[
`\phi`{=tex}(t)=2`\pi`{=tex}`\left`{=tex}(f_0t+`\frac{1}{2}`{=tex}kt\^2`\right`{=tex})
\]

where:

-   (f_0) = starting frequency.
-   (f_1) = ending frequency.
-   \(T\) = pulse duration.
-   (k=(f_1-f_0)/T) = frequency sweep rate.

The digital waveform can be represented as:

\[ x(t)=A,w(t)`\sin`{=tex}(`\phi`{=tex}(t)) \]

where (A) is signal amplitude and (w(t)) is a window function.

### Digital Windowing

Hann, Hamming, Blackman and Tukey windows can reduce abrupt pulse-edge
transitions and help control spectral sidelobes. The analog
reconstruction filter is used to control unwanted out-of-band components
and DAC images.

**Important:** The actual operating frequency range depends on the
selected transducer, DAC, analog filter, amplifier and
impedance-matching network. A frequency range mentioned in the problem
statement is not automatically guaranteed by the prototype hardware.

## 7. Hardware Architecture

The proposed implementation uses commercially available embedded and
analog hardware components.

  -----------------------------------------------------------------------
  Subsystem               Proposed Hardware       Responsibility
  ----------------------- ----------------------- -----------------------
  Main Controller         STM32H7-class MCU       Sensor acquisition and
                                                  adaptive decisions

  Waveform Engine         Artix-7-class FPGA or   Deterministic waveform
                          equivalent              generation

  Digital-to-Analog       High-speed DAC          Digital-to-analog
  Conversion                                      conversion

  Analog Conditioning     Low-pass filter and     Signal conditioning
                          driver                  

  Power Stage             Efficient power         Transducer drive
                          amplifier               

  Matching Network        Frequency-dependent     Transducer electrical
                          matching circuit        matching

  Acoustic Output         Underwater transmit     Acoustic transmission
                          transducer              

  Environmental Sensing   Temperature, pressure,  Environmental
                          salinity and turbidity  measurements
                          sensors                 

  Electrical Monitoring   Voltage and current     Energy and hardware
                          sensing circuits        monitoring

  Feedback Validation     Hydrophone and DAQ      Acoustic measurement
                                                  during testing

  Power Management        Battery monitoring      Battery-state
                          circuit                 estimation
  -----------------------------------------------------------------------

### Hardware Design Considerations

-   Select the DAC according to waveform bandwidth, sample rate,
    resolution and interface requirements.
-   Characterize transducer impedance before designing the matching
    network.
-   Ensure the power amplifier operates within voltage, current and
    thermal limits.
-   Use appropriate analog filtering and signal-integrity practices.
-   Isolate sensitive control electronics from high-power switching
    noise.
-   Implement hardware protection and safe transmission-inhibit
    conditions.

## 8. Embedded Firmware and FPGA Responsibilities

### STM32H7 Firmware

1.  Environmental sensor acquisition.
2.  ADC and digital sensor processing.
3.  Sensor validation and timestamp management.
4.  Environmental data fusion.
5.  Confidence estimation.
6.  Adaptive waveform parameter selection.
7.  Battery and energy monitoring.
8.  Mission-mode management.
9.  Fault detection and protection.
10. Data logging and communication.

### FPGA Waveform Engine

1.  Deterministic waveform generation.
2.  Numerically Controlled Oscillator (NCO) or Direct Digital Synthesis
    (DDS).
3.  LFM chirp generation.
4.  Phase-coded waveform generation.
5.  Hardware-timed waveform sequencing.
6.  DAC sample delivery.
7.  Predictable pulse timing and synchronization.

### DMA and Hardware Timers

Hardware timers and DMA-supported transfers reduce CPU involvement in
repetitive data movement.

In the proposed MCU-FPGA architecture:

-   MCU DMA can support sensor acquisition and data transfers where
    applicable.
-   FPGA logic controls deterministic high-rate waveform timing and
    sample delivery to the DAC.
-   The exact DAC interface and data-transfer implementation depend on
    the selected hardware.

The MCU does not need to calculate every high-speed waveform sample
using repeated software trigonometric operations.

## 9. Adaptive Decision-Making Algorithm

``` text
START
  |
  v
Acquire Environmental and System Data
  |
  v
Validate Sensor Readings
  |
  v
Estimate Environmental and Acoustic Conditions
  |
  v
Evaluate Confidence Level
  |
  +------ Invalid / Unsafe ------> Inhibit Transmission
  |
  v
Check Transducer Operating Limits
  |
  v
Evaluate Mission Requirements
  |
  v
Estimate Candidate Waveform Performance
  |
  v
Evaluate Energy Consumption
  |
  v
Select Suitable Waveform Parameters
  |
  v
Generate Waveform Using FPGA
  |
  v
Transmit Through DAC and Analog Chain
  |
  v
Measure Electrical and Acoustic Feedback
  |
  v
Update Operating History and Model
  |
  v
Optimize Next Transmission
  |
  +------------------------------> REPEAT
```

### Adaptive Parameters

-   Center frequency.
-   Starting and ending frequency.
-   Bandwidth.
-   Pulse duration.
-   Amplitude.
-   Waveform type.
-   Window function.
-   Ping repetition interval.

### Safety Constraints

The controller must reject configurations that violate validated
transducer frequency limits, DAC sample-rate constraints, amplifier
voltage and current limits, thermal limits, impedance-matching
constraints or battery limits.

Hysteresis and minimum-improvement thresholds can prevent rapid
switching between nearly equivalent configurations.

## 10. Communication Interfaces

  Interface               Application
  ----------------------- --------------------------------------------------------------
  I2C                     Environmental sensors and monitoring devices
  SPI                     MCU-FPGA configuration and suitable peripheral communication
  UART                    Debugging and system monitoring
  ADC                     Analog sensor and electrical measurement acquisition
  DMA                     Efficient data movement
  FPGA-to-DAC Interface   High-speed waveform sample transfer
  RS485 / UART            Optional AUV communication integration

## 11. Development and Implementation Plan

### Phase 1: System Design

-   Finalize system architecture.
-   Select controller and waveform-generation hardware.
-   Identify transducer operating requirements.
-   Define waveform parameters and safety limits.

### Phase 2: Digital Waveform Development

-   Implement LFM waveform generation.
-   Implement frequency sweep generation.
-   Develop windowing functions.
-   Verify waveform frequency, duration and bandwidth.
-   Analyze signals using FFT and spectrograms.

### Phase 3: Hardware Integration

-   Interface the MCU and FPGA.
-   Integrate the DAC.
-   Develop the analog reconstruction filter.
-   Integrate the driver and amplifier.
-   Characterize the selected transducer.

### Phase 4: Adaptive Control Development

-   Integrate environmental inputs.
-   Implement data validation.
-   Develop confidence-aware decision logic.
-   Implement mission-aware operating modes.
-   Integrate electrical energy monitoring.

### Phase 5: Closed-Loop Validation

-   Perform controlled acoustic measurements.
-   Implement feedback acquisition.
-   Compare previous ping performance.
-   Verify next-ping parameter adaptation.
-   Evaluate energy consumption and safety behaviour.

### Phase 6: AUV Payload Integration

-   Integrate electronics into a suitable enclosure.
-   Evaluate power, size and communication requirements.
-   Perform progressive environmental testing.
-   Assess readiness for AUV integration.

## 12. Testing and Validation

The system is intended to be evaluated through digital simulation,
electronics bench testing and controlled acoustic experiments.

  -----------------------------------------------------------------------
  Test                                Validation Objective
  ----------------------------------- -----------------------------------
  Waveform Verification               Confirm generated waveform
                                      characteristics

  DAC Testing                         Verify analog output quality

  FFT Analysis                        Evaluate frequency content

  Spectrogram Analysis                Verify time-frequency behaviour

  Filter Testing                      Evaluate out-of-band suppression

  Amplifier Testing                   Verify electrical operating limits

  Transducer Characterization         Measure impedance and frequency
                                      response

  Environmental Input Testing         Verify sensor acquisition and
                                      adaptation

  Closed-Loop Testing                 Verify next-ping parameter updates

  Energy Measurement                  Estimate electrical energy per ping

  Hydrophone Testing                  Measure acoustic output under
                                      controlled conditions

  Fault Injection                     Verify invalid-input and protection
                                      responses
  -----------------------------------------------------------------------

### Performance Metrics

-   Center-frequency accuracy.
-   Bandwidth accuracy.
-   Pulse-duration error.
-   Amplitude accuracy.
-   Waveform fidelity.
-   Spectral sidelobe behaviour.
-   Electrical energy per ping.
-   Acoustic output pressure.
-   Signal-to-noise ratio under documented test conditions.
-   Adaptive decision latency.
-   Fault detection and response time.
-   Repeatability across test conditions.

A calibrated hydrophone is required for reliable acoustic pressure
measurements. Electrical energy measured at the amplifier supply must
not be presented as radiated acoustic energy. Simulated and measured
results must be reported separately.

## 13. Innovation and Unique Selling Proposition

AQUILA-X focuses on integrating multiple engineering capabilities into
one closed-loop adaptive sonar transmitter architecture.

  -----------------------------------------------------------------------
  Existing Approach / Capability      AQUILA-X Approach
  ----------------------------------- -----------------------------------
  Fixed or manually selected          Feedback-driven next-ping
  transmission parameters             optimization

  Adaptation based only on            Environmental, acoustic and
  environmental inputs                system-state evaluation

  Parameter changes without explicit  Confidence-aware adaptation
  confidence handling                 

  Uncertainty handled through         Optional low-energy acoustic
  conservative operation              probing

  Basic hardware operating limits     Measured transducer operating model

  Transmission power considered       Energy-aware mission optimization
  independently                       

  Independent waveform generation     Integrated sensing, decision and
                                      waveform-generation loop

  Static mission configuration        Mission-aware adaptive operating
                                      modes
  -----------------------------------------------------------------------

**Primary USP:** AQUILA-X combines confidence-aware environmental
adaptation, low-energy channel probing, measured transducer operating
constraints, energy-aware waveform selection and feedback-driven
next-ping optimization in a modular embedded sonar transmitter payload.

The novelty lies in this integrated system architecture and operating
strategy, rather than claiming that individual waveform-generation or
software-defined sonar techniques are entirely new.

## 14. Expected Benefits and Applications

### Technical Benefits

-   Adaptive transmission configuration.
-   Reconfigurable waveform generation.
-   Improved mission-specific parameter selection.
-   Hardware-aware transmission control.
-   Closed-loop feedback capability.
-   Modular embedded architecture.

### Energy Benefits

-   Reduced unnecessary transmission energy.
-   Mission-aware power management.
-   Energy measurement at the transmitter electronics.
-   Adaptation to battery availability.

### Potential Applications

-   Autonomous Underwater Vehicles.
-   Marine scientific exploration.
-   Underwater mapping and surveying.
-   Oceanographic research.
-   Underwater infrastructure inspection.
-   Maritime research and monitoring.
-   Future autonomous underwater sensing platforms.

These are intended application areas; actual performance depends on the
selected hardware, transducer, acoustic environment and validation
results.

## 15. Project Scope and Current Limitations

AQUILA-X focuses on the adaptive sonar transmitter payload. The initial
prototype is intended to demonstrate waveform generation, adaptive
transmission control, electrical monitoring and feedback-based next-ping
optimization.

-   A complete side-scan sonar imaging system is outside the initial
    transmitter-only scope unless a receiver and imaging subsystem are
    separately implemented.
-   The operating frequency range depends on the selected transducer and
    analog hardware.
-   Underwater acoustic feedback requires suitable measurement hardware.
-   Pressure-resistant deployment requires an appropriately designed and
    tested enclosure.
-   Performance improvement and energy-saving percentages must be
    established through repeatable measurements.
-   Full underwater AUV deployment is a later integration milestone.

## 16. Future Scope

1.  Integration with a complete sonar receiver and signal-processing
    subsystem.
2.  Real-time target detection and acoustic classification.
3.  Advanced acoustic-channel estimation.
4.  Data-driven adaptive waveform selection using validated experimental
    datasets.
5.  Advanced mission planning and multi-AUV coordination.
6.  Improved transducer health prediction.
7.  Underwater communication and navigation integration.
8.  Field validation in representative underwater environments.

Machine learning or reinforcement learning may be explored after
sufficient reliable experimental data has been collected.


## 17. Getting Started

### Prerequisites

The development environment may include:

-   STM32CubeIDE.
-   STM32CubeMX.
-   FPGA development tools compatible with the selected FPGA.
-   DAC configuration and programming tools.
-   Python 3.
-   NumPy.
-   SciPy.
-   Matplotlib.
-   Pandas.
-   Serial communication utilities.


### Development Status

Document which modules are implemented, simulated, bench-tested or
experimentally validated. The proposed architecture does not imply that
all hardware modules are already operational.

## 18. Project Development Status

Update this table with actual progress and measured evidence as
development continues.

  Module                        Development Status
  ----------------------------- ---------------------------------------
  System Architecture           Design and documentation
  Waveform Generation           Digital implementation
  LFM Chirp                     Waveform development and verification
  FPGA Integration              Hardware implementation
  DAC and Analog Chain          Hardware integration
  Environmental Acquisition     Sensor integration
  Adaptive Decision Logic       Firmware development
  Confidence-Aware Control      Algorithm implementation
  Energy Monitoring             Electrical measurement
  Transducer Characterization   Bench testing
  Closed-Loop Adaptation        Integrated validation
  Underwater Testing            Controlled acoustic experiments

## 19. References and Technical Background

The project draws on established engineering concepts in:

-   Software-defined sonar.
-   Underwater acoustic propagation.
-   Adaptive and cognitive sonar waveform design.
-   Digital waveform synthesis.
-   FPGA-based signal generation.
-   Embedded real-time control systems.
-   Acoustic signal processing.
-   Transducer characterization.
-   Low-power embedded system design.

Add verified research papers, standards, datasheets and technical
publications to `docs/references.md`, including publication details and
DOI or official URLs.

## 20. Conclusion

AQUILA-X proposes a modular, low-power and feedback-driven
software-defined sonar transmitter payload for autonomous underwater
platforms.

By combining environmental sensing, confidence-aware adaptive control,
deterministic waveform generation, transducer operating-model
monitoring, energy-aware optimization and acoustic feedback, the system
aims to move beyond fixed-parameter sonar transmission.

**Do not merely transmit a fixed waveform. Measure the outcome,
understand the operating conditions and intelligently configure the next
transmission.**

AQUILA-X is designed as a scalable foundation for future intelligent
underwater sensing and autonomous marine exploration systems.

------------------------------------------------------------------------

## Project Information

-   **Project Name:** AQUILA-X
-   **Project Domain:** Embedded Systems, FPGA, Digital Signal
    Processing, Underwater Acoustics and Robotics
-   **Application:** Adaptive Sonar Transmitter Payload for AUVs
-   **Problem Statement:** Development of a Low-Power, Real-Time
    Adaptive Software-Defined Sonar Transmitter Payload for Autonomous
    Underwater Vehicles (AUVs)
-   **Organization:** Ministry of Earth Sciences -- National Institute
    of Ocean Technology


