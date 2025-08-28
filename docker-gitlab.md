1. Create folder in local desktop
2. move there [cd <>]
3. git init
4. In gitlab, create a project/repo
5. git pull <repo>
6. add Dockerfile and requirements.txt file and push to gitlab
   git remote add origin https://gitlab.nrp-nautilus.io/meftahuddin416/deepcoder-finetune.git
   git branch -M main
   git push -uf origin main
   
### Dockerfile
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

# Entry point for Jupyter Notebook
CMD ["jupyter", "notebook", "--ip=0.0.0.0", "--port=8888", "--no-browser", "--allow-root", "--NotebookApp.token=''", "--NotebookApp.password=''"]

