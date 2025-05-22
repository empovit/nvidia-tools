# Other tools

* [DCGM](#dcgm)
* [GPU Burn](#gpu-burn)
* [RDMA Performance Test](#rdma-performance-test)

## DCGM

* [Container images](https://catalog.ngc.nvidia.com/orgs/nvidia/teams/cloud-native/containers/dcgm/tags)
* [Testing apps](https://github.com/NVIDIA/DCGM/tree/master/testing/python3/apps)

A DCGM image includes `dcgmproftester10`, `dcgmproftester11` and `dcgmproftester12` according to the target CUDA version.

Usage:

```console
USAGE:

   dcgmproftester12  [-h] [--cublas] [--dvs] [--no-dcgm-validation]
                     [--reset] [--target-max-value] [--version] [-a
                     <absolute tolerance>] [-c <Configuration file (YAML or
                     JSON) to include>] [-d <Duration in seconds>] [-i
                     <List of GPU IDs to run on>] [-m <mean value to match
                     for validation>] [-r <Rate of report gathering in
                     seconds>] [-t <Valid value for the FieldId is a
                     comma-separated list of>] [-w <wait to check
                     percentage>] [--log-file <Log file name>] [--log-level
                     <Log severity level>] [--max-processes <maximum
                     simultaneous GPUs tested>] [--max-value <maximum value
                     to match for validation>] [--min-value <minimum value
                     for validation>] [--mode <operational mode: fast,
                     generate load, report, validate>] [--percent-tolerance
                     <percentage tolerance>] [--sync-count <Number of
                     attempts to reach target activity levels>]


Where:

   -w <wait to check percentage>,  --wait-to-check <wait to check
      percentage>
     percentage of run to wait before checking value

   --max-processes <maximum simultaneous GPUs tested>
     maximum simultaneous GPUs tested (0=all)

   --percent-tolerance <percentage tolerance>
     percentage tolerance when checking value

   -a <absolute tolerance>,  --absolute-tolerance <absolute tolerance>
     absolute value match tolerance

   --min-value <minimum value for validation>
     minimum value

   -m <mean value to match for validation>,  --match-value <mean value to
      match for validation>
     value to match

   --max-value <maximum value to match for validation>
     maximum value

   -d <Duration in seconds>,  --duration <Duration in seconds>
     duration of test

   -r <Rate of report gathering in seconds>,  --report <Rate of report
      gathering in seconds>
     report gathering rate

   --target-max-value
     Run only at the target maximum value

   --no-dcgm-validation
     Do not do validation

   --dvs
     Do not collect dvs output

   --cublas
     Use CUBLAS for FP16, FP32, FP64 tests

   -t <Valid value for the FieldId is a comma-separated list of>,
      --fieldId <Valid value for the FieldId is a comma-separated list of>
     Profiling FieldId

   -i <List of GPU IDs to run on>,  --gpuIds <List of GPU IDs to run on>
     gpu IDs

   --reset
     Reset switch arguments

   --mode <operational mode: fast, generate load, report, validate>
     operational mode

   --sync-count <Number of attempts to reach target activity levels>
     max synchronous measurement attempt count

   --log-file <Log file name>
     log file

   --log-level <Log severity level>
     log level

   -c <Configuration file (YAML or JSON) to include>,  --config
      <Configuration file (YAML or JSON) to include>
     configuration file

   --,  --ignore_rest
     Ignores the rest of the labeled arguments following this flag.

   --version
     Displays version information and exits.

   -h,  --help
     Displays usage information and exits.

   dcgmproftester
```

## GPU Burn

GitHub repo: [GPU Burn](https://github.com/wilicc/gpu-burn).

## RDMA Performance Test

GitHub repo: [Open Fabrics Enterprise Distribution (OFED) Performance Tests](https://github.com/linux-rdma/perftest).