FROM ubuntu:24.04

ARG DEBIAN_FRONTEND=noninteractive
ARG LEAN_TOOLCHAIN=leanprover/lean4:v4.24.0

ENV PYTHONUNBUFFERED=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    ELAN_HOME=/opt/elan \
    PATH=/opt/venv/bin:/opt/elan/bin:${PATH}

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        ca-certificates \
        curl \
        git \
        libgmp-dev \
        python3 \
        python3-venv \
        ripgrep \
        unzip \
        zstd \
    && rm -rf /var/lib/apt/lists/*

RUN python3 -m venv /opt/venv \
    && curl --proto '=https' --tlsv1.2 -sSf \
        -o /tmp/elan-init.sh \
        https://raw.githubusercontent.com/leanprover/elan/master/elan-init.sh \
    && sh /tmp/elan-init.sh -y --default-toolchain "${LEAN_TOOLCHAIN}" \
    && rm /tmp/elan-init.sh \
    && chmod -R a+rX /opt/elan

# Build the wheel first with `uv build --wheel`. Keeping the project and its
# mutable Lake artifacts outside the image makes one SIF reusable across runs.
COPY dist/ /tmp/ax-prover-wheels/
RUN set -- /tmp/ax-prover-wheels/ax_prover-*.whl \
    && test "$#" -eq 1 \
    && pip install --no-cache-dir "$1" \
    && rm -rf /tmp/ax-prover-wheels \
    && lean --version \
    && lake --version \
    && ax-prover --help >/dev/null

WORKDIR /workspace

LABEL org.opencontainers.image.title="AxProver Helios client" \
      org.opencontainers.image.description="ARM64-capable AxProver and Lean runtime for Apptainer"

CMD ["ax-prover", "--help"]
