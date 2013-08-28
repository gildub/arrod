Intro
-----
Ansible plays to deploy OpenStack Havana with Neutron on RHEL 6.4+ or Fedora 19+  
For OpenStack Grizzly deployments, please use grizlly branch

Status
------
* Havana
  * RHEL6.4+
    * RDO(Havana 2): stable
    * RHOS40: developement - Check README-RHOS40
  * Fedora19+: todo

Notes
-----
This work was initialy based from [ansible-openstack](https://github.com/ansible/ansible-redhat-openstack).  
Because of different deployment needs, for multi nodes approach, it has been almost entirely revisited.  
Ultimately they will be merged back together.  
Storage and HA are not supported yet but working on it.

Assumptions
-----------
  1. Using 2 physical networks
  2. A consistent network interface naming and configuration accross the environment.  
     For instance:
     * eth1 is always external network 
     * eth2 is always internal/data network 

How To
------
  1.  Requirements
      * Ansible 1.2+ installed (epel6 for RHEL) on a system having ssh access to all your OpenStack target host(s)
      * Targetted hosts must have
        * RHEL6.4+ or Fedora 19+ installed
        * Either OpenStack repos
          * RHEL: [RDO repo](repos.fedorapeople.org/repos/openstack/) or RHOS 4.0
          * Fedora repo
        * Controller node(s) must have partition allocated for Cinder
      * If using RHOS, also consult README-RHOS40
  2. Copy Ansible definitions to some directory:  
     `git clone https://github.com/gildub/arrod openstack`
  3. Create a `your-hosts` inventory file, use hosts-examples directory for templates:
     * All in one:  
       1 x Controller/Network/Compute node 
     * Controller and network service:  
       N x controller(s), N x compute node(s)
     * All separate:  
       N x controller(s), N x network node(s), N x compute node(s)
  4. Edit group_vars values:
     * Passwords and secrets keys
     * Change `cinder_volume_dev` key accordingly with partition path and name
     * Neutron
       * Set `neutron_external_interface` and `neutron_internal_interface` keys, see Assumptions above
       * VLANs
         * Set `provider_network_type: vlan`
         * Set `splinters: True` if [needed](https://access.redhat.com/site/articles/289823)
       * GRE tunnels
         * Set `provider_network_type: gre`
  5. Deploy
     1. From the ansible installed system, make sure you have ssh access to all hosts  
        For instance have an ssh key installed on all hosts and use ssh-agent
     2. Run Ansible, using inventory file created above:
        `cd openstack`  
        `ansible-playbooks -i ../your-hosts site.yml`
     3. If it fails, address issue according to the message and re-run Ansible: wash, rince and repeat!

