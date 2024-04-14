# Sparse Quantum State Preparation

## Overview

Our approach was primarily based on the algorithms presented in "An Efficient Algorithm for Sparse Quantum State Preparation" by Niels Gleinig and Torsten Hoefler. This involved a two step algorithm implemented in Python using the Classiq API over a 24-hour period between April 13th and April 14th, 2024 as part of |Y>Quantum 2024. Starter code was provided by Tanner Kocher for this challenge.

## Problem Statement

The problem of Quantum State Preparation involves implementing the ability to generate some arbitrary Quantum State from the all-0s state. In practice, most practical applications of Quantum State Preparation involve a desire to generate what is called a "sparse" state, or a state consisting of basis vectors where most of the amplitudes are 0. In this challenge, we implement an efficient algorithm for generating such "sparse" states.

Since Quantum operations are all reversible, we start by building a quantum circuit (i.e. a quantum algorithm) that takes in an arbitrary quantum state as input and outputs the all-0s state. Once we build such a circuit, we can then simply take the transpose of all operations and invert the order. In our code, we build the sparse state preparation circuit by directly emitting circuit elements in reverse order from the algorithm that will be described in the next section.

## Approach and Methodology

The algorithm for "inverse state preparation" is implemented by iteratively reducing the number of basis states included in the inputted quantum state until only one computational basis state remains. This is done through a "merging" protocol that maps a state containing |S| basis vectors to another state containing only |S'| basis vectors, where |S'| < |S|, repeating until we get an |S'| that equals 1. Since the result is a state that is deterministically one of the computational basis elements, we can simply apply one or more X (bitwise flip) gates 
to convert that state to the all-0s state.

"Algorithm 1" implements the "merging" protocol, and Algorithm 2 iteratively repeats Algorithm 1 and the X gates at the end. See the following paper for more information: [https://htor.inf.ethz.ch/publications/img/quantum_dac.pdf](https://htor.inf.ethz.ch/publications/img/quantum_dac.pdf)

## Function Specifications 

## Submitted Files 

Below is information about what is in each file:

* `yquantum_classiq_challenge.ipynb`: Jupyter Notebook containing main challenge implementation 
* TODO: Write about other submitted files 

## Extensions Beyond the Challenge 

To produce more readable code, we also decided to restructure the obfuscated starter code (including renaming the obfuscated `op1` through `op5` variables) in a way that should hopefully provide a better experience for the reader.


