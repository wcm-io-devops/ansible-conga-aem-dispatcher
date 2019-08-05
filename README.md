# wcm_io_devops.conga_aem_dispatcher

This role deploys the configuration artifacts generated by the CONGA
role
[aem-dispatcher](https://github.com/wcm-io-devops/conga-aem-definitions/blob/develop/conga-aem-definitions/src/main/roles/aem-dispatcher.yaml)
with Ansible. It also configured the webserver based on the CONGA
configuration model.

> This role was developed as part of the
> [wcm.io DevOps Ansible Automation for AEM](http://devops.wcm.io/ansible-aem/)
> to integrate Ansible with
> [CONGA](http://devops.wcm.io/conga/).

## Requirements

This role requires Ansible 2.0 or higher.

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):

    # conga_aem_dispatcher_apache_config_path: /etc/apache2

# Overrides the distribution specific apache_config_path which is derived from the vars.

    # conga_aem_dispatcher_apache_user: www-data

# Override the distribution specific apache user which is derived from the vars.

# conga_aem_dispatcher_apache_group: www-data

Override the distribution specific apache group which is derived from the vars.

    conga_aem_dispatcher_restricted_mode: false

Controls if role is running in a non-root restricted mode. This will disable a2enmod, a2enconf and a2ensite commands.

    #conga_aem_dispatcher_apache_config_owner: root
    #conga_aem_dispatcher_apache_config_group: root

The owner and group of apache config files.

    #conga_aem_dispatcher_apache_sites_available: "{{ apache_config_path }}/sites-available }}"
    #conga_aem_dispatcher_apache_sites_enabled: "{{ apache_config_path }}/sites-enabled }}"

Path to sites-available and sites-enabled folders. Only used on Debian based OS.

    #conga_aem_dispatcher_apache_conf_available: "{{ apache_config_path }}/conf-available }}"
    #conga_aem_dispatcher_apache_conf_enabled: "{{ apache_config_path }}/conf-enabled }}"

Path to conf-available and conf-enabled folders. Only used on Debian based OS.

## Dependencies

* [wcm_io_devops.conga_facts](https://github.com/wcm-io-devops/ansible-conga-facts)
  role for accessing the CONGA configuration model.

## Example

Compiles the CONGA configuration, installs the webserver with the
Ansible [wcm_io_devops.apache](https://github.com/wcm-io-devops/ansible-role-apache) role and deploys the configuration from
the CONGA `aem-dispatcher` role in the `aem-author` variant.

	- hosts: localhost
	  roles:
	    - wcm_io_devops.conga_maven
	
	- hosts: webserver
	  pre_tasks:
	    # Setup CONGA facts for the role aem-dispatcher so that
        # we have access to the httpd port configuration
        - conga_facts:
            conga_role_mapping: aem-dispatcher
    
        - set_fact:
            # Save http and https port config aem-dispacher role for use by the aem-dispatcher role
            aem_dispatcher_port: "{{ conga_config.httpd.serverPort | default(80) }}"
            aem_dispatcher_port_ssl: "{{ conga_config.httpd.serverPortSsl | default(443) }}"
	  roles:
	    - wcm_io_devops.aem_dispatcher
        - { role: wcm_io_devops.conga_aem_dispatcher,
            conga_variant_mapping: aem-author
          }

## License

Apache 2.0
