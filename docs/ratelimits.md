# Manage rate limits

Using the Myra Security Terraform Provider it is easy to manage (create, read, update and delete) rate limits.

To have a better structure in your Terraform project, please create a new file `ratelimits.tf`.

## Prerequisite

To manage rate limits, you need a domain and a subdomain (DNS record). You can create a new domain and DNS record, import existing ones or load existing ones as a data source as described in the [Manage domains documentation](./domains.md) and [Manage DNS records documentation](./dns_records.md).  

## Create a new rate limit setting
To create a new rate limit setting, you have to add a new `resource` to the `ratelimits.tf` file.
```hcl
resource "myrasec_ratelimit" "ratelimit" {
    subdomain_name = myrasec_dns_record.www_example_com.name
    network = "192.168.0.0/24"
    burst = 50
    timeframe = 60
    value = 1000
}
```

**NOTE** Instead of `myrasec_dns_record.www_example_com.name` you could have used `"www.example.com"`. `myrasec_dns_record.www_example_com.name` will point to the existing DNS record resource with the (resource) name `www_example_com`.  

After saving the file, please  run `terraform plan` to see what Terraform will do:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # myrasec_ratelimit.ratelimit will be created
  + resource "myrasec_ratelimit" "ratelimit" {
      + burst          = 50
      + created        = (known after apply)
      + id             = (known after apply)
      + modified       = (known after apply)
      + network        = "192.168.0.0/24"
      + ratelimit_id   = (known after apply)
      + subdomain_name = "www.example.com"
      + timeframe      = 60
      + type           = (known after apply)
      + value          = 1000
    }

Plan: 1 to add, 0 to change, 0 to destroy.


Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

After checking this information, please run `terraform apply` to continue.

```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # myrasec_ratelimit.ratelimit will be created
  + resource "myrasec_ratelimit" "ratelimit" {
      + burst          = 50
      + created        = (known after apply)
      + id             = (known after apply)
      + modified       = (known after apply)
      + network        = "192.168.0.0/24"
      + ratelimit_id   = (known after apply)
      + subdomain_name = "www.example.com"
      + timeframe      = 60
      + type           = (known after apply)
      + value          = 1000
    }

Plan: 1 to add, 0 to change, 0 to destroy.
myrasec_ratelimit.ratelimit: Creating...
myrasec_ratelimit.ratelimit: Creation complete after 2s [id=0000000]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

If you login to the Myra application, you can see the new created rate limit setting for this subdomain.

## Update a rate limit setting
To update a rate limit setting, you have to change the values in the resource definition in your .tf file.  
For example, you can change the `value` from `1000` to `500`. To do this, please update the resource definition in the `ratelimits.tf` file to look like this:
```hcl
resource "myrasec_ratelimit" "ratelimit" {
    subdomain_name = myrasec_dns_record.www_example_com.name
    network = "192.168.0.0/24"
    burst = 50
    timeframe = 60
    value = 500
}
```

Executing `terraform plan` to check the changes, should output something similar to this:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_ratelimit.ratelimit: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
-/+ destroy and then create replacement

Terraform will perform the following actions:

  # myrasec_ratelimit.ratelimit must be replaced
  ~ resource "myrasec_ratelimit" "ratelimit" {
        id             = "1000007"
      ~ value          = 1000 -> 500
        # (8 unchanged attributes hidden)
    }

Plan: 1 to add, 0 to change, 1 to destroy.


Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

After validating the changes, please apply those changes using the `terraform apply` command.
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_ratelimit.ratelimit: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # myrasec_ratelimit.ratelimit will be updated in-place
  ~ resource "myrasec_ratelimit" "ratelimit" {
        id             = "0000000"
      ~ value          = 1000 -> 500
        # (8 unchanged attributes hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.
myrasec_ratelimit.ratelimit: Modifying... [id=0000000]
myrasec_ratelimit.ratelimit: Modifications complete after 1s [id=0000000]

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.

```

When you check the changes in the Myra application, you should see the updated `` value in the rate limits overview page of the subdomain.

You can also check the data in the `tfstate` file. 


## Delete a rate limit setting
Deleting a rate limit setting is done by deleting the resource (a managed resource) for this record from the `.tf` file.  
When you remove the rate limit setting from your `ratelimits.tf` file again (so the file is empty) and run `terraform plan`, you should get some output like this:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_ratelimit.ratelimit: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_ratelimit.ratelimit will be destroyed
  # (because myrasec_ratelimit.ratelimit is not in configuration)
  - resource "myrasec_ratelimit" "ratelimit" {
      - burst          = 50 -> null
      - created        = "2022-02-17T11:51:16+01:00" -> null
      - id             = "0000000" -> null
      - modified       = "2022-02-17T11:53:01+01:00" -> null
      - network        = "192.168.0.0/24" -> null
      - ratelimit_id   = 0000000 -> null
      - subdomain_name = "www.example.com." -> null
      - timeframe      = 60 -> null
      - type           = "domain" -> null
      - value          = 500 -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.


Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.

```

Running `terraform apply` will remove this DNS record from the Myra application:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_ratelimit.ratelimit: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_ratelimit.ratelimit will be destroyed
  # (because myrasec_ratelimit.ratelimit is not in configuration)
  - resource "myrasec_ratelimit" "ratelimit" {
      - burst          = 50 -> null
      - created        = "2022-02-17T11:51:16+01:00" -> null
      - id             = "0000000" -> null
      - modified       = "2022-02-17T11:53:01+01:00" -> null
      - network        = "192.168.0.0/24" -> null
      - ratelimit_id   = 0000000 -> null
      - subdomain_name = "www.example.com." -> null
      - timeframe      = 60 -> null
      - type           = "domain" -> null
      - value          = 500 -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.
myrasec_ratelimit.ratelimit: Destroying... [id=0000000]
myrasec_ratelimit.ratelimit: Destruction complete after 6s

Apply complete! Resources: 0 added, 0 changed, 1 destroyed.
```

## Importing an existing rate limit setting
If you want to manage a rate limit setting that is already known by Myra (already created in the app or using the API), you can `import` this rate limit setting to your Terraform project.    
To import an existing rate limit setting, you have to create a resource for this rate limit setting in your `ratelimits.tf` file.

Re-create a rate limit setting in the Myra app so that you can import it.    
Add the resource for this rate limit setting you want to import to your `ratelimits.tf` file:
```hcl
resource "myrasec_ratelimit" "ratelimit_imported" {
    subdomain_name = myrasec_dns_record.www_example_com.name
}
```

Run `terraform myrasec_ratelimit.ratelimit_imported import www.example.com:0000000` to import the existing rate limit setting (with ID=0000000 inside of subdomain www.example.com).


After importing this IP filter, you have to add the missing information to your resource, so that your infrastructure matches your configuration.

```
myrasec_ratelimit.ratelimit_imported: Importing from ID "www.example.com:0000000"...
myrasec_ratelimit.ratelimit_imported: Import prepared!
  Prepared myrasec_ratelimit for import
myrasec_ratelimit.ratelimit_imported: Refreshing state... [id=www.example.com:0000000]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.
```

## Read a rate limit setting
Terraform has resources and data sources. To only fetch information about a rate limit setting (but not changing any information about this setting), you can define a data source and load the rate limit settings to your `tfstate`.

To read/"import" all your rate limit settings as a data source, create a data source definition in your `ratelimits.tf` file like this:
```hcl
data "myrasec_ratelimits" "all" {
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
[Manage settings](./settings.md)  
[Manage redirects](./redirects.md)  
[Manage WAF rules](./waf_rules.md)  
[Manage SSL certificates](./ssl_certificates.md)  
[Manage error pages](./error_pages.md)   
   
### Links
[Rate limit setting resource documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/resources/ratelimit)  
[Rate limit setting data source documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/data-sources/ratelimits)