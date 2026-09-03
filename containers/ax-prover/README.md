# AxProver Apptainer image

Build the AxProver wheel and an ARM64 OCI image on a Docker BuildKit host:

```bash
uv build --wheel
docker buildx build \
  --platform linux/arm64 \
  --build-arg LEAN_TOOLCHAIN=leanprover/lean4:v4.24.0 \
  --tag REGISTRY/ax-prover:VERSION-arm64 \
  --push \
  -f containers/ax-prover/Dockerfile .
```

On Helios, convert the image once and retain the SIF in group storage:

```bash
apptainer pull --arch arm64 \
  /absolute/group/path/ax-prover-VERSION-arm64.sif \
  docker://REGISTRY/ax-prover:VERSION-arm64
```

The image contains AxProver and a pinned Lean toolchain, but no project,
credentials, or output data. The Helios job must bind the writable Lean project
into the client container.
