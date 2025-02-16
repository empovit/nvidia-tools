# NCCL Tests

[NVIDIA Collective Communication Library (NCCL) User Guide](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/index.html)

GitHub repo: [nccl-tests](https://github.com/NVIDIA/nccl-tests.git).

## Running

Refer to [Building and Using CUDA-based Images on Red Hat OpenShift](../build-container-images.md) to build a container image.

Control the execution using [NCCL environment variables](https://docs.nvidia.com/deeplearning/nccl/user-guide/docs/env.html) and [CUDA environment variables](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#cuda-environment-variables).

Example:

```console
CUDA_VISIBLE_DEVICES=0,1 NCCL_P2P_LEVEL=NVL NCCL_P2P_DISABLE=1 ./all_reduce_perf -g 2 -c 0 -n 100 -w 20
```

List of compiled tools:
* all_gather_perf
* all_reduce_perf
* alltoall_perf
* broadcast_perf
* gather_perf
* hypercube_perf
* reduce_perf
* reduce_scatter_perf
* scatter_perf
* sendrecv_perf
