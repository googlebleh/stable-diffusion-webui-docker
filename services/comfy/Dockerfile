FROM pytorch/pytorch:2.1.2-cuda12.1-cudnn8-runtime

ENV DEBIAN_FRONTEND=noninteractive PIP_PREFER_BINARY=1

ENV NVARCH x86_64
RUN apt-get update && apt-get install -y --no-install-recommends \
    gnupg2 curl ca-certificates && \
    curl -fsSL https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/${NVARCH}/3bf863cc.pub | apt-key add - && \
    echo "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/${NVARCH} /" > /etc/apt/sources.list.d/cuda.list && \
    apt-get purge --autoremove -y curl \
    && rm -rf /var/lib/apt/lists/*

# COPY --from=nvidia/cuda:12.1.1-cudnn8-runtime-ubuntu22.04 /cuda-keyring_1.0-1_all.deb /opt/
# RUN apt install /opt/cuda-keyring_1.0-1_all.deb

RUN apt-get update && apt-get install -y git && apt-get clean
RUN apt install -y cuda-cudart-12-1 cuda-compat-12-1 cuda-libraries-12-1 libnpp-12-1 cuda-nvtx-12-1 libcusparse-12-1 libcublas-12-1 libnccl2 libcudnn8

ENV ROOT=/stable-diffusion
RUN \
  git clone https://github.com/comfyanonymous/ComfyUI.git ${ROOT} && \
  cd ${ROOT} && \
  git checkout master && \
  git reset --hard d1f3637a5a944d0607b899babd8ff11d87100503 && \
  pip install -r requirements.txt

WORKDIR ${ROOT}
COPY . /docker/
RUN chmod u+x /docker/entrypoint.sh && cp /docker/extra_model_paths.yaml ${ROOT}

ENV NVIDIA_VISIBLE_DEVICES=all PYTHONPATH="${PYTHONPATH}:${PWD}" CLI_ARGS=""
EXPOSE 7860
ENTRYPOINT ["/docker/entrypoint.sh"]
CMD python -u main.py --listen --port 7860 ${CLI_ARGS}
