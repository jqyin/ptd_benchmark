# PyTorch Data Parallel Benchmark

### Files
* `models.py` contains 5 main components
    1. `GPTConfig` specifies the size of GPT3. See examples below. All DDP/Pipeline/FSDP GPT models should take a `GPTConfig` instance as an example.

        ```python
        class GPTSmallConfig(GPTConfig):
            """ GPT3-small like network roughly 125M params """
            n_layer = 12
            n_head = 12
            n_embd = 768

        class GPTLargeConfig(GPTConfig):
            """ GPT3-large like network roughly 760M params """
            n_layer = 24
            n_head = 16
            n_embd = 1536

        class GPTXLConfig(GPTConfig):
            """ GPT3-XL like network roughly 1.3B params """
            n_layer = 24
            n_head = 24
            n_embd = 2048
        ```

    2. Single-device `class GPT`. This is used by DDP.
    3. `sequential_gpt` returns a GPT model as a `nn.Sequential` which is already balanced across given devices. This is used by pipeline parallelism
    4. TODO: GPT recursively wrapped by FSDP
    5. TODO: import ResNet models from torchvision
* `trainer.py` entry point for `torchrun`


### Launching Experiments

#### Launch Locally

Launch DDP on two GPUs in the same machine:

```
torchrun --nnodes=1 --nproc_per_node=2 --rdzv_id=100 --rdzv_endpoint="localhost:5678" trainer.py
```

Launch Pipeline + DDP on two GPUs in the same machine, where pipeline spans two devices and DDP world size is 1:

```
torchrun --nnodes=1 --nproc_per_node=1 --rdzv_id=100 --rdzv_endpoint="localhost:5678" trainer.py --ndevice_per_proc=2 --mode=pdp
```

#### Launch on GCP


