# murphywants-ansible-role-component-dehydrated

## Description & Purpose

[Dehydrated](https://github.com/dehydrated-io/dehydrated) is an acme client using DNS challenges to request lets encrypt certificates. This ansible role will deploy dehydrated, populate the domains.txt file and request a certificate. Dehydrated works by using specially made hook scripts to integrate with apis for different DNS services. Currently, there is only a single hook script available for integration with Cloudflare, [as sourced from this github repo.](https://github.com/socram8888/dehydrated-hook-cloudflare/tree/master)

This role is intended to be a component of another role, such as deploying an application server. However, it can also be run standalone. 

Dehydrated works with the following file structure:

```
accounts | Dehydrated stores account information here for each CA
archive | Dehydrated archives old certs here
certs | Dehydrated stores certificates here
chains
conf.d 
config | All config settings are stored here, options found here: https://github.com/dehydrated-io/dehydrated/blob/master/docs/examples/config
domains.txt | List of domains to request certs from
hook.d | Extra API providor hook scripts
hook.sh | The main hook script that calls the scripts in hook.d
```

## How to Use

### As a standalone role/playbook 

Example Inventory:

yaml form:
```
group:
  hosts:
    hostname.fdqn
  vars:
    DEHYDRATED_DOMAINS_LIST: 
      - first.fqdn
      - second.fqdn
    DEHYDRATED_HOOK_CLOUDFLATE_API_KEY: InsertYourApiKeyHere
```

Example Playbook:

```
- hosts: all 
  gather_facts: yes
  become: yes
  roles:
  - role: murphywants-ansible-role-component-dehydrated
```

### As a play in another role/playbook 
```
  - name: Setup dehydrated
    include_role: 
      name: murphywants-ansible-role-component-dehydrated
    vars:
      DEHYDRATED_DOMAINS_LIST:
        - fist.fqdn
      DEHYDRATED_HOOK_CLOUDFLATE_API_KEY: InsertYourApiKeyHere 
```

## Requirements/Dependencies
This role uses OOTB ansible modules. 


## Tags
Ansible role template with the following actions & tags:

Tag | Description
--- | ---
Setup/Baseline | Setup the application and apply the configuration baseline


## Variables
Variable | Default Value | Description
---|---|---
DEHYDRATED_CONFIG_BASEDIR |    "/etc/dehydrated/" | Where the config files are located
DEHYDRATED_CONFIG_CONFIG_D |  "{{ DEHYDRATED_CONFIG_BASEDIR }}/conf.d" | An extended config changes
DEHYDRATED_CONFIG_WELLKNOWN |  "{{ DEHYDRATED_CONFIG_BASEDIR }}/acme-challenges" | Location of the temp challenges directory
DEHYDRATED_CONFIG_DOMAINS_TXT |  "{{ DEHYDRATED_CONFIG_BASEDIR }}/domains.txt" | Location of the domains.txt file that contains a list of domains to generate certifactes for
DEHYDRATED_CONFIG_CA |  "letsencrypt" | This can be changed to target different CAs. [As described here](https://github.com/dehydrated-io/dehydrated/blob/master/docs/examples/config), there are a few other options: "letsencrypt, letsencrypt-test, zerossl, buypass, buypass-test"
DEHYDRATED_CONFIG_CHALLENGETYPE |  "dns-01" | The ACME challenge type. This is only configured for DNS challenge and shouldn't be changed. 
DEHYDRATED_CONFIG_HOOK |  "{{ DEHYDRATED_CONFIG_BASEDIR }}/hook.sh" | Location of the hook script
DEHYDRATED_CONFIG_LOCKFILE |  "{{ DEHYDRATED_CONFIG_BASEDIR }}/lock" | Location of the lock file 
DEHYDRATED_CONFIG_AUTO_CLEANUP |  "yes" | Yes or No, if dehydrated should automatically cleanup after itself
DEHYDRATED_DOMAINS_LIST_CLEAN |  false | If its set to true, domains.txt is deleted and recreated, cleaning up the file
DEHYDRATED_DOMAINS_LIST | An empty list | Dehydrated will attempt to request certs for each domain in this list. Each domain is added to domains.txt
DEHYDRATED_HOOK_CLOUDFLATE_API_KEY |  undefined | Enter your cloudflare api key here
DEHYDRATED_HOOK_CLOUDFLATE_DEPLOY_BOOL |  true | True/False if the cloudflare hook script is deployed

# TODO List
