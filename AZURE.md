# Terraform Foundations on Azure

## 👋 Introduction
Welcome to Terraform Foundations on Azure. This is a hands-on workshop that will teach you how to build Azure infrastructure using code. Along the way you'll learn important Terraform skills and best practices for infrastructure as code, version control, and automation. This workshop can be done as a self-study curriculum on your own, or as an instructor-led training. The lab environment will remain up and running for six hours. If you don't complete the entire lab today you can come back and spin up a new sandbox environment for up to three months.
![Terraform Logo](images/tflogo_128.png)

Follow the instructions on this page to complete all your lab assignments. You may use the navigation menu on the left to jump to a particular lab exercise. The lab exercises build upon one another so it's best to do them in order.

Good luck and enjoy the workshop!

Note: If you need to jump ahead to a particular point in the workshop you can copy the code in the Answers appendix to get caught up.

## 👩🏾‍🔬 Your Lab Environment
Your lab environment is running on the <a href="https://instruqt.com" target="_blank">Instruqt</a> platform. Instruqt provides sandbox cloud environments where you can experiment and learn how to use HashiCorp tools like Terraform. You have four tabs in your learning environment:

![Instruqt Navigation Tabs](images/four_tabs.png)

* **Instructions** - All your lab instructions are contained on this page, which opens in a new tab or window. Keep these instructions handy for easy reference.
* **Text Editor** - This tab contains a simple programmer's text editor. Use the file navigation menu on the left side of the editor to browse to files that you wish to edit.
* **Shell** - This is the bash shell for your Ubuntu 18.04 workstation. We've pre-installed a bunch of popular devops tools such as terraform, git, docker, and the `az` Azure CLI tool.
* **Azure Portal** - This tab contains a link and credentials for your Azure sandbox subscription.

Warning: Use an incognito window when you log onto the Azure portal. You can do this by right-clicking the link on the Azure Portal link and selecting "Open in Incognito Mode" or "Open in Private Mode". Using an incognito/private window for all your lab exercises will ensure that you don't accidentally log on with your company or personal account.

![incognito_mode](images/incognito_mode.png)

---
## ⚙️ Lab 1: Terraform Basics
**Topics Covered:**
Intro to Terraform, Installation, Creating a Workspace

**Documentation:**
https://www.terraform.io/intro/index.html
https://learn.hashicorp.com/tutorials/terraform/install-cli
https://www.terraform.io/docs/language/state/workspaces.html

**Summary**: In this lab you'll learn how terraform is installed and you'll create your first workspace.

---
### What is Terraform?
Terraform is an Infrastructure-as-Code (IaC) software tool created by HashiCorp and released in 2014. Terraform allows users to define and provision infrastructure using a declarative configuration language known as HashiCorp Configuration Language (HCL). Terraform manages infrastructure such as public and private cloud instances or VMs, network appliances, Software-as-a-Service (SaaS) and Platform-as-a-Service (PaaS) resources.

### Modular and Extendible
Terraform uses a catalog of providers to communicate with different cloud platforms and APIs. The rich provider ecosystem supports a wide variety of cloud and on-premise resources.

### Declarative Infrastructure
The Terraform language, or HCL, is a Domain Specific Language (DSL) that uses a declarative syntax. Declarative configuration allows you to define the desired final state of your resources without having to define the logic necessary to get there. Terraform determines the order that resources need to be provisioned in based on dependencies. For instance, a network needs to be built before a server can be assigned to it. 

### Executable Documentation
Terraform code is easy for both humans and machines to process. Take a look at the example below to see the simple declarative syntax:

```php
resource "azurerm_resource_group" "tflab_rg" {
  name     = "tflab-resource-group"
  location = "Central US"
}
```

### Terraform Usage
Let's try a few basic commands. Open the Shell tab in your lab environment and run the following commands:

The Unix `which` command will show you where Terraform is installed. Powershell users can use `Get-Command` here instead. 
```bash
which terraform
```

The `file` command tells us what type of file Terraform is. Note that it is an *executable* which means we can run it from the command line.
```bash
file /usr/local/bin/terraform
```

Try running `terraform version` next. This will output the version of Terraform you have installed.
```bash
terraform version
```

This useful command shows all the subcommands supported by the `terraform` command. We'll explore some of these subcommands in the next section.
```bash
terraform -help
```

Terraform files should always be stored in a workspace directory. We've created a directory for you called **sandbox**. All your terraform commands should be run from within the sandbox directory. Use the cd command to move into the sandbox directory if you're not there already:

```bash
cd /root/sandbox
```

Terraform code files always end in a `.tf` extension. By convention, the main configuration file is called `main.tf`.

Create a new file in your sandbox directory called main.tf:
```bash
touch main.tf
```

Your provider configuration and Terraform code will be added to this file in the next labs.

---
## ⚙️ Lab 2: The Azure Provider
**Topics Covered:**
Terraform Providers, AzureRM Provider, Terraform Init

**Documentation:**
https://www.terraform.io/docs/language/providers/index.html
https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs
https://www.terraform.io/docs/cli/commands/init.html

**Summary**: In this lab you'll work with the AzureRM terraform provider and create your first workspace.

---
In order for Terraform to do anything useful, it needs a [provider](https://www.terraform.io/docs/language/providers/index.html). Terraform Providers are the plugins that connect the Terraform command line tool to various APIs and SaaS platforms. Take a moment to browse the public provider registry now:

https://registry.terraform.io/browse/providers

Today you'll be using the Azure provider to build resources in your Azure Cloud sandbox.  Visit the Terraform docs example for the Azure provider here:
https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs#example-usage

Copy the `terraform` and `provider` blocks into your **main.tf** file. You do not need the rest of the example code yet. Use the save icon to save your file.

Warning: Please use version **2.46.0** for the provider. This is the version that has been tested with this workshop. You can check the [answer key](https://github.com/hashicorp/terraform-azure-labs/blob/main/lab_answers/02/main.tf#L5) to make sure you've got it set up correctly.

Now you're ready to initialize your workspace. Run the `terraform init` command:
```bash
terraform init
```

You should see output like this:
```
Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/azurerm versions matching "2.46.0"...
- Installing hashicorp/azurerm v2.46.0...
- Installed hashicorp/azurerm v2.46.0 (signed by HashiCorp)
```

Terraform read your **main.tf** file and downloaded the azurerm provider. Run the `tree` command to see the files that were installed:
```bash
tree .terraform
```

![Tree Command Output](images/tree_command.png)

Terraform stores all its providers and modules in a hidden directory named `.terraform`. Now that you have the provider installed you're ready to start building on Azure!

In order to use the Azure provider, Terraform needs to authenticate against the Azure Cloud API. The simplest way to authenticate Terraform is to [use an Azure Service Principal](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/guides/service_principal_client_secret). Fortunately we've already taken care of this part for you in your lab environment. You can view your Azure Service Principal credentials with the following commands:

```bash
echo $ARM_CLIENT_ID
echo $ARM_CLIENT_SECRET
```

Warning: Sensitive credentials such as your Azure Service Principal should always be stored safely, and **never in your Terraform code.** Do not ever store Azure credentials in your `*.tf` files or variables files. They should always be set as environment variables, which we've already done for you in your Shell environment. 

---
## ⚙️ Lab 3: Build a Resource Group
**Topics Covered:**
Azure Portal, Terraform Files, Terraform Plan, Terraform Apply, Idempotence, Azure Resource Groups

**Documentation:**
https://azure.microsoft.com/en-us/features/azure-portal
https://www.terraform.io/docs/language/files/index.html
https://www.terraform.io/docs/cli/commands/plan.html
https://www.terraform.io/docs/cli/commands/apply.html
https://www.terraform.io/docs/language/index.html
https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_group

**Summary:** In this lab you'll start with the most basic Azure building block, [the Resource Group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/manage-resource-groups-portal).

---
### Log onto the Azure Portal
Log onto the Azure Portal now using the credentials on the **Azure Portal** tab. Remember to open the portal in an incognito or private window. Once you're logged in type "resource" into the search box at the top. Click on the **Resource groups** link under Services. The Azure portal will say "No resource groups to display" because you haven't created anything yet. Leave this window open and return to your Text Editor tab in Instruqt.

### Terraform Resource Structure
All terraform resources are structured exactly the same way. Here's a simple example with placeholders describing each part of the resource:

```php
resource "provider_resource" "identifier" {
  attr1 = "value1"
  attr2 = "value2"
}
```

Look at the first line of the code block. The first keyword tells us that this is a **resource** object. The second part is split in two - the first part will always be the name of the provider the resource comes from, for example `azurerm`. The second half represents the specific resource, such as a `virtual machine`. Put them together and you get `azurerm_virtual_machine`. The **identifier** is a short name that you choose for this specific instance of the resource. The identifier can be any short string of text, as long as it is unique. You can use this identifier later in your code to refer to this resource. Then finally you have some attributes sandwiched between the curly braces. These are the buttons and knobs you can tweak to configure the resource. Now let's look at a real Terraform resource:

```php
resource "azurerm_resource_group" "tflab_rg" {
  name     = "tflab-resource-group"
  location = "Central US"
}
```

Note: Make sure you understand the difference between the **identifier** (tflab_rg), and the **name** parameter (tflab-resource-group), which is what shows up in the Azure Portal.

---
### Update the main.tf File
Your **main.tf** file currently has two blocks of code in it, the `terraform` block and the `provider` block. Next you'll add your first resource to the file.

Visit the `azurerm_resource_group` docs page and view the example usage.

https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_group

Add a new resource group named **tflab-resource-group** to your main.tf file and set the location parameter to **Central US**. You can set these with the **name** and location attributes inside your resource block. Use **tflab_rg** as the identifier.

Also note that the `features {}` line is mandatory. Even though it is empty it must be there for the provider to work correctly.

Don't forget to click on the save icon. 

Note: If you want to try a different Azure location you can find a list of available options on the [Azure Regions page](https://azure.microsoft.com/en-us/global-infrastructure/geographies/#geographies).

---
### Run Terraform Plan
Now you're ready for your next Terraform command, `terraform plan`. This is a dry run where you can see what might change before you approve the changes.

```bash
terraform plan
```

The output will look like this:
```
Terraform used the selected providers to generate the following execution plan. Resource actions are
indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # azurerm_resource_group.tflab will be created
  + resource "azurerm_resource_group" "tflab_rg" {
      + id       = (known after apply)
      + location = "centralus"
      + name     = "tflab-resource-group"
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```

---
### Run Terraform Apply
Apply your changes with the `terraform apply` command. You'll need to type **yes** to the "Are you sure?" prompt.

```bash
terraform apply
```

Now head back over to the Azure Portal and click the **Refresh** link near the top of the page. You should see a new resource group called **tflab-resource-group** appear on the list.

Note: It can take up to a minute for the resource group to show up in the portal. If you don't see it simply hit the refresh button again.

![Azure Resource Group in Portal](images/azure_resource_group.png)

---
### Create Config Drift
Go ahead and delete this resource group from the Azure portal. You can do this by clicking on the resource group's name and then selecting the **Delete resource group** option from the top nav bar. You'll need to type in the resource group name to confirm the deletion. This simulates a manual change being made outside of Terraform's control.

![Delete Resource Group](images/delete_resource_group.png)

---
### Remediate Config Drift
Try running another plan and apply and see what happens:

```bash
terraform plan
terraform apply
```

Terraform was able to detect that someone tampered with the configuration and deleted our resource group:

```
Note: Objects have changed outside of Terraform

Terraform detected the following changes made outside of Terraform since the last "terraform apply":
```

Note: Terraform is *idempotent* which means it will always try to create the same target state for your infrastructure, even if there is configuration drift.

Good job! You've just built (and rebuilt) your first Terraform resource!

### Terraform Apply with No Changes
If your infrastructure is already in the correct state, Terraform will not make any changes. Try another apply command and see what happens:

```bash
terraform apply
```

Terraform reports back that no changes are required:

```
No changes. Your infrastructure matches the configuration.
```

In the next section you'll add another resource to your code and learn how to connect dependent resources to one another.

---
## ⚙️ Lab 4: Add a Virtual Network
**Topics Covered:**
Azure Virtual Network, Interpolation, Dependencies

**Documentation:**
https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_network
https://www.terraform.io/docs/language/syntax/configuration.html

**Summary:** In this lab we'll build a virtual network and place it into the resource group created in the previous lab.

---
A resource group is like a container for Azure resources. Let's add a virtual network to the resource group you created in the previous lab. Copy the following code into your **main.tf** file:

```php
resource "azurerm_virtual_network" "tflab_vn" {
  name                = "tflab-virtual-network"
  location            = REPLACE_ME
  resource_group_name = REPLACE_ME
  address_space       = ["10.0.0.0/16"]
}
```

Your task is to figure out what to enter for the location and resource_group_name parameters. Have a look at the example docs for configuring the azure_virtual_network resource:

https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_network#example-usage

Hint: The `location` and `resource_group_name` need to refer to the name and location of the resource group you added in the previous lab.

Once you think you have the answer correct, run a `terraform apply` command and see if your virtual network builds. You may use the -auto-approve flag to bypass the yes/no approval question:

```bash
terraform apply -auto-approve
```

Great work. You now have a virtual network. In the next section we'll get more practice with interpolation by adding a subnet.

---
## ⚙️ Lab 5: Create a Subnet
**Topics Covered:**
Azure Subnet, Auto Approve

**Documentation:**
https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/subnet
https://www.terraform.io/docs/cli/commands/apply.html

**Summary:** Add a subnet to the virtual network created in the previous lab.

---
This lab exercise is very similar to the previous one. Copy the following code into your main.tf file and update the REPLACE_ME values:

```php
resource "azurerm_subnet" "tflab_sn" {
  name                 = "tflab-subnet"
  resource_group_name  = REPLACE_ME
  virtual_network_name = REPLACE_ME
  address_prefixes     = ["10.0.1.0/24"]
}
```

Look at the example documentation for the azurerm_subnet resource here:
https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/subnet

As before, run a `terraform apply -auto-approve` to check your work. Visit the Azure portal page for your resource group and verify that you now have a virtual network with a single subnet. 

![Portal with Subnet](images/portal_with_subnet.png)

---
## ⚙️ Lab 6: Tag Your Resources
**Topics Covered:**
Tagging, Nested Blocks

**Documentation:**
https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/tag-resources?tabs=json
https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/resource_group
https://www.terraform.io/docs/language/attr-as-blocks.html

**Summary:** Learn how to create tags for your resources using Terraform.

---
Azure allows you to tag any resource with key/value pairs of text. You can use tags to keep track of resources and identify who built them, what they are for, what environment they belong in and more. Tags can be used on almost every Terraform resource in the Azure provider. Here's an example to show you how tags are formatted:

```php
tags = {
  key1 = "value1"
  key2 = "value2"
  key3 = "More text here."
}
```

Simple, right? Add the following tags to your virtual network resource:

```
environment = "Production"
owner = "Your Name"
```

Hint: The tags block must be placed *inside* of the resource block.

Once you have your resources tagged properly check your work in the Azure portal. From this point forward we won't always remind you to run `terraform apply` since you already know the process.

---
## ⚙️ Lab 7: Variables
**Topics Covered:**
Variables File, Configuring Variables, Precedence

**Documentation:**
https://www.terraform.io/docs/language/values/variables.html

**Summary:** In this lab you'll use variables to make your terraform code more flexible.

---
So far all our settings have been hard-coded into our Terraform configs. You'll probably want to make your code more flexible for different users and environments, and that's where variables come in. Variables allow you to replace those hard-coded settings with placeholders that users can change to suit their needs.

### Terraform Variables Basics
In your Shell tab create a new file called `variables.tf` with the following command:

```bash
touch variables.tf
```

Now head back into your Text Editor tab and open the file. Copy the following contents into `variables.tf`:

```php
variable "location" {
  default = "Central US"
  description = "Azure location where resources should be built."
}
```

Save the file. Now open the `main.tf` file and find the `location` line in your azurerm_resource_group resource. It should say "West Europe" or "Central US" or similar. Next you're going to replace that hard coded variable so your resource looks like this:

```php
resource "azurerm_resource_group" "tflab_rg" {
  name     = "tflab-resource-group"
  location = var.location
}
```

See how the `var.location` reference has replaced the plain text? Terraform will now allow you to insert any valid location at run time. If the user doesn't choose a value for location it will use the default that you defined in the **variables.tf** file.

Run an apply and see if anything changes. Next try changing the default location in your variables file to something different, for example `East US`. Run `terraform apply` again. What happens?

Warning: Once in a while Terraform may become stuck if you change the location parameter and try to rebuild everything. If this happens simply run a `terraform destroy` and rebuild with another `terraform apply`.

---
### Create an Owner Variable
Using what you learned in the previous lab, create a new variable called **owner** and set the default to your own name. Replace the owner tag text in your **main.tf** code with a variable.

---
### Set Variables with the CLI
You can override any default variable with the `-var` command line flag. For example:

```bash
terraform apply -var "owner=Bugs Bunny"
```

---
### Set Variables as Environment Vars
The second way to configure Terraform variables is through the use of a specially formatted system environment variable. Both Linux and Windows support the use of environment variables, sometimes called "env vars". Terraform will read any system environment variable that begins with the `TF_VAR_` prefix. Configure a new environment variable in your shell with the following command:

```bash
export TF_VAR_owner="Donald Duck"
```

Now try running `terraform apply`.

---
### Set Variables in a tfvars File
Another way to configure variables is inside of a tfvars file. Think of this as your personal settings file, where you can override the defaults that we created in variables.tf.

Create a new file called **terraform.tfvars** in your workspace. Put the following line into the file:

```php
owner = "Mickey Mouse"
```

Save the file and run an apply. What happens?

---
### Variable Precedence
So far you've learned four different ways to configure variables. You may be curious which will take precedence if they are set in more than one location. Here's a handy list to show you which setting will win the battle. Settings on top will override those lower on the list:

1. Command line flag - run as a command line switch
2. Configuration file - set in your terraform.tfvars file
3. Environment variable - part of your shell environment
4. Default Config - default value in variables.tf
5. User manual entry - if not specified, prompt the user for entry

---
## ⚙️ Lab 8: Outputs
**Topics Covered:**
Terraform Outputs

**Documentation:**
https://www.terraform.io/docs/language/values/outputs.html

**Summary:** In this lab you'll learn how to output useful information at the end of a Terraform run.

---
Now that you have built some infrastructure you might want to output some useful information to the user once the Terraform run is complete. This is accomplished by using [outputs](https://www.terraform.io/docs/language/values/outputs.html).

Outputs usually live in their own `.tf` file. Go ahead and create a file called **outputs.tf** now:

```bash
touch outputs.tf
```

Here's an example output to get you started. Copy this code into your **outputs.tf** file and save it.


```php
output "subnet_name" {
  value = azurerm_subnet.tflab_sn.name
}
```

Run an apply and observe the output.

You can output just about any attribute or even plain text. Here's an example that uses interpolation to combine text with a dynamic value. Update your outputs file to look like this. Note how the variable is enclosed in `${}` so terraform knows that it is an attribute:

```php
output "subnet_name" {
  value = "Subnet Name: ${azurerm_subnet.tflab_sn.name}"
}
```

Now try it on your own. Create another output that shows the subnet ID.

Hint: You can use the [attributes reference](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/subnet#attributes-reference) from the docs site to see what valid attributes you can use in an output.

Once you have both the subnet name and subnet ID in your outputs after each run, move on to the next lab.

---
## ⚙️ Lab 9: Destroy
**Topics Covered:**
Terraform Destroy

**Documentation:**
https://www.terraform.io/docs/cli/commands/destroy.html

**Summary:** In this lab you'll clean up your sandbox environment and prepare for the next lab.

---
The `terraform destroy` command will remove any infrastructure defined in your workspace. Before we move on to the next lab let's clean up everything we've built so far. Run a destroy command in your workspace:

```bash
terraform destroy
```

Warning: This powerful command can take down your entire production environment if it is not used carefully. Always double and triple check that the things you are destroying are not in use!

---
## ⚙️ Lab 10: Create a Linux VM
**Topics Covered:**
Azure Linux Virtual Machine, Random Provider, Random Pets

**Documentation:**
https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/linux_virtual_machine
https://registry.terraform.io/providers/hashicorp/random
https://registry.terraform.io/providers/hashicorp/random/latest/docs/resources/pet

**Summary:** In this lab you'll build a linux virtual machine with a unique pet name.

---
Run the following command to prepare your sandbox workspace for the lab:

```bash
setup.sh 10
```

This script will populate your sandbox directory with terraform code that builds an Ubuntu 18.04 Linux server with all its dependencies. We'll be using this code for the rest of the lab exercises. Take a moment and look through the **main.tf** file to see what's been added. You may notice a special resource near the top of the file:

```php
resource "random_pet" "tflab_pet" {
  length = 2
}
```

This is one of our favorite Terraform resources. It generates [random pet names](https://registry.terraform.io/providers/hashicorp/random/latest/docs/resources/pet) that you can use when you need unique names for things. This is useful when you need a DNS name, storage id, or any kind of unique identifier. Try running `terraform apply` now and see what kind of pet you get!

This Terraform run will take a bit longer than the previous labs because we're building more infrastructure. You can watch your infrastructure populate the resource group in the Azure portal. When the terraform run is complete proceed to the next lab.

Note: The terraform run will output some URLs for your web server but they aren't working yet. We'll fix this in the next lab.

---
## ⚙️ Lab 11: Provisioners
**Topics Covered:**
Terraform Provisioners, File Provisioner, Remote Exec Provisioners, Terraform Taint

**Documentation:**
https://www.terraform.io/docs/language/resources/provisioners/
https://www.terraform.io/docs/language/resources/provisioners/file.html
https://www.terraform.io/docs/language/resources/provisioners/remote-exec.html
https://www.terraform.io/docs/cli/commands/taint.html

**Summary:** In this lab you'll use Terraform provisioners to configure your new web server.

---
### The File Provisioner
Now that you have a virtual machine let's install a web application on it. Terraform does not configure your OS or application settings directly, instead it uses a [provisioner](https://www.terraform.io/docs/language/resources/provisioners/syntax.html). Provisioners are designed to run once the first time you do a `terraform apply`. They will not run on subsequent applies. This is so you don't clobber a running machine by overwriting important settings with a configuration script.

First let's add a **file** provisioner to put our setup script onto the server. Copy the following code into the `azurerm_linux_virtual_machine` resource:

```php
  provisioner "file" {
    source      = "files/setup_webserver.sh"
    destination = "/home/${self.admin_username}/setup_webserver.sh"

    connection {
      type     = "ssh"
      user     = self.admin_username
      password = self.admin_password
      host     = azurerm_public_ip.tflab_pip.fqdn
    }
  }
```

---
### The Remote Exec Provisioner
The remote exec provisioner allows you to run arbitrary commands on the target host. Next we'll add a **remote_exec** provisioner to our VM to run the script that was uploaded in the previous step. Look at the **inline** commands that get run in the block of code below. These commands run on the target host and not your local machine.

The **connection** block describes how Terraform should connect to the remote machine. In this case we're using SSH with a username in password. This is not recommended for production where you'd use an SSH key instead.

Add a **remote_exec** provisioner right below the file provisioner in your main.tf file, inside the `azurerm_linux_virtual_machine` resource:

```php
  provisioner "remote-exec" {
    inline = [
      "cd /home/${self.admin_username}",
      "chmod +x /home/${self.admin_username}/setup_webserver.sh",
      "sudo ./setup_webserver.sh"
    ]

    connection {
      type     = "ssh"
      user     = self.admin_username
      password = self.admin_password
      host     = azurerm_public_ip.tflab_pip.fqdn
    }
  }
```

Try running a `terraform apply` once your provisioners are in place. Nothing changes. Why?

Hint: Remember what we said about provisioners only running on the first apply...

---
### Terraform Taint
Provisioners are only meant to execute on the very first Terraform apply. How can we rebuild our webserver without tearing down all the rest of the infrastructure? The answer is the [terraform taint](https://www.terraform.io/docs/cli/commands/taint.html) command. With this useful command you can mark specific resources for rebuild on the next run. Try it now:

```bash
terraform taint azurerm_linux_virtual_machine.tflab_linux_vm
```

Terraform will report back that the resource has been tainted:

```
Resource instance azurerm_linux_virtual_machine.tflab_linux_vm has been marked as tainted.
```

Now run another `terraform apply -auto-approve` to rebuild your VM and run the **file** and **remote_exec** provisioners. It may take a few minutes for your old machine to be destroyed and replaced with a brand new one, feel free to take a break here while Terraform finishes running.

When the run completes you should be able to access the URL shown in the Terraform output. Click the URL to see your HashiCat Meow World application:

![Meow World Application](images/meow_world.png)

Note: Provisioners are meant to be used as a last resort when other configuration methods are not available. You can use [HashiCorp Packer](https://www.packer.io/) to build virtual machine images, and then use a [custom data](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/virtual_machine#custom_data) script to provision your application at launch time. This is a much cleaner solution that doesn't require SSH to be open to run! Read more about custom data scripts on the Windows Azure docs: https://docs.microsoft.com/en-us/azure/virtual-machines/custom-data

---
## ⚙️ Lab 12: Local Values
**Topics Covered:**
Local Values, Common Tags

**Documentation:**
https://www.terraform.io/docs/language/values/locals.html

**Summary:** In this lab you'll use local values to store common tags for all your Azure resources.

---
Congratulations on building your new Meow World application server. There's one problem though, none of your resources are tagged properly. Let's use a Terraform [local value](https://www.terraform.io/docs/language/values/locals.html) to define some common tags that can be applied to all your resources.

Copy the following block of code into your **main.tf** file, right underneath the provider block. You can replace the "Your Name" value with your own name if you like. This code defines an array of tags that you can refer to with the `common_tags` local.

```php
locals {
  # Common tags to be assigned to all resources
  common_tags = {
    environment  = "Development"
    owner        = "Your Name"
  }
}
```

Next, add tags to every resource in your **main.tf** file that supports tagging. Read the documentation on using local values to find the correct syntax for adding your tags:

https://www.terraform.io/docs/language/values/locals.html#using-local-values

Hint: All of your resources support tagging except for `random_pet`, `azurerm_subnet` and `azurerm_network_interface_security_group_association`.

## ⚙️ Lab 13: Format (fmt)
**Topics Covered:**
Terraform fmt

**Documentation:**
https://www.terraform.io/docs/cli/commands/fmt.html

**Summary:** The `terraform fmt` command is a handy way to tidy up your code.

---
Your code may look a little messy after all that copying and pasting. Here's a handy shortcut you can run anytime you want to clean up and format your code. Run a `terraform fmt` command now to fix all the indentation and spacing in your code:

```bash
terraform fmt
```

Open the **main.tf** file and behold the neatness. If you have OCD tendencies, you will love this command.

---
## ⚙️ Lab 14: Built-in Functions
**Topics Covered:**
Built-in Functions

**Documentation:**
https://www.terraform.io/docs/language/functions/index.html

**Summary:** In this lab you'll learn about Terraform's built in programming functions for working with different types of data.

---
Terraform is known as a declarative language, but it also comes packed with [useful functions](https://www.terraform.io/docs/language/functions/index.html) that you can use to do math, date and time calculations, numeric and string functions and much more.

Let's add a `timestamp()` tag to all our resources. This lab will build on what you learned about local common tags in the Local Values lab.

Take a look at the documentation for the function:
https://www.terraform.io/docs/language/functions/timestamp.html

Now add another tag called `created` to your `common_tags` array with the timestamp value. Run a `terraform apply` to add your new timestamp tag to all the resources.

Look at the Azure portal and verify that your resources all received the new tag.

---
## ⚙️ Lab 15: Data Sources
**Topics Covered:**
Data Sources, Azure Platform Image, Dad Jokes

**Documentation:**
https://www.terraform.io/docs/language/data-sources/index.html
https://www.terraform.io/docs/language/values/locals.html
https://icanhazdadjoke.com/api

**Summary:** In this lab you'll use a data source to pull in external data for use in your Terraform run, and then use a local value to store and convert the data into plain text.

---
Many Terraform providers come with [Data Sources](https://www.terraform.io/docs/language/data-sources/index.html), which allow you to query the provider API for useful information that can be utilized during a Terraform run. Terraform also comes with an **http** provider data source which can be used to query any API that produces JSON or text output.

Copy the following `data` and `locals` blocks right after the locals block you created in the previous lab. The data block fetches data from the Dad Joke API, and the locals block stores the joke body as a text string. 

```php
data "http" "tflab_joke" {
  url = "https://icanhazdadjoke.com"
  request_headers = {
    "Accept" = "application/json"
  }
}

locals {
  json_data = jsondecode(data.http.tflab_joke.body)
}
```

The `json_data` local value is simply a way of storing the output of the `jsondecode` command.

Note: You can have multiple `locals` blocks in your Terraform code. It can be useful to group related local values together in the same block.

You can use your external data anywhere in your terraform code. For now we'll just use it in an output. Add an output to your **outputs.tf** file called `dad_joke` with the value set as `local.json_data.joke`.

Once you've got your output configured you'll need to re-init your workspace because we've added a new provider:

```bash
terraform init
```

Now you can run `terraform apply` to test the external data source:

```bash
terraform apply -auto-approve
```

Congratulations, now you will get a corny dad joke along with every Terraform run. `Hi Hungry, I'm Dad!`

Hint: You can look at [Terraform outputs documentation](https://www.terraform.io/docs/language/values/outputs.html) if you forgot how to structure the output code.

---
## ⚙️ Lab 16: Modules
**Topics Covered:**
Terraform Modules, Public Module Registry

**Documentation:**
https://registry.terraform.io/
https://www.terraform.io/docs/language/modules/develop/index.html
https://registry.terraform.io/modules/Azure/compute/azurerm/latest

**Summary:** In this lab you'll use a module to deploy a second server into your virtual network.

---
Terraform encourages code reusability through the use of [modules](https://www.terraform.io/docs/language/modules/develop/index.html). A module is simply a directory full of re-usable Terraform code. The module can be stored on your local machine, a Git repository, or a Terraform Cloud module registry. The inputs to the module are the variables, and the outputs are the Terraform outputs.

You can think of the module like a black box. A user provides inputs, the module runs and builds some infrastructure, and then produces outputs. The user isn't allowed to tinker with the internal workings of the module. The benefits are more compact code, better security, standardized configurations, and code re-usability.

The central repository for public Terraform modules is located at [registry.terraform.io](https://registry.terraform.io/browse/modules). You can search through all the available modules by keyword to see if someone's already written a module that you need.

Take a look at the documentation for the Azure compute module, which simplifies the deployment of multiple Windows or Linux VMs into a chosen subnet:

https://registry.terraform.io/modules/Azure/compute/azurerm/latest

Copy the `linuxservers` module block and `linux_vm_public_name` from the example code in the docs into your own **main.tf** and **outputs.tf** files. Adjust the settings so that the `resource_group_name`, `vnet_subnet_id`, `public_ip_dns` and `depends_on` settings are correct.

You'll need to run another `terraform init` since you added a new module to your configuration.

Run a `terraform apply` and test your code. You should see the module build a second Linux server in your virtual network. Verify that you have two virtual machines in the Azure portal:

![Two VMs](images/two_servers.png)

Hint: You can use your random pet name for the unique name of `public_ip_dns`. This is one of the more challenging labs. Don't be afraid to take a [peek at the answer](https://github.com/hashicorp/terraform-azure-labs/blob/main/lab_answers/16/main.tf#L185-L193) if you are stuck.

---
## ⚙️ Lab 17: State
**Topics Covered:**
Terraform State

**Documentation:**
https://www.terraform.io/docs/language/state/index.html

**Summary:** In this lab you'll explore the Terraform state file and learn how Terraform manages state.

---
Terraform is a *stateful* application. Every time you run an apply Terraform takes a snapshot of the infrastructure that was built and stores what it knows about the state in a file named `terraform.tfstate`. You may have noticed this file sitting there in your workspace. Go ahead and open it up in your Text Editor now.

Hint: You can enable color syntax highlighting in the Text Editor to make the file easier to read. In the far lower right corner of your editor there is a pop-up menu that lets you set the file type. If you set this to **json** you'll get color syntax highlighting for your state file.

The state file is used on every subsequent run after the first one to compare known state with current state. This is how Terraform knows whether something should be added, changed, or deleted. If you lose your state file then Terraform will have no way to know what is already under management.

Browse through the state file and look for `admin_password`. Oops - there's a password in plaintext! Terraform does it's best to mask sensitive data in the state file, but secrets can and will sometimes end up in your state file. Always treat the state file as a sensitive artifact because it contains all the data required to provision and keep track of your infrastructure.

Warning: Do not delete your state file and do not commit it to a public git repo. The best practice is to store the state file in a private and secure [remote backend](https://www.terraform.io/docs/language/settings/backends/remote.html) like Terraform Cloud.

---
### Prepare Your Workspace
Before we proceed we are going to simplify your configuration and go back to a single virtual machine to keep things simple. Your terraform runs will go faster with just one VM to worry about.

Run the following setup command to prepare your environment for Lab 18:

```bash
setup.sh 18
```

Warning: Do not skip this step! If you don't run the setup script first the next labs will not go well for you.

The script can take several minutes to run. Take a break here and return to the lab once the setup is complete.

---
## ⚙️ Lab 18: Terraform Cloud & Remote State
**Topics Covered:**
Terraform Cloud, Remote Backend, Terraform Login

**Documentation:**
https://www.terraform.io/cloud
https://www.terraform.io/docs/language/settings/backends/remote.html
https://www.terraform.io/docs/cli/commands/login.html

**Summary:** In this lab you'll migrate your local state file into the secure Terraform Cloud remote backend.

---
[Terraform Cloud](https://www.terraform.io/cloud) is a SaaS application that provides features like state storage, version control integration, policy enforcement, and more.

Until now we've been using Terraform Open Source to do all our work locally on our Linux workstation. This is fine for development and experimentation, but in a real environment you will want to store your state file in a secure [remote backend](https://www.terraform.io/docs/language/settings/backends/remote.html). Several different remote backends are supported for state file storage, but the easiest one to use is Terraform Cloud.

---
### Create an Account and Organization
If you don't have a Terraform Cloud account yet go ahead and sign up for one here. If you do have an account sign into it now.

https://www.terraform.io/cloud

Next, create a new organization. The name of your new organization must be unique. You may wish to use something like **myname-tflab**. 

![Create an Organization](images/create_an_org.png)

The next page will ask you to create a new workspace. **Do not do this yet.** We're going to create a workspace automatically in the next step.

---
### Log on from the CLI
The Terraform command line tool comes with a helper command called `terraform login`. Run the command now to generate a new API token and configure it:

```bash
terraform login
```

You'll see a message like the one below. Enter `yes` to proceed:

```
root@workstation:~/sandbox# terraform login
Terraform will request an API token for app.terraform.io using your browser.

If login is successful, Terraform will store the token in plain text in
the following file for use by subsequent commands:
    /root/.terraform.d/credentials.tfrc.json

Do you want to proceed?
  Only 'yes' will be accepted to confirm.

  Enter a value:
```

Next you'll be prompted to visit the token generation page.  Click the link in the message to go to that page now.

```
---------------------------------------------------------------------------------

Open the following URL to access the tokens page for app.terraform.io:
    https://app.terraform.io/app/settings/tokens?source=terraform-login

---------------------------------------------------------------------------------

Generate a token using your browser, and copy-paste it into this prompt.

Terraform will store the token in plain text in the following file
for use by subsequent commands:
    /root/.terraform.d/credentials.tfrc.json
```

You can choose a descriptive name for this token so you can easily identify it later.

![Name Your Token](images/name_your_token.png)

Click on **Create API Token** to proceed.

Finally you need to copy this token and paste it back into the waiting prompt in your Shell terminal. You won't see the token show up on the screen. The simplest way to ensure that you paste the token in correctly is to right click in the Shell terminal and select **Paste**. If all went well you'll see welcome message with the Terraform logo:

![TFC Console Welcome](images/tfc_welcome_message.png)

---
### Configure Remote State
Now we're ready to configure remote state storage for our workspace. Create a new file called `remote_backend.tf` in your sandbox directory:

```bash
touch remote_backend.tf
```

Now populate that file with the following code, replacing `my-organization` with the name of your own organization:

```
terraform {
  backend "remote" {
    organization = "my-organization"

    workspaces {
      name = "tflab"
    }
  }
}
```

Save your remote backend file.

Now you can migrate your local state file into the TFC remote backend. Run a `terraform init` command:

```bash
terraform init
```

Terraform will configure your workspace with the TFC remote backend.

Warning: If you get a prompt to migrate your state file, answer **no** to the prompt. This happens when Terraform detects an existing state file in the local directory. Fix the problem by running `setup.sh 18`, then reconfigure your **remote_backend.tf** file as above and repeat the `terraform init` command.

Look for the following message:

```
Terraform has been successfully initialized!
```

If you see this message it is now safe to delete your local state file. Let's do that now:

```bash
rm terraform.tfstate
```

There's one last step required to set up Terraform for local runs with remote state storage. Go to the Terraform Cloud website and click on the **Workspaces** tab. You'll see a new workspace called **tflab**. Click on the tflab workspace.

Next, click on the **Settings** > **General** link from the pull-down menu:

![General Settings](images/workspace_settings.png)

Scroll down a bit to the **Execution Mode** section and change it to **Local**.

![Local Execution](images/local_execution.png)

Scroll down to the bottom of the page and click the **Save Settings** button.

---
### Trigger a Run
Now you're ready to trigger a terraform run using remote state storage. Try it in your Shell tab now:

```bash
terraform apply -auto-approve
```

You may now see all your state history in the **States** tab of your workspace in TFC. The state file will be automatically locked if a run is in progress:

![State Lock](images/state_lock.png)

Congratulations, now your state files are securely stored in Terraform Cloud where they won't accidentally be deleted or lost.

---
## ⚙️ Lab 19: Remote Execution
**Topics Covered:**
Remote Execution, Secure Variables, Terraform Cloud Variables

**Documentation:**
https://www.terraform.io/docs/cloud/run/index.html

**Summary:** In this lab you'll switch from local execution on your workstation to remote execution in the cloud.

---
All your terraform runs so far have happened on your workstation. This is fine for development and testing but in secure automated environments it's better to use the [Remote Execution](https://www.terraform.io/docs/cloud/run/index.html#remote-operations) capability of Terraform Cloud. Let's migrate to remote execution.

Go into the **General** page of your workspace settings and switch the execution mode back to **Remote**. Now try another Terraform apply:

```bash
terraform apply -auto-approve
```

Whoops - it looks like we don't have any credentials! This is because we haven't copied our Azure credentials into secure variables in TFC yet. Let's fix that now. Visit the **Variables** tab in your workspace settings and scroll down to the bottom where you see **Environment Variables**.

You can reveal your Azure service principal credentials with the following command:

```bash
env | grep "ARM_"
```

Note how there are four parts to your credentials:

```
ARM_SUBSCRIPTION_ID=c4ea3374-2ed2-4ddc-b88f-e299f095c630
ARM_TENANT_ID=ab2e4aa2-3855-48b9-8d02-619cee6d9513
ARM_CLIENT_SECRET=Pn0-LA79UpImv3GL
ARM_CLIENT_ID=2717c06b-eeb5-4de9-948e-4399ab9e868e
```

All four of these environment variables must be configured in TFC. Use the **Add Variable** button to create a new environment variable for each of them. Mark the `ARM_CLIENT_SECRET` variable as sensitive so it is not shown in plain text!

When you're done you should have four variables underneath the **Environment Variables** section (not the top Terraform Variables section):

![Environment Variables in TFC](images/environment_variables.png)

Now you can trigger another Terraform run:

```bash
terraform apply -auto-approve
```

Note that the run is now happening inside TFC with the output being streamed back to your terminal. You may also trigger Terraform runs using the Terraform UI. Try clicking on the **Actions** pulldown menu in the upper right corner and kick off a run from the UI:

![Trigger Plan from UI](images/trigger_plan_ui.png)

You can also watch the plan run via the UI. Note how the GUI provides a more graphical view of what resources are being created.

Great work, you have now securely stored your Azure credentials and enabled remote runs. There's one problem though - your Terraform code is all still stored on your workstation. This makes it difficult for other users to collaborate on changes, and it also exposes you to risk.

In the next lab we'll migrate all of your Terraform code into a Git repo, and then connect the repo to Terraform Cloud for automated, VCS-driven runs.

---
## ⚙️ Lab 20: Version Control System (VCS)
**Topics Covered:**
Version Control Systems, Git, VCS Integration

**Documentation:**
https://www.terraform.io/docs/cloud/vcs/index.html

**Summary:** In this lab you'll move your code into a Git repo and connect it to your TFC workspace.

---
Note: If you haven't used Git before we strongly recommend doing the **Appendix A: A Taste of Git** lab before proceeding. A basic understanding of Git is required for this lab to go smoothly.

Before we create your new GitHub repository a bit of housekeeping is required. Do the setup steps below:

---
### Set Up Your Username and Email
Before we start working with the Git command line we need to run two housekeeping commands. These commands identify you so that your code commits can be tagged. Run these two commands in your Shell tab, replacing the name and email with your own:

```bash
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

---
### Create a New GitHub Repo
If you don't have a [github.com](https://github.com/) account yet go ahead and create one. Next, use the plus sign menu in the upper right corner of the screen to create a **New Repository**. Name your repository **tflab** and make sure you set the repo to **Public** access. Select the box that says **Add .gitignore** and choose the Terraform option. This ensures that sensitive files like your state file and variables files do not accidentally end up getting committed to the repo.

![gitignore config](images/gitignore_setting.png)

Now you have an empty repository to work with. 

---
### Clone the Remote Repo
In this step we'll clone the empty repository onto our local machine. Run the following commands to change into your home directory and clone the repo:

```bash
cd /root
git clone https://github.com/YOURUSER/tflab
```

Now copy all the files from your **sandbox** directory into the newly cloned **tflab** directory:

```bash
cp -r /root/sandbox/* /root/tflab/
```

Change into the tflab directory. This is where you'll be working for the rest of the labs.

```bash
cd /root/tflab
```

Note: Whoa, what happpened to our prompt? We've installed a special git-enabled shell prompt that will show useful information about the Git repo you're in. You can learn more about that in Appendix A.

---
### Git Credentials Config
Note: GitHub passwords are no longer supported for command line access so you'll need to create a Personal Access Token. If you already have a Personal Access Token you may skip this section.

Visit this link in a web browser and click on the **Generate New Token** button:

https://github.com/settings/tokens

In the Note section you can enter "Terraform Azure Lab". Under the Select Scopes section check the box next to **repo**.

![Git Token Creation](images/git_token.png)

Click on **Generate Token** at the bottom of the page. GitHub will generate you a new personal access token. Save this token in a text file as you'll need it in a moment.

![Personal Access Token](images/personal_access_token.png)

Configure the Git Credential Helper so you won't have to copy your token in every time you make a change. Run this command to set up the credential helper to store your creds for 4 hours.

```bash
git config credential.helper 'cache --timeout 14400'
```

---
### Add, Commit and Push Changes
Now that we have a local copy of our git repo we can add all the files in the tflab directory to staging:

```bash
git add .
```

Next, commit your files:

```bash
git commit -m "Initial commit."
```

Finally push your changes to the remote repo:
```bash
git push
```

Here you'll be prompted for a username and password. Enter your GitHub username and instead of your password, copy in the Personal Access Token that you generated in the previous step. The credential helper will cache your credentials so you don't have to paste them in every time you run a `git push`.

Verify that your repo contains copies of all the local files in your workspace:

![Git Repo Example](images/repo_example.png)

Great work. You're now ready to enable VCS-driven Terraform runs.

---
## ⚙️ Lab 21: VCS Backed Workspaces
**Topics Covered:**
Version Control Systems, VCS-Driven Runs

**Documentation:**
https://www.terraform.io/docs/cloud/run/index.html
https://www.terraform.io/docs/cloud/vcs/github.html

**Summary:** In this lab you'll connect TFC directly to your GitHub repo for automated provisioning based on Infrastructure as Code.

---
Once you connect a VCS repository to your Terraform Cloud workspace, **all** changes to the code must be stored in the VCS before Terraform will execute them. This ensures that you have no unauthorized changes to your infrastructure as code.

In addition it allows you to enable features like code reviews, pull requests, and automated testing of your code.

Follow the **Configuring GitHub Access** section of the TFC documentation to connect your GitHub account to your Terraform Organization:

https://www.terraform.io/docs/cloud/vcs/github.html

Once you have completed the integration process you can go into your workspace settings and connect your workspace to Version Control:

![Connect VCS](images/connect_vcs.png)

Select the **tflab** repo in the configuration wizard. All the default settings are fine.

![Select Repo](images/connect_to_repo.png)

Congratulations, you have enabled VCS-backed runs for your workspace. This unlocks all sorts of useful features like code reviews, automated tests, and tracking of all changes.

---
## 💡 Further Resources
If you'd like to learn more about Terraform and Terraform Cloud here are some useful links you can explore:

https://learn.hashicorp.com/
https://docs.microsoft.com/en-us/azure/developer/terraform/overview
https://docs.microsoft.com/en-us/azure/developer/terraform/
https://learn.hashicorp.com/collections/terraform/azure-get-started
https://www.youtube.com/watch?v=Axs2IH94hbA
https://azuredevopslabs.com/labs/vstsextend/terraform/

---
## ⚙️ Appendix A: A Taste of Git
<a href="https://en.wikipedia.org/wiki/Git" target="_blank">Git</a> is the worlds most popular version control system (VCS). This appendix lab is a crash course in basic Git usage. We'll be using Git to download the answer key git repo and use it to learn and experiment.

---
### **Exercise 1:** Fork and Clone

You'll need a GitHub.com account to do the lab exercises. Visit github.com in a new browser tab and sign in. Visit the following URL and click the **Fork** button in the upper right corner. You can right-click the link to open it in a new window or tab:

https://github.com/hashicorp/terraform-azure-labs

Select the Shell tab and run the following commands to download (clone) a copy of the training repo to your workstation.

```bash
cd /root
git clone https://github.com/YOURGITUSERNAME/terraform-azure-labs
```

Now change into the directory you just downloaded and run the `ls -l` command:

```bash
cd terraform-azure-labs
ls -l
```

Note that your prompt changes when you cd into a Git repository. The git-enabled prompt adds additional information about your git repository including the branch name and any changes that haven't been committed yet.

---
### **Exercise 2:** Git Basics
#### Set Up Your Username and Email
Before we start working with the Git command line we need to run two housekeeping commands. These commands identify you so that your code commits can be tagged. Run these two commands in your Shell tab, replacing the name and email with your own:

```bash
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
```

#### Git Status
The first git command you should learn is `git status`. You can always get a summary of the status of your repo with this command. Try it now in your Shell tab:

```bash
git status
```

Note that the current status message says there is nothing to commit. This is known as a clean working tree. It means you don't have any files that have changed from the last checkpoint. The little green checkmark on your prompt also indicates a clean working tree.

Now let's add a file and see how the status changes. Run the following commands:

```bash
touch myfile.txt
git status
```

Now you'll see your prompt change and the status message change to show the untracked file that you just created. Since Git has never seen this file before it is marked as untracked.

Next let's try editing a file. Go into your text editor and open the README.md file inside the terraform-azure-labs directory. Add your name or a short message on the last line of the file. Save the file by clicking the little disk icon on the file tab. CTRL-S or CMD-S keyboard shortcuts may not work here.

![Edit Files in Instruqt](images/edit_readme.png)

Go back to your Shell tab and check the status again:

```bash
git status
```

Now you have one untracked file, and a modified file. Git keeps track of all changes, additions and deletions to the entire repository.

#### Git Add
Next we'll add both the changed file and the new file so Git can keep track of them. Run the following command:

```bash
git add .
```

The `.` is a shortcut that means "Add all my changes from this directory on downward." You'll be using this shortcut a lot. You can also add individual files by specifying the filename directly.

Run the status command again to see what's different:

```bash
git status
```

Now you'll see your two changes sitting in a 'ready to commit' state. This is known as **staging**. Staged items are changes that you want to make but haven't committed yet. It's a bit like a shopping cart at the grocery store. You can add and remove items to your cart before you make the final decision on what to purchase.

#### Git Commit
The next step is to **commit** your pending changes. Use the following command to create a new commit along with a short message explaining what you did:

```bash
git commit -m "Added a file, edited README.md"
```

You'll see some output that looks like this:

```
[main 45d303a] Added a file, edited README.md
 2 files changed, 1 insertion(+)
 create mode 100644 myfile.txt
```

Note the short commit hash next to the branch name, `45d303a`. Every single commit you make to the git repo has an identifier like this. You can use these hashes to refer to specific commits if you need to go back in time.

Run the status command again and see how it has returned back to a clean working tree.

```bash
git status
```

Your bash prompt now has a green checkmark again but there's a new symbol `↑·1` that indicates you have changes that have not been pushed to the main repo.

---
### **Exercise 3:** Git Credentials Config
GitHub passwords are no longer supported for command line access so you'll need to create a Personal Access Token. Visit this link in a web browser and click on the **Generate New Token** button:

https://github.com/settings/tokens

In the Note section you can enter "Terraform Azure Lab". Under the Select Scopes section check the box next to **repo**.

![Git Token Creation](images/git_token.png)

Click on **Generate Token** at the bottom of the page. GitHub will generate you a new personal access token. Save this token in a text file as you'll need it in a moment.

![Personal Access Token](images/personal_access_token.png)

Configure the Git Credential Helper so you won't have to copy your token in every time you make a change. Run this command to set up the credential helper to store your creds for 4 hours.

```bash
git config credential.helper 'cache --timeout 14400'
```

---
### **Exercise 4:** Remote Repositories - Push and Pull
#### Git Remote
When you collaborate with others on the same codebase you'll want a shared main repository that everyone can contribute to. This is known as a remote repository. Check your remote URL with the following command:

```bash
git remote -v
```

You'll see a remote named `origin` which is the default name for remote repos, with entries for both **fetch** and **push**. This is the same repo you forked earlier.
#### Git Push
Let's push our local changes to the remote repo. Run the following command.

```bash
git push origin main
```

This command means "Push all my local commits to the main branch on the origin repo."

Note: You can also abbreviate this command by simply typing `git push` which uses the default remote repo and branch names, **origin** and **main**.

You'll be prompted to log onto GitHub to authorize the push. Use your GitHub username along with the personal access token you created in the previous step. Your credentials are now safely cached on the workstation for future commands.

#### Git Pull
Now let's make a change on the remote repo and pull it into our local copy. Visit your repo fork in a browser and click on the README.md file. 

Next click on the little pencil shaped icon ✏️ on the upper right side of the preview pane. This will allow you to edit the README.md file in place.  Add some more text to the file using the built in text editor. Enter an optional commit message and click on the **Commit changes** button on the bottom of the page.

![Git Commit GUI](images/git_commit_gui.png)

Go back to your Shell tab and run a git status command:

```bash
git status
```

Your local repo doesn't know anything about the changes on the remote. Let's run another command to fetch the change we just made:

```bash
git fetch
```

Look at how your prompt changed: `↓·1`. This means there is one new commit on the remote repo that we can merge into our local copy. Run this command to merge the change:

```bash
git merge FETCH_HEAD
```

You can combine the fetch and merge steps into a single command, `git pull`. Try it now:

```bash
git pull
```

Note: Advanced git users run `git fetch` and `git pull` often to ensure that they have the latest changes to the codebase.

---
### **Exercise 5:** Git Log
You can output the log for your Git repository with the `git log` command. Try it now. 

```bash
git log
```

You can exit the log viewer by typing capital `ZZ` or `:q`.

This log will show all the commits to your repository since you forked it from the main repo. You can control the log output with various flags. Here's an example that creates a more compact, colorized log with a graph:

```bash
git log --oneline --color --graph
```

---
### Git Command Reference
Here's a quick overview of the most commonly used Git commands:

* `git status` - Shows the current status of your local repo.
* `git clone` - Make a local copy of a remote repo.
* `git log` - Outputs the log to your terminal. Escape with `ZZ`.
* `git add .` - Add all files in the current directory and below to staging.
* `git rm filename` - Removes a file from the staging area.
* `git commit` - Commits all local changes. Use the `-m` flag to append a message.
* `git push` - Push local changes to remote repo.
* `git fetch` - Pull changes from a remote repo.
* `git merge` - Merge changes to current branch.
* `git pull` - Fetch and merge at the same time.
* `git branch` - Show which branch you are currently on.
* `git config` - Alter the Git configuration file.
* `git remote` - Work with remote repositories.
* `git checkout` - Use to switch branches or revert changes to a file.

## ❓ Appendix B: The Answers
Stuck on a challenge? Don't worry, help is here.

Here's a link to all the correct answers. Visit this git repo and you'll find completed versions of lab code in the **lab_answers** directory:

https://github.com/hashicorp/terraform-azure-labs

In a hurry? You can automatically reset your environment to the end of a lab exercise with the `catchup.sh` script. Give the script a lab number and it will automatically clean up your sandbox and finish the lab for you. This is a handy way to get caught up or skip to a particular section. The catchup script works as far as the end of **Lab 17 - Terraform State** after which your Terraform Cloud and VCS settings must be configured manually.

Example script usage below. This will jump you to the end of Lab 7 and the beginning of Lab 8.
```bash
catchup.sh 7
```

The catchup script can take up to two or three minutes to complete.

Warning: The catchup script is not 100% foolproof. If it fails to run you may need to visit the Azure portal and manually delete your resource group. If it still fails after manual cleanup, simply restart this Instruqt track to create a fresh lab environment then try the catchup script again.