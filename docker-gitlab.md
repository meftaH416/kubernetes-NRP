1. Create folder in local desktop
2. move there [cd <>]
3. git init
4. In gitlab, create a project/repo
5. git pull <repo>
6. add Dockerfile and requirements.txt file and push to gitlab <br>
   git remote add origin https://gitlab.nrp-nautilus.io/meftahuddin416/deepcoder-finetune.git <br>
   git branch -M main <br>
   git push -uf origin main <br> 
   
## Dockerfile
FROM nvidia/cuda:12.1.0-cudnn8-devel-ubuntu22.04

WORKDIR /app

#### Install system dependencies
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

#### Set Python 3 as default
RUN ln -s /usr/bin/python3 /usr/bin/python

#### Copy requirements file
COPY requirements.txt .

#### Install Python dependencies including Jupyter
RUN pip install --no-cache-dir -r requirements.txt \
    && pip install --no-cache-dir jupyter notebook

#### Expose Jupyter port
EXPOSE 8888

#### Entry point for Jupyter Notebook
CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root", "--NotebookApp.token=''", "--NotebookApp.password=''"]

## Requirements (based on pytorch)
torch==2.5.1 <br>
torchvision==0.20.1  <br>
torchaudio==2.5.1 --index-url https://download.pytorch.org/whl/cu121 <br>
transformers==4.44.2 <br>
datasets==2.21.0 <br>
peft==0.12.0 <br>
bitsandbytes==0.43.3 <br>
accelerate==0.33.0 <br>
trl==0.9.6 <br>
flash-attn==2.6.3 <br>

