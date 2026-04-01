# Pt-Al2O3-Co3O4 Deep Potential Dataset

This directory contains the dataset and a pre-trained model for the platinum (Pt), aluminum oxide ($Al_2O_3$), and cobalt oxide ($Co_3O_4$) system. The data is formatted for use with **DeepMD-kit**.

---

## Directory Structure

### 1. Root Files
* **`dpa.pb`**: The frozen model file (graph). This is the pre-trained Deep Potential (DP) model that can be used directly for MD simulations or property inference.

### 2. Dataset Directory (`/dataset`)
The dataset is categorized by chemical composition and subdivided into specific configuration batches. Each batch is typically separated into `training_data` and `validation_data`.

| System | Description | Batch Range |
| :--- | :--- | :--- |
| **`Al2O3`** | Pure Aluminum Oxide configurations. | 1 – 5 |
| **`Co3O4`** | Pure Cobalt Oxide configurations. | 1 – 4 |
| **`Pt`** | Pure Platinum configurations. | 1 – 6 |
| **`Al2O3-Pt`** | Interfaces or mixtures of $Al_2O_3$ and Pt. | 1 – 16 |
| **`Co3O4-Pt`** | Interfaces or mixtures of $Co_3O_4$ and Pt. | 1 – 12 |
| **`Al2O3-Co3O4`** | Mixed oxide systems. | 1 – 2 |
| **`Al2O3-Co3O4-Pt`**| Ternary systems containing all three components. | 1 – 3 |

---

## Data Format Details

The folders follow the standard DeepMD-kit format. 

### Metadata Files:
* **`type.raw`**: A list of integers representing the atom types for each atom in the system.
* **`type_map.raw`**: A mapping of the integers in `type.raw` to chemical elements (e.g., `Al`, `O`, `Co`, `Pt`).

### Data Sets (`set.000/`):
These contain binary NumPy files (`.npy`) representing the physical properties of the system across multiple frames:
* `coord.npy`: Atomic coordinates $[N_{frames}, N_{atoms} \times 3]$.
* `box.npy`: Simulation box vectors $[N_{frames}, 9]$.
* `energy.npy`: Potential energy of each frame $[N_{frames}]$.
* `force.npy`: Forces acting on atoms $[N_{frames}, N_{atoms} \times 3]$.
* `virial.npy`: Virial stress tensor $[N_{frames}, 9]$.

---

## Usage

### 1. Running Simulations with LAMMPS

When using `pair_coeff` in LAMMPS, you **must** map the LAMMPS atom types to the model's internal element order: **Al, O, Pt, Co**.

**Syntax Examples:**
* For an **$Al_2O_3$ and Pt** system (where LAMMPS types 1, 2, 3 are Al, O, Pt):
  ```
  pair_style deepmd dpa.pb
  pair_coeff * * Al O Pt
  ```

* For a **$Co_3O_4$ and Pt** system (where LAMMPS types 1, 2, 3 are O, Pt, Co):
  ```
  pair_style deepmd dpa.pb
  pair_coeff * * O Pt Co
  ```

* For a pure **Pt** system (where LAMMPS type 1 is Pt):
  ```
  pair_style deepmd dpa.pb
  pair_coeff * * Pt
  ```

> **Note:** The elements listed after `pair_coeff * *` must match the chemical identity of LAMMPS atom types 1, 2, 3, etc., in that specific order.

### 2. Training or Fine-tuning
If you wish to retrain the model, include these paths in your `input.json` file:

**IMPORTANT:** The following JSON snippet is provided only as a structural example for data path configuration. It is **NOT** a complete training script. 

Users must refer to the [DeepMD-kit Official Documentation](https://deepmd-kit.readthedocs.io/en/stable/) to configure essential hyper-parameters (such as `model`, `loss`, and `learning_rate`) according to their specific hardware and accuracy requirements.

```json
"training": {
    "systems": [
        "./dataset/Al2O3/1/training_data",
        "./dataset/Al2O3-Pt/1/training_data"
    ],
    "batch_size": "auto"
}
```


---

## Requirements
* **DeepMD-kit**: 2.x or 3.x
* **NumPy**: To handle `.npy` data files
* **LAMMPS**: Compiled with the `DEEPMD` package