from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister
from qiskit_aer import Aer
from qiskit.visualization import plot_histogram, plot_bloch_multivector
from qiskit.compiler import transpile
from qiskit.quantum_info import Statevector
import matplotlib.pyplot as plt
import numpy as np

# Create quantum circuit
qr = QuantumRegister(1, 'q')
cr = ClassicalRegister(1, 'c')
circuit = QuantumCircuit(qr, cr)

# Apply Hadamard gate to create superposition
circuit.h(qr[0])
circuit.measure(qr, cr)  # Add measurement

# Print circuit
print("Quantum Circuit:")
print(circuit)

# Get the statevector for Bloch sphere (before measurement)
state = Statevector.from_instruction(circuit.remove_final_measurements(inplace=False))

# Execute the circuit with measurements
simulator = Aer.get_backend('qasm_simulator')
compiled_circuit = transpile(circuit, simulator)
job = simulator.run(compiled_circuit, shots=2000)
result = job.result()
counts = result.get_counts()

# Create first figure for measurement histogram
plt.figure(figsize=(8, 6))
plot_histogram(counts)
plt.title('Measurement Results')
plt.savefig('measurement_results.png')
plt.show()

# Create second figure for Bloch sphere
plt.figure(figsize=(8, 6))
bloch_fig = plot_bloch_multivector(state)
plt.title('Bloch Sphere Representation')
plt.savefig('bloch_sphere.png')
plt.show()

# Print the measurement counts
print("\nMeasurement Counts:", counts)
