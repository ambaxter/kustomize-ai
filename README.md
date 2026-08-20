```bash
export gitops_repo=https://github.com/ambaxter/kustomize-ai.git
export cluster_name=local
export cluster_base_domain=$(oc get ingress.config.openshift.io cluster --template={{.spec.domain}} | sed -e "s/^apps.//")
export platform_base_domain=${cluster_base_domain#*.}

oc apply -f .bootstrap/gitops-operator.yaml
oc apply -f .bootstrap/cluster-rolebinding.yaml
sleep 60
oc project openshift-gitops
oc apply -f .bootstrap/envsubst.yaml
oc start-build envsub -w
envsubst < .bootstrap/argocd.yaml | oc apply -f -
sleep 30
envsubst < .bootstrap/root-application.yaml | oc apply -f -
```