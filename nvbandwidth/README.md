# Build and use `nvbandwidth` tool

The tool's GitHub repo: [nvbandwidth]<https://github.com/NVIDIA/nvbandwidth.git>.

## On Red Hat OpenShift

### Enable cluster image registry

1. Open the registry config for editing:

    ```console
    oc edit configs.imageregistry/cluster
    ```

2. Change `managedState` to `Managed`:

    ```yaml
    spec:
      managementState: Managed
    ```

3. Configure a storage, e.g. a directory for a _non-production_ cluster:

    ```yaml
    spec:
      storage:
        emptyDir: {}
    ```

### Create an image stream for the output image

```console
oc create imagestream nvbandwidth [-n <namespace>]
```

### Detect a compatible CUDA version

Find out the driver and CUDA versions used by the NVIDIA GPU operator and use a matching base image. See also [CUDA Compatibility](https://docs.nvidia.com/deploy/cuda-compatibility/).

For example, when trying to use CUDA 12.8.0 with 550 drivers, the tool fails with the following error:

```console
Running device_to_device_memcpy_read_sm.
[cudaErrorUnsupportedPtxVersion] the provided PTX was compiled with an unsupported toolchain. in expression cudaGetLastError() in CUresult memsetKernel(CUstream, CUdeviceptr, CUdeviceptr, unsigned int, unsigned int)() : /build/nvbandwidth/kernels.cu:405
```

### Mirror the compatible CUDA development image

```console
oc import-image [-n <namespace>] cuda:12.4.1-devel-ubi9 --from=nvcr.io/nvidia/cuda:12.4.1-devel-ubi9 --confirm=true --reference-policy=local
```

### Copy the cluster entitlement secret into the build namespace

```console
oc get secret etc-pki-entitlement -n openshift-config-managed -o json | jq 'del(.metadata.resourceVersion)' | jq 'del(.metadata.creationTimestamp)' | jq 'del(.metadata.uid)' | jq 'del(.metadata.namespace)' | oc -n [<namespace>] create -f -
```

### Create a `BuildConfig` for `nvbandwidth`

:warning: This build config builds a single-node version of the benchmark. In order to build a multi-node version,
change the `cmake` command to `cmake -DMULTINODE=1 .`.
Then follow [Multinode benchmarks](https://github.com/NVIDIA/nvbandwidth?tab=readme-ov-file#multinode-benchmarks) to run.

Update the CUDA image in [nvbandwidth-build.yml](nvbandwidth-build.yml), and apply the file:

```console
oc apply -f nvbandwidth-build.yml [-n <namespace>]
```

### Trigger a build

```console
oc start-build nvbandwidth-build [-n <namespace>]
```

### Run the `nvbandwidth` tool

```console
oc run [--rm] -t -i nvbandwidth [--pod-running-timeout=10m] [--image-pull-policy='Always'] --image=<image> -- <arg1> <arg2> ... <argN>
```

Example (creating a pod may take a long time because of the image size, so using `--pod-running-timeout`):

```console
oc run --rm -t -i nvbandwidth --pod-running-timeout=10m --image=image-registry.openshift-image-registry.svc:5000/<namespace>/nvbandwidth@sha256:<digest> -- -t device_to_device_memcpy_read_sm
```

or with the latest image:

```console
oc run --rm -t -i nvbandwidth --pod-running-timeout=10m --image=image-registry.openshift-image-registry.svc:5000/<namespace>/nvbandwidth:latest -- -t device_to_device_memcpy_read_sm
```

### Resources

* https://docs.openshift.com/container-platform/4.17/cli_reference/openshift_cli/developer-cli-commands.html
* https://docs.openshift.com/container-platform/4.17/openshift_images/image-streams-manage.html
* https://docs.openshift.com/container-platform/4.17/cicd/builds/understanding-buildconfigs.html
* https://docs.openshift.com/container-platform/4.17/cicd/builds/running-entitled-builds.html#builds-source-secrets-entitlements_running-entitled-builds
* https://docs.openshift.com/pipelines/1.17/create/using-rh-entitlements-pipelines.html