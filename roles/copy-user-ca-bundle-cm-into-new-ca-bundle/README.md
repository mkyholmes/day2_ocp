Role Name
=========

A helper role used to export an existing CA bundle configmap into a new CA bundle configmap.
Why is this needed? There are case where a configmap exists (e.g. user-ca-bundle in the openshift-config namespace) where the key holding the actual certificate might not match the key expected by certain applications. For example the key in the user-ca-bundle configmap is ca-bundle but there are applications that expect their ca-bundle key to be named ca or something else. When that happens this helper can be used to export the existing configmap into a new one that has the expected key. 
Other cases where this might be useful is where one need the exisiting certificates in order to append new certificates to it. In such cases this can still be used to export the configmap and then additional processing might be needed to extract the actual CA bundle and append the new certificates to it. That last use case is not being handled here. 

Requirements
------------


Role Variables
--------------

- openshift_cli: Openshift client binary used to interact with the cluster api (default to 'oc')
- existing_ca_bundle_cm_name: Name of the CA bundle CM to be exported
- new_ca_bundle_cm_name: Name of the new CA bundle CM to be created
- existing_ca_bundle_cm_namespace: The namespace where the CA bundle being exported is currently located
- new_ca_bundle_cm_file: Name and path of the exported CM yaml manifest 
- new_ca_bundle_cm_namespace: The name of the namespace where the new config map is to be provisioned into
- update_new_ca_bundle_cm_namespace: Wheter to update the namespace in the created configmap
- create_new_ca_bundle_cm: Whether to create the newly create CA bundle configmap
- new_ca_bundle_cert_key: Name of the key in the new CA bundle configmap.
- existing_ca_bundle_cert_key: Name of the key in the existing CA bundle configmap.

Variables for the additional CA bundle for registries
- registries_trust_stores: The structure containing information about the various private registries whose CA should be trusted within the additional trusted CA bundle used for the private registry.
   - fqdn: The FQDN or IP of the registry.
   - port: The port on which the registry listen (default assumes 443)
   - ca_file_pem: The path to the pem based certificate file to use.
      
Dependencies
------------


Example Playbook
----------------

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: localhost
      vars:
        ansible_python_interpreter: /usr/bin/python3
      vars_files:
        - 'vars/vault.yml'
        - 'vars/global.yml'
      vars:
        module: "Export existing user-ca-bundle"
        ansible_name_module: "Export CA bundle CM | {{ module }}" 
      pre_tasks:
        - name: Install required pip library
          pip:
            name: openshift
            state: present

        - name: Ensure Proper Python dependency is installed for Openshift
          python_requirements_facts:
            dependencies:
              - openshift
              - requests
        - name: Authenticate with the API
          command: >
            {{ openshift_cli }} login \
              --token {{ ocp_cluster_token }} \
              --insecure-skip-tls-verify=true {{ ocp_cluster_console_url }}:{{ ocp_cluster_console_port | d('6443', true) }}
          when:
            - ocp_cluster_token is defined and ocp_cluster_token != ""
          register: login_out

        - name: Authenticate with the API
          command: >
            {{ openshift_cli }} login \
              -u {{ ocp_cluster_user }} \
              -p {{ ocp_cluster_user_password }} \
              --insecure-skip-tls-verify=true {{ ocp_cluster_console_url }}:{{ ocp_cluster_console_port | d('6443', true) }}
          when:
            - not ocp_cluster_token is defined or ocp_cluster_token == ""
          register: login_out

      roles:
         - { role: copy-user-ca-bundle-cm-into-new-ca-bundle }
