# Manage cache settings

Using the Myra Security Terraform Provider it is easy to manage (create, read, update and delete) IP filters.

To have a better structure in your Terraform project, please create a new file `ip_filters.tf`.

## Prerequisite

To manage IP filters, you need a domain and a subdomain (DNS record). You can create a new domain and DNS record, import existing ones or load existing ones as a data source as described in the [Manage domains documentation](./domains.md) and [Manage DNS records documentation](./dns_records.md).

You can create 3 different types of IP filters:
* BLACKLIST
* WHITELIST
* WHITELIST_REQUEST_LIMITER

## Create a new IP filter
To create a new IP filter, you have to add a new `resource` to the `ip_filters.tf` file.
```hcl
resource "myrasec_ip_filter" "block_127_0_0_1" {
  subdomain_name = myrasec_dns_record.www_example_com.name
  type = "BLACKLIST"
  value = "127.0.0.1/32"
  enabled = true
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

  # myrasec_ip_filter.block_127_0_0_1 will be created
  + resource "myrasec_ip_filter" "block_127_0_0_1" {
      + created        = (known after apply)
      + enabled        = true
      + filter_id      = (known after apply)
      + id             = (known after apply)
      + modified       = (known after apply)
      + subdomain_name = "www.example.com"
      + type           = "BLACKLIST"
      + value          = "127.0.0.1/32"
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

  # myrasec_ip_filter.block_127_0_0_1 will be created
  + resource "myrasec_ip_filter" "block_127_0_0_1" {
      + created        = (known after apply)
      + enabled        = true
      + filter_id      = (known after apply)
      + id             = (known after apply)
      + modified       = (known after apply)
      + subdomain_name = "www.example.com"
      + type           = "BLACKLIST"
      + value          = "127.0.0.1/32"
    }

Plan: 1 to add, 0 to change, 0 to destroy.
myrasec_ip_filter.block_127_0_0_1: Creating...
myrasec_ip_filter.block_127_0_0_1: Creation complete after 2s [id=0000000]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```
If you login to the Myra application, you can see the new created IP filter for this subdomain.

## Update an IP filter
To update an IP filter, you have to change the values in the resource definition in your .tf file.
As an example, you can disable the IP filter by changing the `enabled` value from `true` to `false`. Please update the resource definition in the `ip_filters.tf` file to look like this:
```hcl
resource "myrasec_ip_filter" "block_127_0_0_1" {
  subdomain_name = myrasec_dns_record.www_example_com.name
  type = "BLACKLIST"
  value = "127.0.0.1"
  enabled = false
}
```

Executing `terraform plan` to check the changes should output something similar to this:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_ip_filter.block_127_0_0_1: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # myrasec_ip_filter.block_127_0_0_1 will be updated in-place
  ~ resource "myrasec_ip_filter" "block_127_0_0_1" {
      ~ enabled        = true -> false
        id             = "0000000"
        # (6 unchanged attributes hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

After validating the changes, please apply those changes using the `terraform apply` command.
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_ip_filter.block_127_0_0_1: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # myrasec_ip_filter.block_127_0_0_1 will be updated in-place
  ~ resource "myrasec_ip_filter" "block_127_0_0_1" {
      ~ enabled        = true -> false
        id             = "0000000"
        # (6 unchanged attributes hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.
myrasec_ip_filter.block_127_0_0_1: Modifying... [id=0000000]
myrasec_ip_filter.block_127_0_0_1: Modifications complete after 1s [id=0000000]

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.
```

When you check the changes in the Myra application, you should see the updated `enabled` value in the IP filters overview page of the subdomain.

You can also check the data in the `tfstate` file.

## Delete an IP filter
Deleting an IP filter is done by deleting the resource (a managed resource) for this record from the `.tf` file.
When you remove the IP filter from your `ip_filters.tf` file again (so the file is empty) and run `terraform plan`, you should get some output like this:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_ip_filter.block_127_0_0_1: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_ip_filter.block_127_0_0_1 will be destroyed
  # (because myrasec_ip_filter.block_127_0_0_1 is not in configuration)
  - resource "myrasec_ip_filter" "block_127_0_0_1" {
      - created        = "2022-02-16T15:39:12+01:00" -> null
      - enabled        = true -> null
      - filter_id      = 0000000 -> null
      - id             = "0000000" -> null
      - modified       = "2022-02-16T15:39:12+01:00" -> null
      - subdomain_name = "www.example.com" -> null
      - type           = "BLACKLIST" -> null
      - value          = "127.0.0.1/32" -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

Running `terraform apply` will remove this DNS record from the Myra application:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_ip_filter.block_127_0_0_1: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_ip_filter.block_127_0_0_1 will be destroyed
  # (because myrasec_ip_filter.block_127_0_0_1 is not in configuration)
  - resource "myrasec_ip_filter" "block_127_0_0_1" {
      - created        = "2022-02-16T15:39:12+01:00" -> null
      - enabled        = true -> null
      - filter_id      = 0000000 -> null
      - id             = "0000000" -> null
      - modified       = "2022-02-16T15:39:12+01:00" -> null
      - subdomain_name = "www.example.com" -> null
      - type           = "BLACKLIST" -> null
      - value          = "127.0.0.1/32" -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.
myrasec_ip_filter.block_127_0_0_1: Destroying... [id=0000000]
myrasec_ip_filter.block_127_0_0_1: Destruction complete after 5s

Apply complete! Resources: 0 added, 0 changed, 1 destroyed.
```

## Importing an existing IP filter
If you want to manage an IP filter that is already known by Myra (already created in the app or using the API), you can `import` this IP filter to your Terraform project.
To import an existing IP filter, you have to create a resource for this IP filter in your `ip_filters.tf` file.

Re-create an IP filter in the Myra app so that you can import it.
Add the resource for this IP filter you want to import to your `ip_filters.tf` file:
```hcl
resource "myrasec_ip_filter" "allow_127_0_0_1" {
  subdomain_name = myrasec_dns_record.www_example_com.name
}
```

Run `terraform import myrasec_ip_filter.allow_127_0_0_1 www.example.com:0000000` to import the existing IP filter (with ID=0000000 inside of subdomain www.example.com).

```
myrasec_ip_filter.allow_127_0_0_1: Importing from ID "www.example.com:0000000"...
myrasec_ip_filter.allow_127_0_0_1: Import prepared!
  Prepared myrasec_ip_filter for import
myrasec_ip_filter.allow_127_0_0_1: Refreshing state... [id=www.example.com:0000000]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.
```

After importing this IP filter, you have to add the missing information to your resource, so that your infrastructure matches your configuration.
```hcl
resource "myrasec_ip_filter" "allow_127_0_0_1" {
  subdomain_name = myrasec_dns_record.www_example_com.name
  value = "127.0.0.1/32"
  type = "WHITELIST"
  enabled = true
}
```

## Read an IP filter
Terraform has resources and data sources. To only fetch information about an IP filter (but not changing any information about this filter), you can define a data source and load the IP filters to your `tfstate`.

To read/"import" all your IP filters as a data source, please create a data source definition in your `ip_filters.tf` file like this:
```hcl
data "myrasec_ip_filters" "all" {
    filter{
        subdomain_name = myrasec_dns_record.www_example_com.name
    }
}
```

Running `terraform apply` will add the data to your `tfstate`.

Data sources in the `tfstate` have the mode `data` whereas resources have the mode `managed`.

Next steps:
[Manage cache settings](./cache_settings.md)
[Manage settings](./settings.md)
[Manage redirects](./redirects.md)
[Manage WAF rules](./waf_rules.md)
[Manage SSL certificates](./ssl_certificates.md)
[Manage error pages](./error_pages.md)

### Links
[IP filter resource documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/resources/ip_filter)
[IP filters data source documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/data-sources/ip_filters)
