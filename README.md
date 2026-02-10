## 1 Get your namespace
Get namespace from your institution. Student can be user and a Postdoc or Professor can be admin.

## 2 Get kubectl tool
- Go to https://nrp.ai/documentation/userdocs/start/getting-started/
- For windows download kubectl.exe from https://kubernetes.io/releases/download/#binaries
- Create a folder in Program File C:\Program Files\kubectl and move kubectl.exe there
- Search Environment Variable--Environment Varible--System Variable--Path--Edit--New--
- Add C:\Program Files\kubectl\ and OK

## 3 Download kubelogin
- Go to https://github.com/int128/kubelogin/releases
- Download for window, same version as kubectl
- Unzip and move kubelogin.exe to C:\Program Files\kubectl
- Rename kubelogin.exe to kubectl-oidc_login.exe

## 4 Download Config file
- Create new folder in user directory named .kube
- Move config file there
