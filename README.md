# NATS Cluster in K8s
This is the easiest possible way to create a true NATS cluster in a K8s environment. 

In order to use this setup, clone the repo then run 

`kubectl apply -f seeds.yml`

`kubectl apply -f nodes.yml`

You can adjust the cluster size by changing the `replicas` values as you wish. But plesae note that, in case JetStream is enabled (which is by default), this will lead to an increase in disk space usage and stress. So it is recommended that you select the right number of replicas based on the number of nodes you have in your cluster. 