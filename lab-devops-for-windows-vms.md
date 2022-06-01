---
# Page settings
layout: default
keywords:
comments: false

# Hero section
title: Lab - DevOps for Windows VMs
description: Let's create some Terraform to provision a virtual machine and then use Ansible to configure a Windows DNS Server!

# Author box
author:
    title: About Author
    title_url: '#'
    external_url: true
    description: Facundo is a Solutions Architect at BoxBoat. He's been a lot spending time with IT Pros teaching them DevOps practices like Infrastructure as Code (IaC) and version control.

# Micro navigation
micro_nav: true

# Page navigation
page_nav:
    prev:
        content: Home
        url: '/'
---

## Fork the workshop repo, launch Codespaces

Link: [Workshop repo](https://github.com/BoxBoat-Codespaces/codespaces-workshop)

The destination should be a Codespaces enabled Org. You should prepend the repo name with your username, so if your username is fgauna, your repo should be `fgauna-codespaces-workshop`.

Now that you have a repo to work from, let's launch Codespaces. Click the green "Code" button, "Create codespaces on main". You can now choose to open that in the browser, or in your Visual Studio Code desktop app. 

## More prep work, sorry!

Open a Terminal from the GitHub Codespace. It should be under "Terminal" -> "New Terminal". Use Bash, it should be the default.

![](/doks-theme/assets/images/codespaces-terraform-ansible.png)

Next, login to Azure. Ensure you have the right subscription selected.

``` bash
az login
```

Next, create a resource group and a storage account. We will use the storage account for the Terraform backend.
Substitue `[your-name]` with your first name to avoid name collisions with your peers.

``` bash
RESOURCE_GROUP_STATE="rg-workshop-[your-name]-state-001"
STORAGE_ACCOUNT_STATE="stwkshp[your-name]"
az group create -n $RESOURCE_GROUP_STATE -l eastus
az storage account create -n $STORAGE_ACCOUNT_STATE -g $RESOURCE_GROUP_STATE -l eastus
az storage container create -n workshop --account-name $STORAGE_ACCOUNT_STATE

export ARM_ACCESS_KEY=$(az storage account keys list -g $RESOURCE_GROUP_STATE -n $STORAGE_ACCOUNT_STATE --query "[0].value" -o tsv)
```

Now, from Visual Studio Code, open the `backend.tf` file. 

Set `storage_account_name` to the name of the storage account you created above.

``` yaml
storage_account_name = "stwkshp[your-name]"
```

Now you are ready to plan and apply!

## Creating the virtual machine

Before you run the Terraform configuration, you'll need to pick a password for your virtual machine.

``` bash
export TF_VAR_admin_password="<your password here>"
```

Next, you need to "initialize" Terraform. This will also validate that you can use the Terraform backend appropiately. 

``` bash
cd terraform
terraform init
```

Next, we do the Terraform "plan". A "plan" shows you what Terraform will do. 

``` bash
terraform plan
```

Will give you an output like this.



``` bash
terraform apply -auto-approve
```

You will see the Public IP for your virtual machine as an output. 
Don't worry, there's an Network Security Group (NSG) rule that only allows traffic from your public IP address.

## Configuring the virtual machine

Configure your `inventory` file to use the Public IP from your virtual machine. 

``` ini
[domain_controllers]
vm-workshop ansible_host=<your ip goes here>
```

Next, execute the Ansible. But, first you'll have to set your current directory to the `ansible` directory.

``` bash
cd ..
cd ansible
ansible-playbook -i inventory --extra-vars="ansible_user=adminuser" --extra-vars="ansible_password=$TF_VAR_admin_password" playbook.yaml 
```

![](/doks-theme/assets/images/codespaces-ansible-play.png)