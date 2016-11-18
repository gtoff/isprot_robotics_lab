
# Export the google app credentials
export GOOGLE_APPLICATION_CREDENTIALS=~/.ssh/ISPROT-Collab-8b086947beba.json

# copy the config file in your .kube directory
mkdir -p ~/.kube
cp config ~/.kube/config

# test if you can see the cluster
kubectl cluster-info
