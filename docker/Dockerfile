# Use NVIDIA CUDA base image
FROM nvidia/cuda:11.8.0-devel-ubuntu22.04 AS builder

# Set environment variables
ENV DEBIAN_FRONTEND=noninteractive
ENV PYTHONUNBUFFERED=1

# Set working directory
WORKDIR /app

# Install system dependencies
RUN apt-get update -y && \
    apt-get install -y curl python3 python3-pip git libgl1-mesa-glx libglib2.0-0 && \
    rm -rf /var/lib/apt/lists/*

# Copy and install Python dependencies
COPY requirements.txt /app/requirements.txt
RUN pip3 install --no-cache-dir -r requirements.txt

# Download model and dataset
RUN python3 -c "from huggingface_hub import snapshot_download; snapshot_download(repo_id='xiaodongguaAIGC/llama-3-debug', local_dir='/app/model')"
#RUN python3 -c "from huggingface_hub import snapshot_download; snapshot_download(repo_id='NousResearch/Meta-Llama-3-8B-Instruct', local_dir='/app/model')"

# Download dataset at build time
RUN python3 -c "from datasets import load_dataset; dataset = load_dataset('jmaczan/rick-and-morty-scripts-llama-2', split='train'); dataset.save_to_disk('/app/dataset')"

# Copy application files
COPY Dockerfile /app/Dockerfile
COPY main.py /app/main.py
COPY requirements.txt /app/requirements.txt
COPY start.sh /app/start.sh

RUN ls -la /app

# Make start.sh executable
RUN chmod +x /app/start.sh

# Set the entrypoint #
ENTRYPOINT ["/app/start.sh"]