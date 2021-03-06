# FIO Helm Chart

This Helm chart defines tests to check kubernetes volume status using FIO tools, a tool that would be able to simulate a given I/O workload without resorting to writing a tailored test case.

## Table of Contents
* [Description](#description)
* [Requirements](#requirements)
* [Quick Start](#quick-start)

## Description

Fio test chart includes a persist volume claim, a ConfigMap and a CronJob for helm test.Persist volume claim is used as the disk target to test. ConfigMap pre-defined default configuration of fio and allows user to customize the parameters to meet test senarios. Test pod is created by CronJob to process pre-defined tests.

## Requirements

* A [Helm](https://helm.sh) installation with access to install to one or more namespaces.
* Access to an up-and-running Kubernetes cluster, which could be the ECS cluster here.

## Quick Start

1. First, [install and setup Helm](https://docs.helm.sh/using_helm/#quickstart).  *_Note:_* you'll likely need to setup role-based access controls for Helm to have rights to install packages, so be sure to read that part.

2. Setup the [EMCECS Helm Repository](https://github.com/EMCECS/charts).

```bash
$ helm repo add ecs https://emcecs.github.io/charts
$ helm repo update
```

3. Install fio-test. This will create the k8s persistent volume claim (pvc) and configmap for the fio test program.

```bash
$ helm install --name fio-test ecs/fio-test
NAME:   fio-test
```

4. Tests will be runing periodically in pods created by CronJob. Existing pods can be collected by command below:

```bash
$ kubectl get po | grep fiotest
fiotest-fio-cronjob-1558939500-qwk8q   0/1     Completed   0          12m
```

5. Until fio test completed in pod, logs can be collected by the command below:
```bash
$ kubectl logs fiotest-fio-cronjob-1558939500-qwk8q
```


6. Optionally you can change the settings on how fio will run against the test volumes. This is done by editing the fio config file as follows:
Option 1
```bash
# get default configmap yaml file
$ kubectl get configmap fio-test-config -o yaml > fio-test-config.yaml

# update customized parameters yaml file, it will take effect within 10s
$ kubectl apply -f fio-config.yaml

```

Option 2

```bash
# update parameters directly
EDITOR=vi; kubectl edit configmap fio-test-config
```