# Traffic Light System Model in nuXmv

This project uses nuXmv, a symbolic model checker, to model and formally verify a traffic light and pedestrian crossing system. By incorporating Linear Temporal Logic (LTL) specifications, the model ensures safety, liveness, and correct sequencing, addressing critical real-world requirements for traffic management systems.

## Project Description

Traffic light systems play a crucial role in urban traffic management, ensuring the safety and efficiency of vehicle and pedestrian interactions at intersections. However, due to the complexity of coordinating traffic and pedestrian signals across various phases and responding to real-time events, it’s essential to verify that these systems function correctly under all scenarios. This project uses nuXmv, a formal verification tool, to model and verify a traffic light control system with integrated pedestrian signals. By formally verifying the system, we aim to prevent dangerous situations such as conflicting green signals or failure to respond to pedestrian requests.

## Problem Statement

Designing a traffic light control system that safely manages vehicle and pedestrian signals is challenging due to the need to coordinate multiple phases, states, and real-time inputs. Malfunctions or errors in implementation can result in serious safety hazards, such as simultaneous green signals or failure to prioritize pedestrians. This project addresses the need for a formally verified model to ensure that the traffic light and pedestrian crossing system meets critical safety and operational requirements. By verifying this model with nuXmv, we aim to establish a reliable system that guarantees safety, liveness, and correct sequencing under varying traffic conditions and scenarios.

## Project Structure

- **traffic_light_system.smv** - The single nuXmv file containing the complete model and specifications for the traffic light system.

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
