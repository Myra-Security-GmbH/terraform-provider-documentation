# Getting started

## Install Terraform

In order to use Terraform, you have to install the Terraform CLI.  
HashiCorp provides different installation options for different systems. Please have a look at the official [Terraform download page](https://www.terraform.io/downloads) for more details.


Terraform offers a huge amount of [documentation](https://learn.hashicorp.com/terraform) and tutorials about different topics. 
The purpose of this documentation is not about the basic usage of the Terraform CLI. Therefore, please have a look at ["Use the Command Line Interface"](https://learn.hashicorp.com/collections/terraform/cli).

## Prerequisite
To be able to use the Myra Security Terraform Provider, you need an API key and an API secret.  
You can manage your API keys and other profile settings in the Myra user management on your profile page. Please make sure to note and store those credentials in a secure location. Your API secret is visible only once in the moment when you create a new API key.

## Setup a new Terraform project
Create a new directory for your `.tf` files. All files ending with `.tf` will be processed by Terraform. You can have multiple `.tf` files. Like this, you can split your configurations/resources/variables into multiple files.  
For now, please create a directory called `myra` containing a single `main.tf` file.

```
myra
└── main.tf
```

Terraform providers are loaded from the [Terraform Registry](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest).

In order to use the Myra Security Terraform Provider, open the `main.tf` file and add the very basic provider configuration to it.

```hcl
terraform {
  required_providers {
    myrasec = {
      source = "Myra-Security-GmbH/myrasec"
      version = ">= 1.43"
    }
  }
}
```
This tells Terraform which provider (and in which version) to use.

Please save the `main.tf` file, switch to your command line, navigate to the directory that contains this file and run `terraform init`.

The output on your console should look like this:
```

Initializing the backend...

Initializing provider plugins...
- Finding myra-security-gmbh/myrasec versions matching ">= 1.43.0"...
- Installing myra-security-gmbh/myrasec v1.43.0...
- Installed myra-security-gmbh/myrasec v1.43.0 (self-signed, key ID 11AA111111A1A111)

Partner and community providers are signed by their developers.
If you'd like to know more about provider signing, you can read about it here:
https://www.terraform.io/docs/cli/plugins/signing.html

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

Now, you have a new Terraform project. To be able to communicate with Myra, you have to edit the `main.tf` file again and configure the provider to use your API key and your API secret.  

Please add the following configuration below the `terraform {...}` block that defines which provider you use:

```hcl
provider "myrasec" {
  api_key = "1234567890abcdefghijklmnopqrstuvwxyz"
  secret = "1234567890abcdefghijklmnopqrstuvwxyz"
}
```
**NOTE** Please replace the value for `api_key` with your own API key and the value for `secret` with your own API secret, generated in the Myra user management.

### Optional but recommended
As it is very bad practice to use those values hardcoded and visible here, you can use environment variables here, too, instead of plain values.

Please create environment variables:
```
export MYRASEC_API_KEY=1234567890abcdefghijklmnopqrstuvwxyz
export MYRASEC_API_SECRET=1234567890abcdefghijklmnopqrstuvwxyz
```

Now, you can remove the `api_key` and `secret` configuration again. Please change the `provider "myrasec"` configuration in the `main.tf` file:

```hcl
provider "myrasec" {}
```


Now, you are ready to use the Myra Security Terraform Provider.

Next step: [Manage domains](./domains.md)

### Links
- https://www.terraform.io/downloads
- https://learn.hashicorp.com/terraform
- https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest