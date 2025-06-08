# Refinery I&C Support Network Optimizer

![Python Version](https://img.shields.io/badge/python-3.9+-blue.svg)
![Gurobi Version](https://img.shields.io/badge/gurobi-10.0+-green.svg)
![Category](https://img.shields.io/badge/domain-Instrumentation_&_Control-blueviolet.svg)
![License](https://img.shields.io/badge/license-MIT-lightgrey.svg)

A strategic planning tool for optimizing the logistics network that supports a refinery's critical **Instrumentation & Control (I&C)** infrastructure. This solver uses Mixed-Integer Linear Programming (MILP) to determine the optimal placement of field support hubs and the most efficient daily routes for technical service teams, minimizing operational costs and system downtime risk.

---

## Table of Contents
- [Industrial Context](#industrial-context-automation-in-a-refinery)
- [Problem Formulation](#problem-formulation)
- [Technology Stack](#technology-stack)
- [Getting Started](#getting-started)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [License](#license)

---

## Industrial Context: Automation in a Refinery

A modern refinery is a highly automated environment, governed by a complex web of Instrumentation and Control (I&C) systems. Thousands of sensors (measuring pressure, temperature, flow, level), actuators (control valves, pumps), and controllers (DCS, PLCs) work in unison to ensure safe, stable, and efficient production.

The reliability of this automation layer is paramount. A single faulty sensor or delayed calibration can compromise product quality, create safety hazards, or lead to a costly unit shutdown. Therefore, the I&C infrastructure requires a robust support system for:
*   **Preventive Maintenance & Calibration:** Regular servicing of thousands of instruments spread across the plant.
*   **Consumable Supply:** Delivery of calibration gases, reagents, and other essential materials.
*   **Spare Part Logistics:** Staging and delivery of critical spare parts like transmitters, control cards, and valve positioners.

This project models and solves the core logistical challenge faced by an I&C or Maintenance department: **How do we design our support network to service the entire plant most effectively?**

This solver answers two questions:
1.  **Hub Placement:** Where should we establish decentralized **Field Support Hubs** or technical lockers to store parts and equipment?
2.  **Technician Routing:** What is the most efficient daily route for a **Technical Service Vehicle** to travel from the central workshop, resupply the active hubs, and return?

## Problem Formulation

The objective is to **minimize the total daily operational cost and risk**, which is a sum of:

1.  **Field Hub Establishment Cost:** The amortized daily cost of setting up and maintaining a field support hub (e.g., secure storage, climate control, inventory management).
2.  **Downtime Risk Penalty:** A financial penalty representing the increased risk of production delays or safety incidents for any plant area (or "supermarket" of I&C assets) that is not within the service range of an established hub.
3.  **Service Vehicle Operational Cost:** The daily cost (fuel, maintenance, labor) of running a service vehicle on its resupply tour, proportional to the distance traveled.

### Decision Variables
-   **`X` (Binary Vector):** Determines hub activation. `X[i] = 1` if a field support hub is established at potential location `i`.
-   **`Y` (Binary Matrix):** Defines the service vehicle's route. `Y[i, j] = 1` if the vehicle travels from location `i` to `j`. Location `0` represents the Central I&C Workshop.

The model is formulated as a Mixed-Integer Linear Program (MILP) with constraints ensuring tour validity (subtour elimination) and correct linkage between hub activation and the plant areas they are designated to serve.

## Technology Stack

*   **[Python](https://www.python.org/)**: Core programming language for model implementation.
*   **[Gurobi](https://www.gurobi.com/)**: A world-class commercial solver for solving the MILP model.
*   **[Pandas](https.pandas.pydata.org/)**: For efficient data loading from CSV files.
*   **[JSON](https://www.json.org/json-en.html)**: For reading cost parameters and configuration.

## Getting Started

Follow these steps to set up the project locally.

### Prerequisites

*   Python 3.8+
*   A Gurobi installation and an active license (free academic licenses are available).
    *   [Gurobi Installation Guide](https://www.gurobi.com/documentation/10.0/quickstart_mac/index.html)

### Installation

1.  **Clone the repo:**
    ```sh
    git clone https://github.com/your_username/Refinery-IC-Support-Network-Optimizer.git
    cd Refinery-IC-Support-Network-Optimizer
    ```
2.  **Install dependencies:**
    (It's highly recommended to use a Python virtual environment)
    ```sh
    pip install gurobipy pandas
    ```

---

## Usage

1.  **Data Setup:** Place your instance data files (`weights.json`, `service.csv`, `distances.csv`) into a subdirectory within the `data/` folder.

2.  **Solver Configuration:** Open `main.py` and ensure it imports and instantiates your solver class correctly.
    ```python
    # In main.py
    from solvers.solver_XXXXXX_YYYYYY import solver_XXXXXX_YYYYYY as MySolver
    
    # ...
    
    solver = MySolver(instance) 
    ```

3.  **Execute the Solver:**
    ```sh
    python main.py
    ```
    The solver will run and output the optimal decision variables.

4.  **Evaluate Solution Cost:**
    To get a detailed cost breakdown of the solution, run the evaluator.
    ```sh
    python evaluator.py
    ```

---

## Project Structure

```
.
├── data/                  # Problem instance data
│   └── sample_refinery/
│       ├── distances.csv  # Distances between workshop, hubs, etc.
│       ├── service.csv    # Matrix of which hub can serve which plant area
│       └── weights.json   # Cost and penalty data
├── solvers/               # Solver implementations
│   ├── __init__.py
│   └── solver_XXXXXX_YYYYYY.py   # Your MILP solver class
├── main.py                # Main executable script
├── evaluator.py           # Solution evaluation script
└── README.md              # This file
