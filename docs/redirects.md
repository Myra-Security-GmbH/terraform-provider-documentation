# Manage redirects

Using the Myra Security Terraform Provider it is easy to manage (create, read, update and delete) redirects.

To have a better structure in your Terraform project, please create a new file `redirects.tf`.

## Prerequisite

To manage redirects, you need a domain and a subdomain (DNS record). You can create a new domain and DNS record, import existing ones or load existing ones as a data source as described in the [Manage domains documentation](./domains.md) and [Manage DNS records documentation](./dns_records.md).  

## Create a new redirect
To create a new redirect, you have to add a new `resource` to the `redirects.tf` file.
```hcl
resource "myrasec_redirect" "redirect" {
  subdomain_name = myrasec_dns_record.www_example_com.name
  matching_type = "exact"
  type = "permanent"
  source = "/index_old"
  destination = "/index_new"
}
```

**NOTE** Instead of `myrasec_dns_record.www_example_com.name` you could have used `"www.example.com"`. `myrasec_dns_record.www_example_com.name` will point to the existing DNS record resource with the (resource) name `www_example_com`. 

After saving the file, you can run `terraform plan` to see what Terraform will do:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # myrasec_redirect.redirect will be created
  + resource "myrasec_redirect" "redirect" {
      + created        = (known after apply)
      + destination    = "/index_new"
      + enabled        = true
      + id             = (known after apply)
      + matching_type  = "exact"
      + modified       = (known after apply)
      + redirect_id    = (known after apply)
      + sort           = 0
      + source         = "/index_old"
      + subdomain_name = "www.example.com"
      + type           = "permanent"
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

After checking this information, please run `terraform apply`.

```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # myrasec_redirect.redirect will be created
  + resource "myrasec_redirect" "redirect" {
      + created        = (known after apply)
      + destination    = "/index_new"
      + enabled        = true
      + id             = (known after apply)
      + matching_type  = "exact"
      + modified       = (known after apply)
      + redirect_id    = (known after apply)
      + sort           = 0
      + source         = "/index_old"
      + subdomain_name = "www.example.com"
      + type           = "permanent"
    }

Plan: 1 to add, 0 to change, 0 to destroy.
myrasec_redirect.redirect: Creating...
myrasec_redirect.redirect: Creation complete after 4s [id=0000000]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

If you login to the Myra application, you can see the new created redirect for this subdomain.

## Update a redirect
To update a redirect, you have to change the values in the resource definition in your `.tf` file.  
As an example, you can change the `source` from `/index_old` to `/index`. Please update the resource definition in the `redirects.tf` file to look like this:
```hcl
resource "myrasec_redirect" "redirect" {
  subdomain_name = myrasec_dns_record.www_example_com.name
  matching_type = "exact"
  type = "permanent"
  source = "/index"
  destination = "/index_new"
}
```

Executing `terraform plan` to check the changes, should output something similar to this:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_redirect.redirect: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # myrasec_redirect.redirect will be updated in-place
  ~ resource "myrasec_redirect" "redirect" {
        id             = "0000000"
      ~ source         = "/index_old" -> "/index"
        # (9 unchanged attributes hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

After validating the changes, please apply those changes using the `terraform apply` command.
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_redirect.redirect: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # myrasec_redirect.redirect will be updated in-place
  ~ resource "myrasec_redirect" "redirect" {
        id             = "0000000"
      ~ source         = "/index_old" -> "/index"
        # (9 unchanged attributes hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.
myrasec_redirect.redirect: Modifying... [id=0000000]
myrasec_redirect.redirect: Modifications complete after 5s [id=0000000]

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.
```

When you check the changes in the Myra application, you should see the updated `source` value in the redirects overview page of the subdomain.

You can check the data in the `tfstate` file, too. 


## Delete a redirect
Deleting a redirect is done by deleting the resource (a managed resource) for this record from the `.tf` file.  
When you remove the redirect from your `redirects.tf` file again (so the file is empty) and run `terraform plan`, you should get some output like this:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_redirect.redirect: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_redirect.redirect will be destroyed
  # (because myrasec_redirect.redirect is not in configuration)
  - resource "myrasec_redirect" "redirect" {
      - created        = "2022-02-17T12:38:09+01:00" -> null
      - destination    = "/index_new" -> null
      - enabled        = true -> null
      - id             = "0000000" -> null
      - matching_type  = "exact" -> null
      - modified       = "2022-02-17T12:41:19+01:00" -> null
      - redirect_id    = 0000000 -> null
      - sort           = 0 -> null
      - source         = "/index" -> null
      - subdomain_name = "www.example.com." -> null
      - type           = "permanent" -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

Running `terraform apply` will remove this DNS record from the Myra application:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_redirect.redirect: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_redirect.redirect will be destroyed
  # (because myrasec_redirect.redirect is not in configuration)
  - resource "myrasec_redirect" "redirect" {
      - created        = "2022-02-17T12:38:09+01:00" -> null
      - destination    = "/index_new" -> null
      - enabled        = true -> null
      - id             = "0000000" -> null
      - matching_type  = "exact" -> null
      - modified       = "2022-02-17T12:41:19+01:00" -> null
      - redirect_id    = 0000000 -> null
      - sort           = 0 -> null
      - source         = "/index" -> null
      - subdomain_name = "www.example.com." -> null
      - type           = "permanent" -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.
myrasec_redirect.redirect: Destroying... [id=0000000]
myrasec_redirect.redirect: Destruction complete after 7s

Apply complete! Resources: 0 added, 0 changed, 1 destroyed.
```

## Importing an existing redirect
If you want to manage a redirect that is already known by Myra (already created in the app or using the API), you can `import` this redirect to your Terraform project.  
To import an existing redirect, you have to create a resource for this redirect in your `redirects.tf` file.

Re-create a redirect in the Myra app, so you can import it.  
Please add the resource for this redirect you want to import to your `redirects.tf` file:
```hcl
resource "myrasec_redirect" "redirect_imported" {
  subdomain_name = myrasec_dns_record.www_example_com.name
}
```

Please run `terraform import myrasec_redirect.redirect_imported www.example.com:0000000` to import the existing redirect (with ID=0000000 inside of subdomain www.example.com).

```
myrasec_redirect.redirect_imported: Importing from ID "www.example.com:0000000"...
myrasec_redirect.redirect_imported: Import prepared!
  Prepared myrasec_redirect for import
myrasec_redirect.redirect_imported: Refreshing state... [id=www.example.com:0000000]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.
```

After importing this redirect, you have to add the missing information to your resource, so that your infrastructure matches your configuration.

## Read a redirect
Terraform has resources and data sources. To only fetch information about a redirect (but not changing any information about this redirect), you can define a data source and load the redirect to your `tfstate`.

To read/"import" all your redirects as a data source, please create a data source definition in your `redirects.tf` file like this:
```hcl
data "myrasec_redirects" "all" {
  filter {
      subdomain_name = myrasec_dns_record.www_example_com.name
  }
}
```
Running `terraform apply` will add the data to your `tfstate`.

Data sources in the `tfstate` have the mode `data` whereas resources have the mode `managed`.  

Next steps:  
[Manage cache settings](./cache_settings.md)  
[Manage IP filters](./ip_filters.md)  
[Manage rate limits](./ratelimits.md)  
[Manage settings](./settings.md)  
[Manage WAF rules](./waf_rules.md)  
[Manage SSL certificates](./ssl_certificates.md)  
[Manage error pages](./error_pages.md)      

### Links
[Redirect resource documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/resources/redirect)  
[Redirect setting data source documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/data-sources/redirects)