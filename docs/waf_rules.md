# Manage WAF rules  

Using the Myra Security Terraform Provider it is easy to manage (create, read, update and delete) WAF rules.

To have a better structure in your Terraform project, create a new file `waf_rules.tf`.

## Prerequisite

To manage WAF rules, you need a domain and a subdomain (DNS record). You can create a new domain and DNS record, import existing ones or load existing ones as a data source as described in the [Manage domains documentation](./domains.md) and [Manage DNS records documentation](./dns_records.md).  

As a WAF rule has one or more conditions as well as one or more actions, you can load the available actions and conditions using a data provider.

To load all available actions, you can use the `myrasec_waf_actions` data provider. 
```hcl
data "myrasec_waf_actions" "all" {
    filter {}
}
```

To load all available conditions, you can use the `myrasec_waf_conditions` data provider. 
```hcl
data "myrasec_waf_conditions" "all" {
    filter {}
}
```
Executing `terraform apply` will load this data to your `tfstate`.

## Create a new WAF rule
To create a new WAF rule, you have to add a new `resource` to the `waf_rules.tf` file.

```hcl
resource "myrasec_waf_rule" "waf" {
  subdomain_name = myrasec_dns_record.www_example_com.name
  name = "Block by URL"
  description = "This WAF rule will block URLs containing blockme"
  log_identifier = "BLOCK_BY_URL"
  direction = "in"
  sort = 1
  process_next = false
  enabled = true
  conditions {
      matching_type = "IREGEX"
      name = "url"
      value = "blockme"
  }
  actions {
    name = "Block"
    type = "block"
  }
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

  # myrasec_waf_rule.waf will be created
  + resource "myrasec_waf_rule" "waf" {
      + created        = (known after apply)
      + description    = "This WAF rule will block URLs containing blockme"
      + direction      = "in"
      + enabled        = true
      + id             = (known after apply)
      + log_identifier = "BLOCK_BY_URL"
      + modified       = (known after apply)
      + name           = "Block by URL"
      + process_next   = false
      + rule_id        = (known after apply)
      + rule_type      = (known after apply)
      + sort           = 1
      + subdomain_name = "www.example.com"
      + sync           = false
      + template       = false

      + actions {
          + action_id = (known after apply)
          + created   = (known after apply)
          + modified  = (known after apply)
          + name      = "Block"
          + type      = "block"
        }

      + conditions {
          + available_phases    = (known after apply)
          + condition_id        = (known after apply)
          + created             = (known after apply)
          + force_custom_values = (known after apply)
          + matching_type       = "IREGEX"
          + modified            = (known after apply)
          + name                = "url"
          + value               = "blockme"
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

After checking this information, please run `terraform apply`.

Next steps:  
[Manage cache settings](./cache_settings.md)  
[Manage IP filters](./ip_filters.md)  
[Manage rate limits](./ratelimits.md)  
[Manage settings](./settings.md)  
[Manage redirects](./redirects.md)  
[Manage SSL certificates](./ssl_certificates.md)  

### Links
[WAF rule resource documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/resources/waf_rule)  
[WAF actions data source documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/data-sources/waf_actions)  
[WAF conditions data source documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/data-sources/waf_conditions)