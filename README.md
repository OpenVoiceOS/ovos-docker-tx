# ovos-docker-tx

Docker image definitions for Open Voice OS translation (tx) microservices. Each image runs [`ovos-translate-server`](https://github.com/OpenVoiceOS/ovos-translate-server) over HTTP and serves translation and language detection through a specific engine plugin.

## Install

This repo has no Python package. It only ships Dockerfiles and a build script. Build the images locally:

```bash
docker build -f base/Dockerfile -t smartgic/ovos-tx-server-base:alpha ./base
docker build -f nllb/Dockerfile -t smartgic/ovos-tx-server-nllb:alpha ./nllb
docker build -f google/Dockerfile -t smartgic/ovos-tx-server-google:alpha ./google
```

The engine images (`nllb`, `google`) build `FROM smartgic/ovos-tx-server-base:${TAG}`, so build the base image first, or pull it, before you build an engine image. Pass `--build-arg ALPHA=true` to install pre-release or git-based dependencies instead of the released packages.

## Usage

Each engine image starts `ovos-translate-server` with a fixed translation and language-detection engine:

- `nllb/Dockerfile` runs the server with [`ovos-translate-plugin-nllb`](https://github.com/OpenVoiceOS/ovos-translate-plugin-nllb) for translation and [`ovos-lang-detector-fasttext-plugin`](https://github.com/OpenVoiceOS/ovos-lang-detector-fasttext-plugin) for language detection.
- `google/Dockerfile` runs the server with [`ovos-google-translate-plugin`](https://github.com/OpenVoiceOS/ovos-google-translate-plugin) for translation and [`ovos-google-lang-detector-plugin`](https://github.com/OpenVoiceOS/ovos-google-lang-detector-plugin) for language detection.

Runtime configuration comes from `.env`, which sets `CONFIG_FOLDER`, `OVOS_USER`, `TZ`, and `VERSION`.

### Layout

- `base/Dockerfile`: Debian bookworm-slim base image with a Python virtual environment under `/home/ovos/.venv`. The engine images build on this shared layer.
- `base/Dockerfile.cuda`: CUDA 11.8 / cuDNN variant of the base image.
- `nllb/Dockerfile`: installs `ovos-translate-server` and `ovos-translate-plugin-nllb` (plus `ovos-lang-detector-fasttext-plugin` when `ALPHA=true`).
- `nllb/Dockerfile.cuda`: CUDA variant that builds `FROM smartgic/ovos-tx-server-base-cuda` instead of the CPU base image.
- `google/Dockerfile`: installs `ovos-translate-server` and `ovos-google-translate-plugin`.

This repo is not a Python plugin or skill. It has no `pyproject.toml` and no entry-point group. The OVOS plugins each image installs live in their own repos, linked above.

## Related projects

- [OpenVoiceOS/ovos-translate-server](https://github.com/OpenVoiceOS/ovos-translate-server): the HTTP server every image in this repo runs.
- [OpenVoiceOS/ovos-translate-plugin-nllb](https://github.com/OpenVoiceOS/ovos-translate-plugin-nllb): the NLLB translation engine used by the `nllb` image.
- [OpenVoiceOS/ovos-google-translate-plugin](https://github.com/OpenVoiceOS/ovos-google-translate-plugin): the Google translation engine used by the `google` image.
- [OpenVoiceOS/ovos-lang-detector-fasttext-plugin](https://github.com/OpenVoiceOS/ovos-lang-detector-fasttext-plugin): the fastText language detector used by the `nllb` image.
- [OpenVoiceOS/ovos-google-lang-detector-plugin](https://github.com/OpenVoiceOS/ovos-google-lang-detector-plugin): the Google language detector used by the `google` image.
