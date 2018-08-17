## Verify Installation and Configuration of Docker ##
Verify that Docker is running on all of the nodes in the cluster.
* ansible nodes -m shell -a"systemctl status docker | grep Active"

Make sure that the Docker version is correct for the desired OpenShift version
* ansible nodes -m shell -a"docker version|grep Version"  
  
Examine the NFS server to see which NFS volumes are shared:
* ansible nfs -m shell -a"exportfs"

## Deploy HA Cluster ##

### Download LDAP cert on bastion ###
```
cd /root
wget http://ipa.shared.example.opentlc.com/ipa/config/ca.crt -O /root/ipa-ca.crt
```

### Basic Required Ansible Inventory Parameters ###
```
ansible-playbook -f 20 openshift-ansible/playbooks/prerequisites.yml

ansible-playbook -f 20 openshift-ansible/playbooks/deploy_cluster.yml
```

## Uninstalling OpenShift ##

Run the uninstall playbook:
```
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/adhoc/uninstall.yml
```

After the uninstallation completes it is also usually necessary to delete all left over content (certificates etc.) in the /etc/origin directories on all masters and nodes.
```
ansible nodes -a "rm -rf /etc/origin"
```

It is also necessary to delete all data from the NFS Server.
```
ansible nfs -a "rm -rf /srv/nfs/*"
```
