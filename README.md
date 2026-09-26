# XIAO MG24 OpenThread RCP firmware

This repository is a personal fork of [Nabu Casa's siliconlabs-firmware-builder](https://github.com/NabuCasa/silabs-firmware-builder). Use the upstream repository for Nabu Casa and Home Assistant firmware. This fork adds buildable OpenThread RCP images for the Seeed Studio XIAO MG24.

## What was added

The XIAO MG24 target is based on Nabu Casa's OpenThread RCP project and targets the `EFR32MG24B220F1536IM48` device. It uses:

- EUSART0 on PA8/PA9
- 460800 baud
- No hardware flow control
- A 512-byte receive buffer
- A 4096-byte OpenThread transmit buffer
- Nabu Casa's OpenThread UART reliability patches
- PB5 high to enable the XIAO RF switch
- PB4 to select the antenna

The base project is in [`src/openthread_rcp_xiao`](src/openthread_rcp_xiao). The antenna variants are selected at compile time:

- [`xiao_mg24_openthread_rcp_external.yaml`](manifests/custom/xiao_mg24_openthread_rcp_external.yaml) selects the external antenna.
- [`xiao_mg24_openthread_rcp_internal.yaml`](manifests/custom/xiao_mg24_openthread_rcp_internal.yaml) selects the onboard antenna.

## Prebuilt images

Prebuilt images are kept in [`artifacts/`](artifacts/):

- [XIAO MG24 OpenThread RCP HEX](artifacts/xiao_mg24_openthread_rcp_3.1.1.0_GitHub-fb274efe6_gsdk_2026.6.1.hex)
- [XIAO MG24 OpenThread RCP internal-antenna HEX](artifacts/xiao_mg24_openthread_rcp_internal_3.1.1.0_GitHub-fb274efe6_gsdk_2026.6.1.hex)

The second link names the internal-antenna artifact produced by the internal manifest and can be committed alongside the existing image.

## Build both images

The Docker image supplies the Linux Silicon Labs Configurator, SDK, toolchain, and Nabu Casa build scripts. From the repository root:

```bash
for antenna in external internal; do
  docker run --rm -v "$(pwd):/repo" \
    ghcr.io/nabucasa/silabs-firmware-builder \
    --manifest "manifests/custom/xiao_mg24_openthread_rcp_${antenna}.yaml" \
    --output hex \
    --output-dir artifacts
done
```

The resulting HEX images are written to `artifacts/` and can be programmed directly with OpenOCD or another SWD programmer.
