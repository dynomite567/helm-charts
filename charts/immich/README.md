# Immich

<!-- BADGES/ -->
[![GitHub Release](https://img.shields.io/github/v/release/dynomite567/helm-charts?filter=immich*)](https://github.com/dynomite567/helm-charts/tree/main/charts/immich)
[![Release Charts](https://github.com/dynomite567/helm-charts/actions/workflows/release.yaml/badge.svg)](https://github.com/dynomite567/helm-charts/actions/workflows/release.yaml)

**Cloned from [immich-app/immich-charts](https://github.com/immich-app/immich-charts)**

Installs [Immich](https://github.com/immich-app/immich), a self-hosted photo and video backup solution directly from your mobile phone. 

# Goal

This repo contains helm charts the immich community developed to help deploy Immich on Kubernetes cluster.

Specifically, this chart has been modified to include NFS shares as an alternative option for the main library path. Any changes made to the official chart will be mirrored here.

It leverages the bjw-s [common-library chart](https://github.com/bjw-s-labs/helm-charts/tree/923ef40a39520979c98f354ea23963ee54f54433/charts/library/common) to make configuration as easy as possible. 

# Installation
```
helm repo add dyno-charts https://dynomite567.github.io/helm-charts/
helm install --create-namespace --namespace immich immich dyno-charts/immich -f values.yaml
```
or
```
helm install --create-namespace --namespace immich immich oci://ghcr.io/dynomite567/helm-charts/immich -f values.yaml
```

You should not copy the full values.yaml from this repository. Only set the values that you want to override.

There are a few things that you are required to configure in your values.yaml before installing the chart:
* You need to do one of the followoing:
  * Separately create a PVC for your library volume and configure `immich.persistence.library.existingClaim` to reference that PVC.
  * Have an accessible NFS share for your library and configure `immich.persistence.library.nfs.server` and `immich.persistence.library.nfs.path` to reference that NFS server.
* You need to make sure that Immich has access to a redis and postgresql instance. 
  * Redis can be enabled directly in the values.yaml, or by manually setting the `env` to point to an existing instance.
  * You need to deploy a suitable postgres instance with the pgvecto.rs extension yourself.
* You need to set `image.tag` to the version you want to use, as this chart does not update with every Immich release.

# Configuration

The immich chart is highly customizable. You can see a detailed documentation
of all possible changes within the `charts/immich/values.yaml` file.

## Chart architecture 

This chart uses the [common library](https://github.com/bjw-s-labs/helm-charts/tree/923ef40a39520979c98f354ea23963ee54f54433/charts/library/common). The top level `env` and `image` keys are applied to every component of the Immich stack, and the entries under the `server`, `microservices`, etc... keys define the specific values for each component. You can freely add more top level keys to be applied to all the components, please reference [the common library's values.yaml](https://github.com/bjw-s-labs/helm-charts/blob/923ef40a39520979c98f354ea23963ee54f54433/charts/library/common/values.yaml) to see what keys are available.

## Uninstalling the Chart

To see the currently installed Immich chart:

```console
helm ls --namespace immich
```

To uninstall/delete the `immich` chart:

```console
helm delete --namespace immich immich
```

The command removes all the Kubernetes components associated with the chart and deletes the release.