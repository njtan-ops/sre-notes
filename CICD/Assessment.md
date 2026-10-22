```
[ntan@ntan-a2t-aut-cid1 ~]$ flux get sources git
flux get kustomizations
kubectl -n flask-app get pods,svc,endpoints
NAME       REVISION          SUSPENDED READY MESSAGE                            
flux-system main@sha1:10f68fb6 False    True stored artifact for revision 'main@sha1:10f68fb6'
NAME                         REVISION          SUSPENDED READY MESSAGE           
flux-system                  main@sha1:10f68fb6 False    True Applied revision: main@sha1:10f68fb6
python-flaskapp-kustomization main@sha1:10f68fb6 False    True Applied revision: main@sha1:10f68fb6
**Warning:** v1 Endpoints is deprecated in v1.33+; use discovery.k8s.io/v1 EndpointSlice
NAME                                   READY   STATUS    RESTARTS   AGE
pod/python-flaskapp-7fdb579576-7lm59   1/1     Running   0          3m49s
pod/python-flaskapp-7fdb579576-zxjk8   1/1     Running   0          3m40s
NAME                         TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/flask-app-nodeport   NodePort   10.96.164.11   <none>        5000:30024/TCP   19m
NAME                           ENDPOINTS                           AGE
endpoints/flask-app-nodeport   10.244.1.29:5000,10.244.1.30:5000   19m
```