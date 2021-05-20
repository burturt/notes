kubernetes

`kubectl config current-context`
`kubectl config use-context`

```
gcloud container clusters create trainingcluster    --num-nodes=3 --enable-autoupgrade --no-enable-basic-auth    --no-issue-client-certificate --enable-ip-alias --metadata    disable-legacy-endpoints=true  --zone us-west1-a
kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.4
kubectl expose deployment hello-node --type=LoadBalancer --port=8080
```