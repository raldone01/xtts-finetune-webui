# syntax = docker/dockerfile:1.4
FROM python:3.10

# Install system dependencies
RUN --mount=type=cache,target=/var/cache/apt \
  apt-get update && \
  apt-get install -y --no-install-recommends net-tools nano rustc cargo

ENV PYTHONDONTWRITEBYTECODE=1

# Create and activate the xtts environment
RUN python -m venv /opt/xtts && \
  /opt/xtts/bin/pip install --upgrade pip

# Set the PATH to prioritize the virtualenv
ENV PATH="/opt/xtts/bin:$PATH"

WORKDIR /app

COPY . .

ARG TARGET=cuda
RUN --mount=type=cache,target=/root/.cache/pip \
  if [ "$TARGET" = "cuda" ]; then \
  pip install torch torchaudio --index-url https://download.pytorch.org/whl/cu121; \
  elif [ "$TARGET" = "rocm" ]; then \
  pip install torch torchaudio --index-url https://download.pytorch.org/whl/rocm5.2; \
  else \
  pip install torch torchaudio; \
  fi; \
  pip install -r requirements.txt

EXPOSE 5003
VOLUME [ "/root/.cache/huggingface", "/data"]
ENTRYPOINT ["python", "xtts_demo.py", "--port", "5003", "--out_path", "/data/output", "--server_name", "0.0.0.0"]
