install kubectl
https://kubernetes.io/docs/tasks/tools/

install docker
curl -fsSL https://get.docker.com -o get-docker.sh
 sudo sh get-docker.sh


export REGISTRY_SERVER=https://index.docker.io/v1/

# Replace `[...]` with the registry username
export REGISTRY_USER=[...]

# Replace `[...]` with the registry password
export REGISTRY_PASS=[...]

# Replace `[...]` with the registry email
export REGISTRY_EMAIL=[...]

kubectl create namespace tekton-builds

kubectl --namespace tekton-builds \
    create secret \
    docker-registry regcred \
    --docker-server=$REGISTRY_SERVER \
    --docker-username=$REGISTRY_USER \
    --docker-password=$REGISTRY_PASS \
    --docker-email=$REGISTRY_EMAIL


kubectl apply --filename cd.yaml

kubectl --namespace tekton-builds \
    get pipelines

############################
# Running Tekton pipelines #
############################

tkn --namespace tekton-builds \
    pipeline start toolkit \
    --dry-run

cat cd-run.yaml

kubectl create --filename cd-run.yaml

tkn --namespace tekton-builds \
    pipelinerun list

tkn --namespace tekton-builds \
    pipelinerun logs --last --follow

kubectl --namespace tekton-builds \
    get pods

#################
# Tekton Web UI #
#################

echo http://tekton.$BASE_HOST

# Open the URL
helm install geared-marsupi ./mychart --dry-run --debug

kubectl create ns prod
kubectl create ns staging