### Create PVC from bash
kubectl apply -f pvc-finetune.yaml
### Create deployment 
kubectl apply -f dep-finetune-jupyter.yaml
kubectl get pods -l k8s-app=dep-finetune-jupyter
-- If not running: kubectl describe pod -l k8s-app=dep-finetune-jupyter
kubectl get pods -l k8s-app=dep-finetune-jupyter -o name
kubectl port-forward pod/dep-finetune-jupyter-xxxxxxxx-yyyy 8888:8888


## After code is run.. delete everything
kubectl delete deployment dep-finetune-jupyter
kubectl delete service dep-finetune-jupyter-service
kubectl delete pvc pvc-finetune
