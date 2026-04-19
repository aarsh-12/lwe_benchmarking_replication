# LWE Benchmarking & Replication Wrapper

This repository contains our attempt to improve the dataset generation and attack execution for Machine Learning-based cryptanalysis of the Learning with Errors (LWE) problem. 

## Acknowledgments and Core Codebase

**This project is built entirely upon the foundational open-source release by Facebook Research.** All credit for the core ML architectures and attack mechanics belongs to the original authors.

* **Original Repository:** [facebookresearch/LWE-benchmarking](https://github.com/facebookresearch/LWE-benchmarking/)

---

## Environment Setup

For the complete, step-by-step installation of the underlying models and dependencies, **please follow the setup instructions in the [official Facebook Research repository](https://github.com/facebookresearch/LWE-benchmarking/).**

### Key Dependency Notes:
1. **Conda:** It is highly recommended to use a fast Conda environment manager (like Miniconda) to handle the complex PyTorch and math dependencies without conflicts.
2. **Flatter:** For the lattice-reduction post-processing steps, you will need to install **Flatter**. You can find the source code and build instructions here: [keeganryan/flatter](https://github.com/keeganryan/flatter).

---

## Usage Instructions: The Automation Pipeline

Instead of running the complex base commands manually, you can use our helper scripts to generate data and launch attacks interactively.

### Step 1: Generate the LWE Samples (Matrix A)
Generate the uniform distribution matrix $A$. By default, the number of rows ($m$) is generally taken to be $4 \times n$, but can be adjusted to test different redundancy thresholds.

```bash
python3 scripts/generateA.py --N <n_val> --Q <q_val> --rows <num_rows>
```
*Outputs to: `data/n{n_val}logq{logqval}/origA_n{n_val}logq{logqval}.npy`*

### Step 2: Preprocess the Dataset
Generate the `data.prefix` files required for the Transformer model.

```bash
python3 scripts/run_prepoc.py 
```
* **Interactive Prompts:** The script will ask for necessary setup info.
* **Reload Data:** Provide the path to the `.npy` file generated in Step 1.
* **Continuous Generation:** This process runs infinitely to build massive datasets. Note your `dump_path`. Stop the process with `CTRL+C` once you have enough samples.

*(Tip: Check the sample count in another terminal using `wc -l <dump_path>/data`)*

### Step 3: Generate A and b (Task Formatting)
Format the preprocessed data for your specific attack vector.

```bash
python3 scripts/genAb.py
```
* **RLWE:** Set to `0` for standard LWE.
* **Actions:** * Type `secrets` if you are preparing for a standard **SALSA** attack.
  * Type `describe` if you are targeting cruel bits for the **Cool & Cruel** attack.
* Provide the `dump_path` you noted from Step 2.

## Executing the Attacks

Once your dataset is fully generated and preprocessed, launch the attacks using our interactive runner scripts.

### 1. Run SALSA

```bash
python3 scripts/run_salsa.py
```
* **Task:** When prompted for the task, explicitly type `lwe`.
* Follow the remaining interactive prompts to feed it your dataset path and ML parameters.

### 2. Run Cool & Cruel

```bash
python3 scripts/run_cc.py
```
* Follow the instructions, ensuring you point it to the dataset generated with the `describe` action.
* **Cruel Bits:** Ensure the number of cruel bits you input here exactly matches the number you specified during the `genAb.py` step.
