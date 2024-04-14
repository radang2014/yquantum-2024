# Sparse Quantum State Preparation

## Overview

Our approach was primarily based on the algorithms presented in "An Efficient Algorithm for Sparse Quantum State Preparation" by Niels Gleinig and Torsten Hoefler. This involved a two step algorithm implemented in Python using the Classiq API over a 24-hour period as part of |Y>Quantum 2024. Starter code was provided by Tanner Kocher for this challenge.

## Problem Statement

The problem of Quantum State Preparation involves implementing the ability to generate some arbitrary Quantum State from the all-0s state. In practice, most practical applications of Quantum State Preparation involve a desire to generate what is called a "sparse" state, or a state consisting of basis vectors where most of the amplitudes are 0. In this challenge, we implement an efficient algorithm for generating such "sparse" states.

Since Quantum operations are all reversible, we start by building a quantum circuit (i.e. a quantum algorithm) that takes in an arbitrary quantum state as input and outputs the all-0s state. Once we build such a circuit, we can then simply take the transpose of all operations and invert the order. In our code, we build the sparse state preparation circuit by directly emitting circuit elements in reverse order from the algorithm that will be described in the next section.

## Approach and Methodology

The algorithm for "inverse state preparation" is implemented by iteratively reducing the number of basis states included in the inputted quantum state until only one computational basis state remains. This is done through a "merging" protocol that maps a state containing |S| basis vectors to another state containing only |S'| basis vectors, where |S'| < |S|, repeating until we get an |S'| that equals 1. Since the result is a state that is deterministically one of the computational basis elements, we can simply apply one or more X (bitwise flip) gates 
to convert that state to the all-0s state.

"Algorithm 1" implements the "merging" protocol, and Algorithm 2 iteratively repeats Algorithm 1 and the X gates at the end. See the following paper for more information: [https://htor.inf.ethz.ch/publications/img/quantum_dac.pdf](https://htor.inf.ethz.ch/publications/img/quantum_dac.pdf)

## Function Specifications 

### Helper Functions (Provided)

- `dict_to_3d_array(sparse_states)`: Converts a dictionary of sparse states into a 3D array format.
- `custom_filter(func, iterable)`: Filters items in an iterable based on a function.
- `unequal_sets(t, n)`: Determines the best qubit to split a set `t` into subsets with a significant size difference.
- `process_subsets(t, n, dif_qubits, dif_values)`: Processes subsets to determine difference qubits and values.
- `toggle_operations(index, n, x_x, ops1, ops2, s)`: Toggles operations based on the index condition.
- `conditional_toggle(ops1, ops2, n, dif, b, s)`: Conditionally toggles based on a difference.
- `calc_alpha_beta(x_1, x_2)`: Calculates and adjusts alpha and beta values.

### Our Quantum Functions
- `unitary_control(qubit: QArray[QBit], contrl: QArray[QBit], target: QParam[int])`: Implements a CNOT gate (i.e. CX gate) with the qubits in `contrl` as the control bit(s) and `qubit[target]` as the target bit.
- `y_rotation(theta: QParam[float], reg: QArray[QBit], target: QParam[int])`: Implement a Y rotation gate (i.e. RY gate) with `theta` as the angle to rotate in radians and `reg[target]` as the qubit to rotate.
- `my_controlled_unitary(q:QArray[QBit], w:QParam[float], ctrl:QArray[QBit], target:QParam[int])`: Complete an RY rotation, followed by an X operation (i.e. bit flip), followed by the conjugate transpose of the RY rotation on qubit `q[target]`. `w` specifies the rotation angle in radians and `ctrl` specifies the control bit(s) that determine whether the operation is done on `q[target]` (operation is done only if all control bits are on).
- `my_unitary(q:QArray[QBit], w:QParam[float],target:QParam[int])`: Complete an RY rotation, followed by an X operation (i.e. bit flip), followed by the conjugate transpose of RY on the qubit `q[target]`. `w` specifies the rotation angle in radians.

### Overarching Functions 
- `algorithm_1(s,n, gates, x_qubits, cx_qubits, cg_params, final_state, max_num_ctrls)`: Performs "Algorithm 1" as described in the paper, which is the "merging" protocol, along with the logic of iteratively repeating "Algorithm 1" until we have built a circuit whose final state is deterministically one of the states of the computational basis. This function takes in the following parameters:
    - `s` is a 3D array containing the basis states and probabilities to prepare
    - `n` is the number of qubits 
    - `gates` is a list storing strings identifying the different gates in the outputted circuit from left to right (items will likely be appended over the course of the algorithm)
    - `x_qubits` is a list of qubit-string indices identifying the qubits that the X operators are applied on 
    - `cx_qubits` is a list of two-element lists, each of which identify the control qubits (1st element) and target qubits (2nd element) that the Controlled X operators are applied on 
    - `cg_params` is a list that temporarily stores `alpha`, `beta`, `dif_qubits`, and `dif` values (meanings are the same as described in the paper)
    - `final_state` stores the state of the circuit after all "merge" operations have completed, but before any of the final X gates have been applied
    - `max_num_ctrls` stores the maximum number of control qubits necessary for the G operations (i.e. RY X RY-dagger)

- `main(psi: Output[QArray[QBit]])`: Contains overarching execution of sparse state preparation, storing the output state within `psi`. A dictionary of desired probabilities of measuring basis states is hard-coded in the local variable `sparse_states` (along with other commented out lines showing other test cases).


## Submitted Files 

Below is information about what is in each file:

* `yquantum_classiq_challenge.ipynb`: Jupyter Notebook containing main challenge implementation. Run all cells in order. Change `sparse_states` near the top of the `main` function to change the desired state to prepare. **This file contains our implementation of the challenge requirements**.
* `yquantum_classiq_challenge_figure2.ipynb`: Jupyter Notebook containing implementation of the replication of part of Figure 2 done for our enrichment (see "Extensions Beyond the Challenge"). *This was not required by the challenge instructions.*
* Files relating to the preparation of state with the following probabilities (circuit and results from one test case):
    * Probability of 000: 0.25
    * Probability of 001: 0.5
    * Probability of 111: 0.25
    * `000_0.25_001_0.5_111_0.25_circuit.json`: JSON file containing export of constructed circuit for the preparation of the above state 
    * `000_0.25_001_0.5_111_0.25_results.json`: JSON file containing export of results when running the constructed circuit 
* Files relating to the preparation of state with the following probabilities (circuit and results from another test case):
    * Probability of 000011: 0.4
    * Probability of 111111: 0.6
    * `000011_0.4_111111_0.6_circuit.json`: JSON file containing export of constructed circuit for the preparation of the above state 
    * `000011_0.4_111111_0.6_results.json`: JSON file containing export of results when running the constructed circuit 
* `README.md`: This file

## Extensions Beyond the Challenge 

Features implemented beyond what the challenge asked for:
* To produce more readable code, we decided to restructure the obfuscated starter code (including renaming the obfuscated `op1` through `op5` variables) in a way that should hopefully provide a better experience for the reader.
* To explore the effect of the number of nonzero coefficients in the quantum state to prepare and the number of qubits on the depth of the resulting circuit, we have ran an experiment that measured this effect, replicating the results shown in part of Figure 2 from the paper. Our implementation of this experiment is in `yquantum_classiq_challenge_figure2.ipynb`, with the resulting plot in `depth_by_k.png`



