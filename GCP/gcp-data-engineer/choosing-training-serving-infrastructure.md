# Choosing Training and Serving Infrastructure

## Hardware Accelerators

2 types of accelerators are available in GCP:

1. Graphics processing units (GPUs)
   - has multiple ALUs (arithmetic logic units).
   - Good for:
     - massive parallelization (e.g. training deep learning models)
     - deep learning models performance can be increased by factor 10
   - Bad side:
     - limited data rate between a processor and memory (von Neumann bottleneck)
     - slow processing
2. Tensor processing units (TPUs)
   - 1 TPU is 27 times faster at training than 8 GPUs
   - good for training deep learning models
