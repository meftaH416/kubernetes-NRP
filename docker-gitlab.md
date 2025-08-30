1. Create folder in local desktop
2. move there [cd <>]
3. git init
4. In gitlab, create a project/repo
5. Go to Preference by clicking the Gitlab Name. Then go to Access tokens and create token with a name.
6. git pull <repo>
7. add Dockerfile and requirements.txt file and push to gitlab <br>
   git remote add origin https://gitlab.nrp-nautilus.io/meftahuddin416/deepcoder-finetune.git <br>
   git branch -M main <br>
   -- First time use:  git push https://meftahuddin416:glpat-xxxxxxxxxxxxxxxxxxxx@gitlab.nrp-nautilus.io/meftahuddin416/deepcoder-finetune.git main
   -- after that just use: git push -uf origin main <br> 
   
## Step 1: Dockerfile
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

## Step 2: Requirements (based on pytorch)
torch==2.5.1 <br>
torchvision==0.20.1  <br>
torchaudio==2.5.1 --index-url https://download.pytorch.org/whl/cu121 <br>
transformers==4.44.2 <br>
datasets==2.21.0 <br>
peft==0.12.0 <br>
bitsandbytes==0.43.3 <br>
accelerate==0.33.0 <br>
trl==0.9.6 <br>

## Step 3: .gitlab-ci.yml 
-- https://nrp.ai/documentation/userdocs/development/gitlab/ <br>
image: gcr.io/kaniko-project/executor:debug

stages:
  - build-and-push

build-and-push-job:
  stage: build-and-push
  variables:
    GODEBUG: "http2client=0"
  script:
    - echo "{\"auths\":{\"$CI_REGISTRY\":{\"username\":\"$CI_REGISTRY_USER\",\"password\":\"$CI_REGISTRY_PASSWORD\"}}}" > /kaniko/.docker/config.json
    - /kaniko/executor --cache=true --push-retry=10 --context $CI_PROJECT_DIR --dockerfile $CI_PROJECT_DIR/Dockerfile --destination $CI_REGISTRY_IMAGE:latest
  only:
    - main

## Step 4: Configuring GitLab CI/CD Variables

This guide explains how to configure CI/CD variables (`CI_REGISTRY_USER` and `CI_REGISTRY_PASSWORD`) for the `deepcoder-finetune` project to enable the `.gitlab-ci.yml` pipeline to build and push the Docker image (`registry.gitlab.nrp-nautilus.io/meftahuddin416/deepcoder-finetune:latest`).

### Understanding CI/CD Variables Fields

#### Type
- **Options**:
  - **Variable**: Standard key-value pair (e.g., `CI_REGISTRY_USER=meftahuddin416`).
  - **File**: Stores value as a file path (not needed).
- **Choice**: Use **Variable** for `CI_REGISTRY_USER` and `CI_REGISTRY_PASSWORD` (simple text values for `.gitlab-ci.yml`).

#### Visibility
- **Options**:
  - **Visible**: Value appears in job logs (insecure for tokens).
  - **Masked**: Value hidden in logs, revealable in settings (requires alphanumeric, 8+ characters).
  - **Masked and hidden**: Value hidden in logs, non-revealable after saving.
- **Choice**:
  - `CI_REGISTRY_USER`: **Visible** (username `meftahuddin416` is not sensitive).
  - `CI_REGISTRY_PASSWORD`: **Masked** (token meets regex; allows updates).

#### Flags
- **Options**:
  - **Protect variable**: Restricts to protected branches/tags (e.g., `main`).
  - **Expand variable reference**: Allows referencing other variables (e.g., `$OTHER_VAR`).
- **Choice**:
  - Check **Protect variable** (pipeline runs on `main`, per `.gitlab-ci.yml`).
  - Uncheck **Expand variable reference** (static values).

#### Description (Optional)
- **Choice**:
  - `CI_REGISTRY_USER`: “GitLab username for container registry authentication.”
  - `CI_REGISTRY_PASSWORD`: “Personal access token for container registry push.”

#### Key
- Variable name (e.g., `CI_REGISTRY_USER`, `CI_REGISTRY_PASSWORD`).
- **Precedence** (highest to lowest):
  1. Pipeline-specific variables (trigger/manual run).
  2. Project variables (set in Settings -> CI/CD -> Variables).
  3. Group variables.
  4. Instance variables (set by NRP admins).
  5. Predefined variables (e.g., `$CI_REGISTRY`).
- **Impact**: Project variables override group/instance variables with the same name.

#### Value
- **Choice**:
  - `CI_REGISTRY_USER`: `meftahuddin416`
  - `CI_REGISTRY_PASSWORD`: Personal access token (e.g., `glpat-xxxxxxxxxxxxx`) with `api`, `write_repository`, `write_registry` scopes.

### Step-by-Step Configuration

1. **Access CI/CD Variables**:
   - Go to `https://gitlab.nrp-nautilus.io/meftahuddin416/deepcoder-finetune`.
   - Log in as `meftahuddin416`.
   - Navigate to **Settings** -> **CI/CD** -> **Variables** -> **Expand**.

2. **Add `CI_REGISTRY_USER`**:
   - Click **Add variable**.
   - Set:
     - **Key**: `CI_REGISTRY_USER`
     - **Value**: `meftahuddin416`
     - **Type**: Variable
     - **Visibility**: Visible
     - **Protect variable**: Check
     - **Description**: “GitLab username for container registry authentication.”
   - Click **Add variable**.

3. **Add `CI_REGISTRY_PASSWORD`**:
   - Click **Add variable**.
   - Set:
     - **Key**: `CI_REGISTRY_PASSWORD`
     - **Value**: `glpat-xxxxxxxxxx` (or new token)
     - **Type**: Variable
     - **Visibility**: Masked
     - **Protect variable**: Check
     - **Description**: “Personal access token for container registry push.”
   - Click **Add variable**.

4. **Verify Token Scopes**:
   - Go to Profile picture -> **Preferences** -> **Access Tokens**.
   - Ensure the token has `api`, `write_repository`, `write_registry` scopes.
   - If invalid, create a new token:
     - Name: `deepcoder-finetune-cicd`
     - Scopes: `api`, `write_repository`, `write_registry`
     - Click **Create personal access token**, copy the token, and update `CI_REGISTRY_PASSWORD`.

5. **Check Runners**:
   - Go to **Settings** -> **CI/CD** -> **Runners**.
   - Verify shared or group runners exist (NRP-provided).
   - If none, contact NRP admin via `https://nrp.ai/documentation` to request a runner.

6. **Test Pipeline**:
   - Push changes to `.gitlab-ci.yml`, `Dockerfile`, or `requirements.txt` to trigger the pipeline.
   - Check **CI/CD** -> **Pipelines** for status.
   - Verify image in **Deploy** -> **Container Registry** (`deepcoder-finetune:latest`).


