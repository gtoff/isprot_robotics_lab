# ISPROT robotics lab

## Part 1:

### Installation of kubectl:

Inside your vm open up a new terminal and become root:

for example, run sudo su -

A new apt-key is needed for downloading and installing the kubectl package. Use the commands to below add the keys and install kubectl. 


    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF > /etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
    EOF
    apt-get update
    apt-get install -y kubectl

* Copy the kubernetes config file from OLAT to /home/<your user>/.kube/
* Copy the google app credentials from OLAT, the file is called: ISPROT-Collab-8b086947beba.json
* Export the google app credentials


    export GOOGLE_APPLICATION_CREDENTIALS=<path to ISPROT-Collab-8b086947beba.json>

You should now be able to run “kubectl cluster-info” in a terminal. It should print out information about the cluster.

### Use your group namespace

Set namespace to your namespace by setting a local context:

    
    kubectl config set-context gke_isprot-collab_us-central1-a_cluster-isprot --namespace=groupX

Use the local context you created:	


    kubectl config use-context gke_isprot-collab_us-central1-a_cluster-isprot

Test that your namespace is empty:

    kubectl get all

### Deploying the components to the cluster:

* Clone the provided git repository with the kubernetes templates needed. 


    Git clone https://github.com/gtoff/isprot_robotics_lab.git

* In the cloned repo there are two folders, one for each part of the exercise. 

* In the part1 folder you’ll find a list of kubernetes descriptors of pods and services. 

* The kubernetes templates for the map_server_pod is missing. Create a pod template. You can use the existing templates from the other nodes as a example. The container you need to deploy is present on docker hub with the name: robopaas/mapserver:latest

* Deploy the web frontend as the last part, you will need to add an environment variable to connect to the public endpoint of the ROS-Bridge. 

* Create all the other pods and services with the appropriate kubectl command. Either look it up online or use the integrated help of kubectl. 

* You can use “kubectl get all” to verify that every required pod and service is up and running. 

* Look up the loadBalancer ingress url of the ROS-Bridge external service and add it as a environment variable to the web frontend pod template. Use “ROSBRIDGE_URL” as the environment variable name.
Hint: use “kubectl describe service <service name>” to display the informations of a service. 

* To access the web gui, look up the loadbalancer ingress of the service connected to the webfrontend pod. Copy the url to a browser and visit the page on port 8080. If you deployed everything correctly, you should see a map. 


## Part 2:

### Create Publisher Node

In the second part you will create a simple ROS publisher node which publishes all running nodes to a topic. There is already a listener in the web frontend to display the running nodes. So you can always check the web frontend to see if the publisher node is working correctly. 

In folder Part2 you can find the basic structure of a ROS publisher node with all the required project files. Add the required code to the nodes.py file in the src folder to read the running nodes and publish those to the given topic.
In the kubernetes folder you will find a pod and a service template for deploying your node. 

### Edit nodes.py to create the ROS node

You can find an example of a ROS Publisher at the following URL: http://wiki.ros.org/rospy_tutorials/Tutorials/WritingPublisherSubscriber

First you will need to create a publisher object. Use the message Type String and the queue_size=1

Also initialize the actual ROS node with the init_node function.

Read every two seconds the running nodes. Use the rosnode library for that task. 

Because we want to send only one String with all of the running nodes we have to concatenate the String from the array we get back from the rosnode library. Add a space as separator between each node name. This list will be parsed on the webfrontend. It should look like this:

nodeString += node + " "


### Deploy the created node to kubernetes:

* Push your code including the project files to your github account. You should push only the “rosnodeinfo” folder making sure that it is preserved when checking out. (the rosnodeinfo folder should be preserved in your repository)

* Before creating the pod and the service in kubernetes, you have to add your git repository url as an environment variable to the pod template. Use “GIT_REPO” as the name of the environment variable. The docker image will then automatically clone your code into the container.

* Create now the pod and the service using the appropriate kubectl command. 

* If everything works fine, you should see now the running nodes on the web frontend.
