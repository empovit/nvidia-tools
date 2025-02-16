# `nvidia-smi` for Querying GPUs

* [Manual page](https://docs.nvidia.com/deploy/nvidia-smi/index.html)

Examples:
* `nvidia-smi topo -m` - show topology matrix
* `nvidia-smi topo -mp` - show topology matrix for PCI only
* `nvidia-smi topo -p2p n` - show the NVLink P2P status between GPUs
* `nvidia-smi nvlink -gt -d` - show link throughput counters for Tx and Rx data payload in KiB
* `nvidia-smi nvlink -s` - show link state, active/inactive