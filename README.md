

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

# 작성한 헬름차트 일단 깃헙에 푸쉬
# 폴더준비
mkdir -p docs

###### 압축해서 넣기 => XXX.tgz ###############################################
helm package charts/helm01_member -d docs/
###### 인덱스파일을 docs/폴더에 생성하기 => index.yaml ##########################
helm repo index docs/ --url https://rush7993.github.io/my-helm-chart/

[user1@master my-helm-chart]$ git add ./docs
[user1@master my-helm-chart]$ git commit -m "release: member-app chart v1.0.0 package"
[master f7a7668] release: member-app chart v1.0.0 package
 2 files changed, 14 insertions(+)
 create mode 100644 docs/index.yaml
 create mode 100644 docs/member-app-0.1.0.tgz


[user1@master my-helm-chart]$ git push origin master


깃헙 세팅 페이지 설정 후 세이브 하면 액션에서 자동으로 반영

$helm repo add my-repo https://rush7993.github.io/my-helm-chart/
"my-repo" has been added to your repositories

[user1@master my-helm-chart]$ helm repo ls
NAME    URL                                      
cnpg    https://cloudnative-pg.github.io/charts  
argo    https://argoproj.github.io/argo-helm     
my-repo https://rush7993.github.io/my-helm-chart/

helm repo update
helm search repo my-repo
NAME                    CHART VERSION   APP VERSION     DESCRIPTION               
my-repo/member-app      0.1.0           1.0.0           fastapi member application

# 깃헙에서 내려받은 헬름차트 설치배포하기
# 네임스페이스 있으면 삭제하고 밑에진행
kubectl delete ns helm01
helm install member-release my-repo/member-app -n helm01 --create-namespace
kubectl get pod,svc -n helm01

helm ls -n helm01

helm uninstall member-release -n helm01
kubectl delete ns helm01



-------------------------
[user1@master charts]$ helm install micro-release ./helm02_micro -n helm02 --create-namespace
에러나서 고치고 업그레이드
[user1@master charts]$ helm upgrade micro-release ./helm02_micro -n helm02

[user1@master charts]$ helm ls -n helm02
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
micro-release   helm02          2               2026-07-01 09:41:03.162563566 +0900 KST deployed        market-app-0.1.0        1.0.0      
[user1@master charts]$ 

user1@master charts]$ kubectl get pod,svc -n helm02
NAME                              READY   STATUS    RESTARTS   AGE
pod/market-app-75d6cc8fd8-qtztj   1/1     Running   0          2m
pod/market-app-75d6cc8fd8-z2tvd   1/1     Running   0          2m

NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/svc-market-app   ClusterIP   10.105.201.167   <none>        8000/TCP   5m15s
[user1@master charts]$ 

[user1@master charts]$ helm uninstall micro-release -n helm02
release "micro-release" uninstalled
[user1@master charts]$ kubectl get pod,svc -n helm02
No resources found in helm02 namespace.
[user1@master charts]$ 

### 깃헙 배포 자동 생성을 위해  아래를 실행 하고 푸쉬하면 배포가 자동을 된다.
[user1@master my-helm-chart]$  helm package charts/helm02_micro -d docs/
Successfully packaged chart and saved it to: docs/market-app-0.1.0.tgz
[user1@master my-helm-chart]$ helm repo index docs/ --url https://rush7993.github.io/my-helm-chart/

git add .
git commit -m "helm01_micro chart 추가"
git push

git repo ls