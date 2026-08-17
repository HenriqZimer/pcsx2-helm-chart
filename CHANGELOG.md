# Changelog

All notable changes to this Helm chart will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.2.1] - 2026-08-16

### Added
- `icon` in Chart.yaml, pointing at linuxserver.io's own logo image for this app - was missing entirely before, which is why no image ever showed up on ArtifactHub for this chart.

## [1.2.0] - 2026-08-13

### Added
- `gpu.vendor: nvidia` mode (alongside the existing `intel-amd` VA-API mode), requesting `nvidia.com/gpu`
  via the NVIDIA Kubernetes device plugin and setting `NVIDIA_VISIBLE_DEVICES`/`NVIDIA_DRIVER_CAPABILITIES`.
  Documented the `nvidia-drm.modeset=1` kernel parameter (and, on headless nodes, a dummy HDMI/DP plug)
  needed on the node for the compositor to render anything instead of a solid black desktop.
- `seccompUnconfined` to work around PCSX2's JIT recompiler crashing with `SIGBUS` under the default
  seccomp profile on some kernel/libseccomp combinations.

### Changed
- Bumped the default `shmSize` from `1Gi` to `2Gi` — PCSX2's EE recompiler uses a shared-memory-backed
  "fastmem" mapping that crashes with `SIGBUS` right after a game launch (not on the dashboard) if
  `/dev/shm` is too small.

## [1.1.1] - 2026-08-12

### Changed
- Clarified the streaming README section: `broker_host` is server-to-server (cluster-internal DNS is fine, keep it off any externally-reachable Ingress/LoadBalancer), while `host` is opened directly by the end user's browser and needs real external reachability + TLS. Documented that the container's self-signed cert has no SAN, which some browsers (notably Safari) refuse outright instead of showing the usual clickthrough warning.

## [1.1.0] - 2026-08-12

### Added
- `streaming.enabled`/`streaming.brokerPort` to expose the RomM emulator-streaming broker sidecar port. The chart doesn't install the broker itself — pair with `env.DOCKER_MODS: ghcr.io/loneangelfayt/pcsx2-romm-integration-mod:latest` and mount your ROMs library at the same path RomM uses. See [Emulator Streaming](https://docs.romm.app/latest/using/emulator-streaming/).

## [1.0.0] - 2026-08-12

### Added
- Initial release of the PCSX2 Helm chart
- Deployment, Service, optional Ingress and PVC for the linuxserver.io PCSX2 KasmVNC webtop image
- Configurable `serviceAccount` (create/name/annotations)
- Readiness and liveness probes (TCP check on the KasmVNC HTTP port)
- Optional VA-API (`/dev/dri`) GPU passthrough via `gpu.enabled`
- `extraVolumes`/`extraVolumeMounts` for mounting an existing ROMs/BIOS library
