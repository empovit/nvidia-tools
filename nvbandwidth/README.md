# nvbandwidth tool

GitHub repo: [nvbandwidth](https://github.com/NVIDIA/nvbandwidth.git).

Running on OpenShift: [Building and Using CUDA-based Images on Red Hat OpenShift](../build-container-images.md).

:warning: The [build config](nvbandwidth-build.yml) provided here builds a single-node version of the benchmark.
In order to build a multi-node version, change the `cmake` command to `cmake -DMULTINODE=1 .`.
Then follow [Multinode benchmarks](https://github.com/NVIDIA/nvbandwidth?tab=readme-ov-file#multinode-benchmarks) to run.