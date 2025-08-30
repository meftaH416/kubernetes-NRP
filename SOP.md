### Create PVC from bash
kubectl apply -f pvc-finetune.yaml
### Create deployment 
kubectl apply -f dep-finetune-jupyter.yaml
### Run deployment
kubectl get pods -l k8s-app=dep-finetune-jupyter <br>
-- If not running: kubectl describe pod -l k8s-app=dep-finetune-jupyter <br>
kubectl get pods -l k8s-app=dep-finetune-jupyter -o name <br>
kubectl port-forward pod/dep-finetune-jupyter-xxxxxxxx-yyyy 8888:8888 <br>


## After code is run.. delete everything
kubectl delete deployment dep-finetune-jupyter <br>
kubectl delete service dep-finetune-jupyter-service <br>
kubectl delete pvc pvc-finetune <br>
