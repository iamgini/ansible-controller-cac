# Configuration as Code for Automation Controllers

This Ansible playbooks and roles allows for easy interaction with an Ansible Controller server via Ansible roles using the Controller collection modules.

## What is Configuration as Code (CaC) in Ansible Automation Platform?

CaC is a term generally referring to the separation of configuration settings from the actual code. The ideal being you can store that configuration data in source control, and easily run and tweak it to match different environments.

In Ansible Automation Platform terms, we can use the features within the automation controller in combination with CaC to provide a more flexible, richer experience.

You can use CaC with a GitOps approach to help replicate configurations across automation controller environments. When dealing with large, complex systems, you often need to replicate configurations between environments or sites. Treating your configs as a form of code, called Configuration as Code (CaC or CasC), allows you to track bugs, fixes, and deployments.

## Requirements

1. `awxkit >= 9.3.0`
2. [ansible.controller](https://console.redhat.com/ansible/automation-hub/repo/published/ansible/controller/) collection - 4.4.2 or later if any.

`ansible-galaxy collection install ansible.controller`

3. Credential to the Ansible Automation Controller

## How to use this playbook

The `controller_configure.yaml` can be executed using `ansible-playbook`, `ansible-navigator` or using a Job template from **Ansible controller** itself.

When using `ansible-playbook` or `ansible-navigator`, the credential can be passed as environment variables; configure the credential as follows.

```shell
export CONTROLLER_USERNAME=admin
export CONTROLLER_PASSWORD=secretpassword
export CONTROLLER_HOST=https://ansiblecontroller22-1.lab.local
```

### Method 1: Using `ansible-playbook`

```shell
$ ansible-plabook controller_configure.yaml -t <tag>
```

### Method 2: Using `ansible-navigator`

```shell
$ ansible-navigator run controller_configure.yaml --penv CONTROLLER_USERNAME --penv CONTROLLER_PASSWORD --penv CONTROLLER_HOST -t <tag>
```

### Method 3: Using automation controller job template

Login to the automation controller (from where you are going to execute the CaC operation),

- Step 1. A Git repo for Configuration as Code - Store this content in a Git repo as it is.

- Step 2: Create the Project in Automation controller with the CaC repo as source and add Git credential if required.

- Step 3: Create a `Red Hat Ansible Automation Platform` type credential and enter the controller host, username and password for the controller.

- Step 4: Create a Job template (eg: `CaC-Controller-Configuration`) using the playbook `controller_configure.yaml`
  - Step 4.1: Attach the previously created `Red Hat Ansible Automation Platform` type credential. 
  - Step 4.2: Enable `Prompt on Launch` for the `Job Tags` as we need to control the execution.
  - Step 4.3: Add a tag `none` in the `Job Tags` text box. (This is to avoid any accidental execution.)

- Step 5: Execute the playbook with appropriate tags as explained in the next section.

### Controlling the controller configurations

- If the [credential sources](https://console.redhat.com/ansible/automation-hub/repo/published/ansible/controller/content/module/credential_input_source/?sort=-pulp_created) are enabled (automated password and token input), all of the resources can be created in a one batch.
- If the credential input (password, token and so on) needs to enter manually, then break the job execution as follows.

Step 1: Launch the job template `CaC-Controller-Configuration` with the following tags: `settings`, `organizations`, `credential_types`, `credentials`

Step 2: Login to the target automation controller (if this is a different controller), edit the source control credentials with correct password (this is for syncing the project with correct credentials). You can also update other credential passwords and tokens at this time.

Step 3. Continue the CaC configuration with the remaining tags: `projects`, `inventories`, `hosts`, `templates`, `workflows` etc.

## References 

- [Red Hat Communities of Practice Controller Configuration Collection](https://github.com/redhat-cop/controller_configuration/tree/devel)
- [Automation controller workflow deployment as code](https://www.ansible.com/blog/automation-controller-workflow-deployment-as-code)
- [Ansible Automation Platform 2.3 Configuration as Code Improvements](https://www.ansible.com/blog/ansible-automation-platform-2.3-configuration-as-code-improvements) - blog