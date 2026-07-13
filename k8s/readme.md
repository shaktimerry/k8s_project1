Taints

kubectl taint nodes desktop-control-plane dedicated=project1:NoSchedule

kubectl label node desktop-control-plane environment=production


Rolling Update :

strategy:
    type: RollingUpdate

    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1

**********************
Manifest Pattern :

apiversion :
kind :

metadata: name labels

spec : replicas strategy selector

template : 
 metadata

 spec: tolerance , affinity

 container : envFrom , image , volumeMount

Volume

***********************************

HPA : It needs a metric server to be used

In Container :

resources:
  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"

**********************************
VPA : Install VPA controllers first as VPA is a CRD

git clone https://github.com/kubernetes/autoscaler.git
cd autoscaler/vertical-pod-autoscaler
kubectl apply -f deploy/vpa-v1-crd-gen.yaml
kubectl apply -f deploy/
kubectl get pods -n kube-system | grep vpa

In Container Put resource request