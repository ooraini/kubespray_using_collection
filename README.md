# kubespray_using_collection

`vagrant up`

`ansible-galaxy install -r requirments.yml`

`ansible-playbook -i mycluster/hosts.yaml  --become --become-user=root cluster.yml -u vagrant -e "maximal_ansible_version=2.12.0"`
