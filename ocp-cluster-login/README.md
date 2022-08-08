Role Name
=========

A utility role to help login to an OpenShift Container Platform cluster or a Kubernetes cluster using one of the standard three methods: kubeconfig file, token or username and passowrd.

Requirements
------------


Role Variables
--------------

- ocp_cluster_kubeconfig: The path to the kubeconfig file on the controller node
- ocp_cluster_user: the username to use along with the password below to authenticate to the cluster
- ocp_cluster_user_password: The password for the user above to be use for basic authentication
- ocp_cluster_token: The token to use for login if that the way to authenticate to the cluster.
- openshift_cli: the path to the OpenShift or kubectl cient to use 
- ocp_cluster_console_url: The URL of the API to the OpenShift or kubernetes cluster
- ocp_cluster_console_port: The API port used by the cluster (default to 6443 if not provided)


Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------


    - hosts: localhost 
      vars:
        ocp_cluster_kubeconfig: ''
        ocp_cluster_user: ''
        ocp_cluster_user_password: ''
        ocp_cluster_token: ''
        ocp_cluster_console_url: ''
        ocp_cluster_console_port: ''
        openshift_cli: ''
      roles:
         - { role: ocp-cluster-login }

License
-------

BSD

Author Information
------------------

An optional section for the role authors to include contact information, or a website (HTML is not allowed).
