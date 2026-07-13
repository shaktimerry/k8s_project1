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