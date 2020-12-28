Ansible  VMWare ESXi 6.7

Prerequisites

- VMWare ESXi 6.7
-  ansible 2.9.11

Ansible provides various modules to manage VMware infrastructure, which includes datacenter, cluster, host system and virtual machine

$ pip install pyvmomi

Ansible VMware modules leveraging latest vSphere(6.0+) features are using vSphere Automation Python SDK. The vSphere Automation Python SDK also has client libraries, documentation, and sample code for VMware Cloud on AWS Console APIs, NSX VMware Cloud on AWS integration APIs, VMware Cloud on AWS site recovery APIs, NSX-T APIs.

#You can install vSphere Automation Python SDK using pip:

$ pip install --upgrade git+https://github.com/vmware/vsphere-automation-sdk-python.git

Note:
    Installing vSphere Automation Python SDK also installs pyvmomi. A separate installation of pyvmomi is not required.



##Control Node

Delegation

Delegation allows you to select the system that executes a given task. If you do not have pyVmomi installed on your control node, use the delegate_to keyword on VMware-specific tasks to execute them on any host where you have pyVmomi installed.

##Modules

The units of code Ansible executes. Each module has a particular use, from creating virtual machines on vCenter to managing distributed virtual switches in the vCenter environment. You can invoke a single module with a task, or invoke several different modules in a playbook. For an idea of how many modules Ansible includes, take a look at the list of cloud modules, which includes VMware modules.

##Playbooks

Ordered lists of tasks, saved so you can run those tasks in that order repeatedly. Playbooks can include variables as well as tasks. Playbooks are written in YAML and are easy to read, write, share and understand.

##pyVmomi

Ansible VMware modules are written on top of pyVmomi. pyVmomi is the official Python SDK for the VMware vSphere API that allows user to manage ESX, ESXi, and vCenter infrastructure.

You need to install this Python SDK on host from where you want to invoke VMware automation. For example, if you are using control node then pyVmomi must be installed on control node.

If you are using any delegate_to host which is different from your control node then you need to install pyVmomi on that delegate_to node.


$ pip install  Pyvmomi


Installing SSL Certificates

All vCenter and ESXi servers require SSL encryption on all connections to enforce secure communication. You must enable SSL encryption for Ansible by installing the server’s SSL certificates on your Ansible control node or delegate node.

If the SSL certificate of your vCenter or ESXi server is not correctly installed on your Ansible control node, you will see the following warning when using Ansible VMware modules:

To install the SSL certificate for your VMware server, and run your Ansible VMware modules in encrypted mode, please follow the instructions for the server you are running with VMware.

Installing vCenter SSL certificates for Ansible

   From any web browser, go to the base URL of the vCenter Server without port number like https://vcenter-domain.example.com
    Click the “Download trusted root CA certificates” link at the bottom of the grey box on the right and download the file.
    Change the extension of the file to .zip. The file is a ZIP file of all root certificates and all CRLs.
    Extract the contents of the zip file. The extracted directory contains a .certs directory that contains two types of files. Files with a number as the extension (.0, .1, and so on) are root certificates.
    Install the certificate files are trusted certificates by the process that is appropriate for your operating system.

##Installing ESXi SSL certificates for Ansible

- Enable SSH Service on ESXi either by using Ansible VMware module vmware_host_service_manager or manually using vSphere Web interface.
-   SSH to ESXi server using administrative credentials, and navigate to directory /etc/vmware/ssl
-   Secure copy (SCP) rui.crt located in /etc/vmware/ssl directory to Ansible control node.
-   Install the certificate file by the process that is appropriate for your operating system.

$ sudo cp rui.crt /usr/local/share/ca-certificates

or

$ sudo cp rui.crt /usr/share/ca-certificates


$  sudo dpkg-reconfigure ca-certificates

$ sudo update-ca-certificates


##VMware Dynamic Inventory Plugin

The best way to interact with your hosts is to use the VMware dynamic inventory plugin, which dynamically queries VMware APIs and tells Ansible what nodes can be managed.

Requirements

To use the VMware dynamic inventory plugins, you must install pyVmomi on your control node (the host running Ansible).

To include tag-related information for the virtual machines in your dynamic inventory, you also need the vSphere Automation SDK, which supports REST API features like tagging and content libraries, on your control node.

cat ~/.ssh/id_rsa.pub | ssh root@10.1.1.11 'cat >> /etc/ssh/keys-root/authorized_keys' 

https://graspingtech.com/ansible-esxi/

Since Ansible utilizes the VMware API to perform actions, in this use case we will be connecting directly to the API from our localhost. This means that our playbooks will not be running from the vCenter or ESXi Server. We do not necessarily need to collect facts about our localhost, so the gather_facts parameter will be disabled. You can run these modules against another server that would then connect to the API if your localhost does not have access to vCenter. If so, the required Python modules will need to be installed on that target server.

To begin, there are a few bits of information we will need. First and foremost is the hostname of the ESXi server or vCenter server. After this, you will need the username and password for this server. For now, you will be entering these directly, but in a more advanced playbook this can be abstracted out and stored in a more secure fashion using ansible-vault or using Ansible Tower credentials. If your vCenter or ESXi server is not setup with proper CA certificates that can be verified from the Ansible server, then it is necessary to disable validation of these certificates by using the validate_certs parameter. To do this you need to set validate_certs=False in your playbook.


The removal VMware virtual machine using vmware_guest module is destructive operation and can not be reverted, so it is strongly recommended to take the backup of virtual machine and related files (vmx and vmdk files) before proceeding.


##Using VMware HTTP API using Ansible

This guide will show you how to utilize Ansible to use VMware HTTP APIs to automate various tasks.

Since Ansible utilizes the VMware HTTP API using the uri module to perform actions, in this use case it will be connecting directly to the VMware HTTP API from localhost.

