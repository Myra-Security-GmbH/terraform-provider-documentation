# Manage cache settings

Using the Myra Security Terraform Provider it is easy to manage (create, read, update and delete) cache settings.

To have a better structure in your Terraform project, please create a new file `cache_settings.tf`

## Prerequisite

To manage cache settings, you need a domain and a subdomain (DNS record). You can create a new domain and DNS record, import existing ones or load existing ones as a data source as described in the [Manage domains documentation](./domains.md) and [Manage DNS records documentation](./dns_records.md).

## Create a new cache setting
To create a new cache setting, you have to add a new `resource` to the `cache_settings.tf` file.
```hcl
resource "myrasec_cache_setting" "index" {
  subdomain_name = myrasec_dns_record.www_example_com.name
  type = "exact"
  path = "/index.html"
  ttl = 2678400
  not_found_ttl = 3600
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

  # myrasec_cache_setting.index will be created
  + resource "myrasec_cache_setting" "index" {
      + created        = (known after apply)
      + enabled        = true
      + enforce        = false
      + id             = (known after apply)
      + modified       = (known after apply)
      + not_found_ttl  = 3600
      + path           = "/index.html"
      + setting_id     = (known after apply)
      + sort           = 0
      + subdomain_name = "www.example.com"
      + ttl            = 2678400
      + type           = "exact"
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

After checking this information, please run `terraform apply`.

```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # myrasec_cache_setting.index will be created
  + resource "myrasec_cache_setting" "index" {
      + created        = (known after apply)
      + enabled        = true
      + enforce        = false
      + id             = (known after apply)
      + modified       = (known after apply)
      + not_found_ttl  = 3600
      + path           = "/index.html"
      + setting_id     = (known after apply)
      + sort           = 0
      + subdomain_name = "www.example.com"
      + ttl            = 2678400
      + type           = "exact"
    }

Plan: 1 to add, 0 to change, 0 to destroy.
myrasec_cache_setting.index: Creating...
myrasec_cache_setting.index: Creation complete after 2s [id=0000000]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```
If you login to the Myra application, you can see the new created cache setting for this subdomain.

## Update a cache setting
To update a cache setting, you have to change the values in the resource definition in your `.tf` file.
As an example, you can change the `path` from `/index.html` to `/index.php`. Please update the resource definition in the `cache_settings.tf` file to look like this:
```hcl
resource "myrasec_cache_setting" "index" {
  subdomain_name = myrasec_dns_record.www_example_com.name
  type = "exact"
  path = "/index.php"
  ttl = 2678400
  not_found_ttl = 3600
}
```

Executing `terraform plan` to check the changes, should output something similar to this:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_cache_setting.index: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # myrasec_cache_setting.index will be updated in-place
  ~ resource "myrasec_cache_setting" "index" {
        id             = "0000000"
      ~ path           = "/index.html" -> "/index.php"
        # (10 unchanged attributes hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

After validating the changes, please apply those changes using the `terraform apply` command.
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_cache_setting.index: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # myrasec_cache_setting.index will be updated in-place
  ~ resource "myrasec_cache_setting" "index" {
        id             = "0000000"
      ~ path           = "/index.html" -> "/index.php"
        # (10 unchanged attributes hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.
myrasec_cache_setting.index: Modifying... [id=0000000]
myrasec_cache_setting.index: Modifications complete after 2s [id=0000000]

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.
```
When you check the changes in the Myra application, you should see the updated `path` value in the cache settings overview page of the subdomain.

You can check the data in the `tfstate` file, too. 

## Delete a cache setting
Deleting a cache setting is done by deleting the resource (a managed resource) for this record from the `.tf` file.  
When you remove the cache setting from your `cache_settings.tf` file again (so the file is empty) and run `terraform plan`, you should get some output like this:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_cache_setting.index: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_cache_setting.index will be destroyed
  # (because myrasec_cache_setting.index is not in configuration)
  - resource "myrasec_cache_setting" "index" {
      - created        = "2022-02-16T14:56:39+01:00" -> null
      - enabled        = true -> null
      - enforce        = false -> null
      - id             = "0000000" -> null
      - modified       = "2022-02-16T15:02:21+01:00" -> null
      - not_found_ttl  = 3600 -> null
      - path           = "/index.php" -> null
      - setting_id     = 0000000 -> null
      - sort           = 0 -> null
      - subdomain_name = "www.example.com" -> null
      - ttl            = 2678400 -> null
      - type           = "exact" -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

Running `terraform apply` will remove this DNS record from the Myra application:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_cache_setting.index: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_cache_setting.index will be destroyed
  # (because myrasec_cache_setting.index is not in configuration)
  - resource "myrasec_cache_setting" "index" {
      - created        = "2022-02-16T14:56:39+01:00" -> null
      - enabled        = true -> null
      - enforce        = false -> null
      - id             = "0000000" -> null
      - modified       = "2022-02-16T15:02:21+01:00" -> null
      - not_found_ttl  = 3600 -> null
      - path           = "/index.php" -> null
      - setting_id     = 0000000 -> null
      - sort           = 0 -> null
      - subdomain_name = "www.example.com" -> null
      - ttl            = 2678400 -> null
      - type           = "exact" -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.
myrasec_cache_setting.index: Destroying... [id=0000000]
myrasec_cache_setting.index: Destruction complete after 6s

Apply complete! Resources: 0 added, 0 changed, 1 destroyed.
```

## Importing an existing cache setting
If you want to manage a cache setting that is already known by Myra (already created in the app or using the API), you can `import` this cache setting to your Terraform project.
To import an existing cache setting you have to create a resource for this cache setting in your `cache_settings.tf` file.

Re-create a cache setting in the Myra app, so you can import it.
Please add the resource for this cache setting you want to import to your `cache_settings.tf` file:
```hcl
resource "myrasec_cache_setting" "home" {
  subdomain_name = myrasec_dns_record.www_example_com.name
}
```

Please run `terraform import myrasec_cache_setting.home www.example.com:0000000` to import the existing cache setting (with ID=0000000 inside of subdomain www.example.com).

```
myrasec_cache_setting.home: Importing from ID "www.example.com:0000000"...
myrasec_cache_setting.home: Import prepared!
  Prepared myrasec_cache_setting for import
myrasec_cache_setting.home: Refreshing state... [id=www.example.com:0000000]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.
```

After importing this cache setting, you have to add the missing information to your resource, so that your infrastructure matches your configuration.
```hcl
resource "myrasec_cache_setting" "home" {
  subdomain_name = myrasec_dns_record.www_example_com.name
  enabled = true
  enforce = false
  type = "exact"
  path = "/home.html"
  sort = 0
  ttl = 1800
  not_found_ttl = 10
}
```

## Read a cache setting
Terraform has resources and data sources. To only fetch information about a cache setting (but not changing any information about this setting), you can define a data source and load the cache settings to your `tfstate`.

To read/"import" all your cache settings as a data source, please create a data source definition in your `cache_settings.tf` file like this:
```hcl
data "myrasec_cache_settings" "all" {
    filter{
        subdomain_name = myrasec_dns_record.www_example_com.name
    }
}
```

Running `terraform apply` will add the data to your `tfstate`.

Data sources in the `tfstate` have the mode `data` whereas resources have the mode `managed`.

Next steps:  
[Manage IP filters](./ip_filters.md)  
[Manage rate limits](./ratelimits.md)  
[Manage settings](./settings.md)  
[Manage redirects](./redirects.md)  
[Manage WAF rules](./waf_rules.md)  
[Manage SSL certificates](./ssl_certificates.md)  
[Manage error pages](./error_pages.md)  

### Links
[Cache setting resource documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/resources/cache_setting)  
[Cache setting data source documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/data-sources/cache_settings)