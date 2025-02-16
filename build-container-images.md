# Building and Using CUDA-based Images on Red Hat OpenShift

For experimenting with a tools, it can be useful to build a container image for it.
This guide explains how to build and host an image easily inside an OpenShift cluster.

## Enable cluster image registry

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

## Create an image stream for the output image

```console
oc create imagestream <name> [-n <namespace>]
```

Example:

```console
oc create imagestream nvbandwidth -n nvidia-tools
```

## Find a compatible CUDA version

Find out the driver and CUDA versions used by the NVIDIA GPU operator and use a matching base image. See also [CUDA Compatibility](https://docs.nvidia.com/deploy/cuda-compatibility/).

For example, trying to use CUDA 12.8.0 with 550 drivers will fails with an error similar to the following one:

```console
Running device_to_device_memcpy_read_sm.
[cudaErrorUnsupportedPtxVersion] the provided PTX was compiled with an unsupported toolchain. in expression cudaGetLastError() in CUresult memsetKernel(CUstream, CUdeviceptr, CUdeviceptr, unsigned int, unsigned int)() : /build/nvbandwidth/kernels.cu:405
```

## Mirror the compatible CUDA development image

Store the base image locally so it isn't pulled every time from the source registry:

```console
oc import-image [-n <namespace>] cuda:12.4.1-devel-ubi9 --from=nvcr.io/nvidia/cuda:12.4.1-devel-ubi9 --confirm=true --reference-policy=local
```

Example:

```
oc import-image -n nvidia-tools cuda:12.4.1-devel-ubi9 --from=nvcr.io/nvidia/cuda:12.4.1-devel-ubi9 --confirm=true --reference-policy=local
```

## Copy the cluster entitlement secret into the build namespace

This is required to install additional packages, such as `make` and `git`, using `dnf install`.

```console
oc get secret etc-pki-entitlement -n openshift-config-managed -o json | jq 'del(.metadata.resourceVersion)' | jq 'del(.metadata.creationTimestamp)' | jq 'del(.metadata.uid)' | jq 'del(.metadata.namespace)' | oc [-n <namespace>] create -f -
```

Example:

```console
oc get secret etc-pki-entitlement -n openshift-config-managed -o json | jq 'del(.metadata.resourceVersion)' | jq 'del(.metadata.creationTimestamp)' | jq 'del(.metadata.uid)' | jq 'del(.metadata.namespace)' | oc -n nvidia-tools create -f -
```

## Create a `BuildConfig` resource

See examples of build configuration under the respective tool directories, usually named `*-build.yml`.

```console
oc apply -f <name>-build.yml [-n <namespace>]
```

Example:

```console
oc apply -f nvbandwidth-build.yml -n nvidia-tools
```

## Trigger a build

```console
oc start-build <name>-build [-n <namespace>]
```

Example:

```console
oc start-build nvbandwidth-build -n nvidia-tools
```

### Run the tool

```console
oc run [--rm] -t -i <name> [--pod-running-timeout=10m] [--image-pull-policy='Always'] --image=<image> -- <arg1> <arg2> ... <argN>
```

Example for `nvbandwith` (creating a pod may take a long time because of the image size, so using `--pod-running-timeout`):

```console
oc run --rm -t -i nvbandwidth --pod-running-timeout=10m --image=image-registry.openshift-image-registry.svc:5000/nvidia-tools/nvbandwidth@sha256:<digest> -- -t device_to_device_memcpy_read_sm
```

or with `latest` tag (always pull to get the latest image):

```console
oc run --rm -t -i nvbandwidth --image-pull-policy='Always' --pod-running-timeout=10m --image=image-registry.openshift-image-registry.svc:5000/nvidia-tools/nvbandwidth:latest -- -t device_to_device_memcpy_read_sm
```

### Resources

* [OpenShift CLI developer command reference](https://docs.openshift.com/container-platform/4.17/cli_reference/openshift_cli/developer-cli-commands.html)
* [Managing image streams](https://docs.openshift.com/container-platform/4.17/openshift_images/image-streams-manage.html)
* [Understanding build configurations](https://docs.openshift.com/container-platform/4.17/cicd/builds/understanding-buildconfigs.html)
* [Adding subscription entitlements as a build secret](https://docs.openshift.com/container-platform/4.17/cicd/builds/running-entitled-builds.html#builds-source-secrets-entitlements_running-entitled-builds)
* [Using Red Hat entitlements in pipelines](https://docs.openshift.com/pipelines/1.17/create/using-rh-entitlements-pipelines.html)