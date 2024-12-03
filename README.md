# Traffic Light Control System Model in nuXmv

This project uses nuXmv, a symbolic model checker, to model and formally verify a traffic light and pedestrian crossing system. By incorporating Linear Temporal Logic (LTL) specifications, the model ensures safety, liveness, and correct sequencing, addressing critical real-world requirements for traffic management systems.

## Project Description

Traffic light systems play a crucial role in urban traffic management, ensuring the safety and efficiency of vehicle and pedestrian interactions at intersections. However, due to the complexity of coordinating traffic and pedestrian signals across various phases and responding to real-time events, it’s essential to verify that these systems function correctly under all scenarios. This project uses nuXmv, a formal verification tool, to model and verify a traffic light control system with integrated pedestrian signals. By formally verifying the system, we aim to prevent dangerous situations such as conflicting green signals or failure to respond to pedestrian requests.

## Problem Statement

Designing a traffic light control system that safely manages vehicle and pedestrian signals is challenging due to the need to coordinate multiple phases, states, and real-time inputs. Malfunctions or errors in implementation can result in serious safety hazards, such as simultaneous green signals or failure to prioritize pedestrians. This project addresses the need for a formally verified model to ensure that the traffic light and pedestrian crossing system meets critical safety and operational requirements. By verifying this model with nuXmv, we aim to establish a reliable system that guarantees safety, liveness, and correct sequencing under varying traffic conditions and scenarios.

## System Architecture and Design Overview

This project models a single intersection Traffic Light Control System (TLCS) using nuXmv. The system incorporates state variables, transitions, and formal verification specifications to ensure safe and efficient operation under varying traffic conditions. The project is organized as follows:

### **File Structure**
- **traffic_light_system.smv**: The nuXmv model file containing:
  - Definitions of traffic light states, pedestrian signals, timers, sensors, and buttons.
  - Transition logic governing state changes.
  - Linear Temporal Logic (LTL) specifications to verify safety, liveness, and correct sequencing.

---

### **Code Design Overview**

The code models a single intersection with North-South and East-West traffic flows, incorporating vehicle sensors, pedestrian buttons, and countdown timers. It defines state variables, transitions, invariants, and formal properties to verify the system’s correctness.

#### **1. Defined Variables**
The model uses state variables to represent the components of the TLCS.

##### **Traffic Lights**
- **`traffic_NS`**: Represents the North-South traffic light state. Possible values: `{green, yellow, red}`.
- **`traffic_EW`**: Represents the East-West traffic light state. Possible values: `{green, yellow, red}`.

##### **Pedestrian Signals**
- **`pedestrian_NS`**: Represents the pedestrian signal for the North-South crossing. Possible values: `{WALK, DONT_WALK}`.
- **`pedestrian_EW`**: Represents the pedestrian signal for the East-West crossing. Possible values: `{WALK, DONT_WALK}`.

##### **Timers**
- **`timer_NS`**: Tracks the duration of the North-South traffic light state. Range: `0..10`.
- **`timer_EW`**: Tracks the duration of the East-West traffic light state. Range: `0..10`.
- **`pedestrian_countdown_NS`**: Countdown timer for the North-South pedestrian signal. Range: `0..5`.
- **`pedestrian_countdown_EW`**: Countdown timer for the East-West pedestrian signal. Range: `0..5`.

##### **Sensors and Buttons**
- **`sensor_north`, `sensor_south`**: Boolean variables indicating vehicle presence in the North-South direction.
- **`sensor_east`, `sensor_west`**: Boolean variables indicating vehicle presence in the East-West direction.
- **`ped_button_NS`, `ped_button_EW`**: Boolean variables indicating pedestrian button presses for North-South and East-West crossings.

---

#### **2. Initialization**
The `INIT` block defines the starting state of the system to reflect real-world conditions:

- **Traffic Lights**:
  - `traffic_NS = green` and `traffic_EW = red`: North-South starts with a green light, East-West starts with a red light to prevent conflicts.
- **Pedestrian Signals**:
  - Both pedestrian signals are initially `DONT_WALK` for safety.
- **Timers**:
  - All timers start at `0` to synchronize with the system's initial state.
- **Sensors and Buttons**:
  - Sensors and pedestrian buttons are assumed to be inactive (`FALSE`) at the start.

---

#### **3. Transitions**
The `ASSIGN` block defines state transitions based on traffic flow, pedestrian interactions, and timing constraints.

##### **Traffic Light Transitions**
1. **North-South (`traffic_NS`)**:
   - **Green**:
     - Stays green if vehicles are detected (`sensor_north | sensor_south`) and `timer_NS < 10`.
     - Transitions to yellow when `timer_NS = 10`.
   - **Yellow**:
     - Transitions to red when `timer_NS = 10`.
   - **Red**:
     - Returns to green when `traffic_EW = red` and `timer_NS = 0`.

2. **East-West (`traffic_EW`)**:
   - Similar rules to `traffic_NS`, ensuring proper sequencing and no conflicts.

##### **Pedestrian Signal Transitions**
- **WALK**:
  - Activates when the corresponding traffic light is red, the pedestrian button is pressed, and the signal is currently `DONT_WALK`.
- **DONT_WALK**:
  - Activates when the traffic light turns green or the pedestrian countdown reaches `0`.

##### **Timers**
- Traffic light timers increment every step and reset to `0` when reaching `10`.
- Pedestrian countdown timers decrement during `WALK` and reset to `5` during `DONT_WALK`.

---

#### **4. Invariants**
The invariants define conditions that must hold in every reachable state of the system. These ensure the fundamental correctness of the model.

##### **Traffic Safety**
1. **No Simultaneous Green Lights**:
   - `!(traffic_NS = green & traffic_EW = green)`
   - Ensures that the North-South and East-West traffic lights cannot be green simultaneously, preventing vehicle collisions.

2. **No Conflicts Between Pedestrian and Traffic Signals**:
   - `!(traffic_NS = green & pedestrian_NS = WALK)`
   - `!(traffic_EW = green & pedestrian_EW = WALK)`
   - Guarantees that a pedestrian WALK signal is never active when the corresponding traffic light is green.

##### **Pedestrian Countdown**
1. **Proper Countdown Logic**:
   - The pedestrian countdown timer should only decrement during the WALK phase and reset when transitioning to DONT_WALK.

These invariants ensure the system remains safe and predictable under all scenarios.

---

#### **5. LTL Specifications**
The model uses LTL to define and verify critical properties. These include:

##### **Safety Properties**
1. **No Conflicts Between Traffic and Pedestrians**:
   - `G !(traffic_NS = green & pedestrian_NS = WALK)`
   - `G !(traffic_EW = green & pedestrian_EW = WALK)`

2. **No Simultaneous Green Lights**:
   - `G !(traffic_NS = green & traffic_EW = green)`

##### **Liveness Properties**
1. **Pedestrian Requests**:
   - `G (ped_button_NS -> F pedestrian_NS = WALK)`
   - `G (ped_button_EW -> F pedestrian_EW = WALK)`

2. **Traffic Light Cycles**:
   - `G (traffic_NS = green -> X traffic_NS = yellow)`
   - `G (traffic_NS = yellow -> X traffic_NS = red)`
   - Similar properties for `traffic_EW`.

##### **Correct Sequencing**
1. **Pedestrian Countdown**:
   - Countdown starts at `5` during `WALK`: 
     - `G ((pedestrian_NS = WALK & X pedestrian_NS = WALK) -> pedestrian_countdown_NS = 5)`
   - Countdown decreases correctly: 
     - `G ((pedestrian_NS = WALK & pedestrian_countdown_NS > 0) -> X pedestrian_countdown_NS = pedestrian_countdown_NS - 1)`
   - Signal transitions to `DONT_WALK` when countdown reaches `0`: 
     - `G (pedestrian_countdown_NS = 0 -> X pedestrian_NS = DONT_WALK)`

##### **Deadlock Freedom**
- Ensures traffic lights continue cycling:
  - `G (traffic_NS = green | traffic_NS = yellow | traffic_NS = red)`
  - Similar property for `traffic_EW`.

## Verification Results

The verification results from the nuXmv model, stored in `results.txt`, confirm that all specified properties hold true. Below is a detailed explanation of the results:

### **1. Safety Properties**
These specifications ensure that the system operates without conflicts or unsafe behaviors.

#### **Preventing Conflicts Between Traffic and Pedestrian Signals**
- `G !(traffic_NS = green & pedestrian_NS = WALK)`: **true**  
- `G !(traffic_EW = green & pedestrian_EW = WALK)`: **true**  
  - Confirms that pedestrians are never signaled to WALK while traffic lights are green in the same direction, ensuring safety for pedestrians and vehicles.

#### **No Simultaneous Green Lights**
- `G !(traffic_NS = green & traffic_EW = green)`: **true**  
  - Confirms that the North-South and East-West traffic lights are never green simultaneously, preventing cross-traffic collisions.

---

### **2. Liveness Properties**
These specifications ensure that the system responds to inputs and does not get stuck.

#### **Pedestrian Button Functionality**
- `G (ped_button_NS -> F pedestrian_NS = WALK)`: **true**  
- `G (ped_button_EW -> F pedestrian_EW = WALK)`: **true**  
  - Confirms that when a pedestrian button is pressed, the corresponding WALK signal will eventually activate, ensuring pedestrians are prioritized.

#### **Proper Traffic Light Cycling**
##### **North-South Direction**
- `G (traffic_NS = green -> X traffic_NS = yellow)`: **true**  
- `G (traffic_NS = yellow -> X traffic_NS = red)`: **true**  
- `G (traffic_NS = red -> X traffic_NS = green)`: **true**

##### **East-West Direction**
- `G (traffic_EW = green -> X traffic_EW = yellow)`: **true**  
- `G (traffic_EW = yellow -> X traffic_EW = red)`: **true**  
- `G (traffic_EW = red -> X traffic_EW = green)`: **true**  

- Confirms that traffic lights follow the proper sequence (green → yellow → red → green) without skipping states or getting stuck.

---

### **3. Correct Sequencing of Pedestrian Signals**
These specifications ensure that pedestrian signals and timers operate correctly.

#### **Pedestrian Signal Activation with Traffic Lights**
- `G ((ped_button_NS & traffic_NS = red) -> F pedestrian_NS = WALK)`: **true**  
- `G ((ped_button_EW & traffic_EW = red) -> F pedestrian_EW = WALK)`: **true**  
  - Confirms that pedestrian signals activate during red lights when buttons are pressed.

#### **Countdown Timer Behavior**
##### **Initialization**
- `G ((pedestrian_NS = WALK & X pedestrian_NS = WALK) -> pedestrian_countdown_NS = 5)`: **true**  
- `G ((pedestrian_EW = WALK & X pedestrian_EW = WALK) -> pedestrian_countdown_EW = 5)`: **true**  
  - Confirms that pedestrian countdown timers start at `5` when the WALK signal is activated.

##### **Decrement**
- `G ((pedestrian_NS = WALK & pedestrian_countdown_NS > 0) -> X pedestrian_countdown_NS = pedestrian_countdown_NS - 1)`: **true**  
- `G ((pedestrian_EW = WALK & pedestrian_countdown_EW > 0) -> X pedestrian_countdown_EW = pedestrian_countdown_EW - 1)`: **true**  
  - Confirms that countdown timers decrement correctly during the WALK phase.

##### **Transition to DONT_WALK**
- `G (pedestrian_countdown_NS = 0 -> X pedestrian_NS = DONT_WALK)`: **true**  
- `G (pedestrian_countdown_EW = 0 -> X pedestrian_EW = DONT_WALK)`: **true**  
  - Confirms that pedestrian signals transition from WALK to DONT_WALK when the countdown reaches `0`.

##### **Reset**
- `G (pedestrian_NS = DONT_WALK -> X pedestrian_countdown_NS = 5)`: **true**  
- `G (pedestrian_EW = DONT_WALK -> X pedestrian_countdown_EW = 5)`: **true**  
  - Confirms that pedestrian countdown timers reset to `5` when the signal transitions to DONT_WALK.

---

### **4. Deadlock Freedom**
These specifications ensure the system does not reach a state where no transitions are possible.

- `G (traffic_NS = green | traffic_NS = yellow | traffic_NS = red)`: **true**  
- `G (traffic_EW = green | traffic_EW = yellow | traffic_EW = red)`: **true**  
  - Confirms that traffic lights continuously cycle through valid states, ensuring that the system remains operational without stalling.

---

### How to Analyze Results

To view the verification results:

1. Run the nuXmv model using the provided instructions in the **"Running the Model"** section.
2. Review the generated `results.txt` file for confirmation that all specifications hold true.
3. If any specification fails, the `results.txt` file will include a counterexample trace, which can help debug the issue.

## Requirements

- **nuXmv** - Cloning this repository should provide access to nuXmv out of the box. However, if you encounter any issues, you can download and install nuXmv from [https://nuxmv.fbk.eu](https://nuxmv.fbk.eu).
  - This project was developed and tested on Windows and has not been tested on macOS or Linux. Additional installation steps may need to be done.

## Running the Model

To verify the model in `traffic_light_system.smv`, follow these steps:

1. Open a terminal or command prompt.
2. Navigate to the directory containing `nuXmv.exe` and the `traffic_light_system.smv` file.
3. Run the following command:

    ```bash
    .\nuXmv .\smv\traffic_light_system.smv
    ```

This command will execute the model, checking the defined LTL specifications and providing verification results.

### Saving Results to a Text File

To save the output to a file named `results.txt`, use the following command:

```bash
.\nuXmv .\smv\traffic_light_system.smv > results.txt
