# NATS Cluster in K8s
This is the easiest possible way to create a true NATS cluster in a K8s environment. 

In order to use this setup, clone the repo then run 

`kubectl apply -f seeds.yml`

`kubectl apply -f nodes.yml`

You can adjust the cluster size by changing the `replicas` values as you wish. But plesae note that, in case JetStream is enabled (which is by default in this configuration), this will lead to an increase in disk space usage and stress. So it is recommended that you select the right number of replicas based on the number of nodes you have in your cluster. 

## Seeds 
Seeds are used for node discovery only. 

1. They are stateless because they only solicit routes (no JetStream, no state)
2. They are replicated for HA (if one is unavilable, another seed can allow a node to join the cluster)

## Nodes
Nodes are actual NATS server which serve the clients.


# K3s 
You may have to disable `firewald` in order for the headless service to work. However, if you are not interested in disabling your `firewalld` (which is the right choice) and `firewalld` is blocking requests, please follow these steps: 

1. `firewall-cmd --set-log-denied all`
2. `vi /etc/rsyslog.d/custom_iptables.conf`
3. Append the following to the file and save:

        :msg,contains,"_DROP" /var/log/iptables.log
        & stop
        :msg,contains,"_REJECT" /var/log/iptables.log
        & stop
4. `systemctl restart rsyslog`
5. Run a test pod like `kubectl run -i -tty --rm test --image=rockylinux:9 /bin/bash`
6. Install bind-utils `dnf install -y bind-utils`
7. Run `nslookup` on the headless service like `nslookup <my_service>`
8. Run a curl on one of the pods IPs
9. Monitor the `/var/log/iptables.log` file on the host and find the `cni` that is being rejected
10. Add the firewall rule like `firewall-cmd --zone=<zone-name> --add-interface=<cni> --permanent`

        Example:
        firewall-cmd --zone=trusted --add-interface=cni0 --permanent
        firewall-cmd --reload
