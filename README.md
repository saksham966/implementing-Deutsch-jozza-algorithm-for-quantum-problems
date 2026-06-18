# Deutsch-Jozsa Algorithm using Qiskit

## Overview

This project implements the Deutsch-Jozsa Algorithm, one of the earliest quantum algorithms demonstrating quantum advantage over classical computation.

The algorithm determines whether a given Boolean function

f : {0,1}ⁿ → {0,1}

is:

* **Constant**: returns the same output (0 or 1) for every input.
* **Balanced**: returns 0 for exactly half of the inputs and 1 for the other half.

Classically, determining this may require evaluating the function on up to 2ⁿ⁻¹ + 1 inputs in the worst case, whereas the Deutsch-Jozsa Algorithm solves the problem using only a single oracle query.

---

## Objectives

* Understand the working of the Deutsch-Jozsa Algorithm.
* Construct constant and balanced oracles.
* Simulate the circuit using Qiskit Aer.
* Observe the measurement outcomes for different oracle types.

---

## Project Structure

```text
.
├── deutsch_jozsa.ipynb      # Main notebook
├── README.md               # Project documentation
└── images/                 # Circuit diagrams and screenshots (optional)
```

---

## Theory

The Deutsch-Jozsa oracle performs the transformation

U_f |x,y⟩ = |x, y ⊕ f(x)⟩

where:

* x is the input register.
* y is the ancilla qubit.
* f(x) is a Boolean function.

The algorithm proceeds as follows:

1. Initialize n input qubits in |0⟩ and one ancilla qubit in |1⟩.
2. Apply Hadamard gates to all qubits.
3. Apply the oracle U_f.
4. Apply Hadamard gates again to the input register.
5. Measure the input qubits.

### Decision Rule

* If the measurement result is

```text
00...0
```

then the function is **constant**.

* If the measurement result is anything other than

```text
00...0
```

then the function is **balanced**.

---

## Oracle Construction

### Constant Oracle

The ancilla remains unchanged:

```python
qc.id(q[n])
```

or

```python
qc.x(q[n])
```

depending on whether the constant output is 0 or 1.

---

### Balanced Oracle

A balanced oracle is created using a bitstring.

Example:

```python
bitstring = '101'
```

The oracle is constructed as:

```python
for i in range(n):
    if bitstring[i] == '1':
        qc.x(q[i])

    qc.cx(q[i], q[n])

    if bitstring[i] == '1':
        qc.x(q[i])
```

The X-CNOT-X pattern converts a normal control-on-1 CNOT into a control-on-0 CNOT, allowing the oracle to implement complemented variables when required.

---

## Requirements

Install the required libraries:

```bash
pip install qiskit
pip install qiskit-aer
```

---

## Running the Simulation

```python
from qiskit_aer import AerSimulator

backend = AerSimulator()

qc_compiled = transpile(qc, backend)

job = backend.run(qc_compiled, shots=1024)

result = job.result()

counts = result.get_counts()

print(counts)
```

---

## Sample Output

### Constant Function

```text
{'000': 1024}
```

The output is always |000⟩, indicating a constant function.

### Balanced Function

```text
{'101': 1024}
```

Any result other than |000⟩ indicates a balanced function.

---

## Results

The project successfully demonstrates:

* Construction of constant and balanced quantum oracles.
* Quantum parallelism through superposition.
* Phase kickback using the oracle.
* Determination of function type using a single oracle query.

---

## Applications

* Foundations of quantum algorithms.
* Demonstration of quantum speedup.
* Oracle-based algorithms.
* Basis for algorithms such as Bernstein-Vazirani and Simon's Algorithm.

---

## References

1. Deutsch, D., and Jozsa, R., "Rapid solution of problems by quantum computation", Proceedings of the Royal Society A, 1992.

2. Qiskit Textbook and official Qiskit documentation.

3. Nielsen, M. A., and Chuang, I. L., *Quantum Computation and Quantum Information*.
