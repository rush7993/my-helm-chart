

helm ls -n helm01
k get pod,svc -n helm01

[user1@master charts]$ helm install member-release ./helm01_member -n helm01 --create-namespace
NAME: member-release
LAST DEPLOYED: Mon Jun 29 10:37:19 2026
NAMESPACE: helm01
STATUS: deployed
REVISION: 1
TEST SUITE: None


[user1@master charts]$ helm ls -n helm01
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
member-release  helm01          1               2026-06-29 10:37:19.636451687 +0900 KST deployed        member-app-0.1.0        1.0.0     



[user1@master charts]$ k get pod,svc -n helm01
NAME                              READY   STATUS    RESTARTS   AGE
pod/member-app-5d4bff9458-l2bt6   1/1     Running   0          60s
pod/member-app-5d4bff9458-twzbw   1/1     Running   0          60s
pod/member-app-5d4bff9458-wm8vm   1/1     Running   0          60s

NAME                         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/member-app-service   ClusterIP   10.108.88.124   <none>        8000/TCP   60s


helm uninstall member-release -n helm01

