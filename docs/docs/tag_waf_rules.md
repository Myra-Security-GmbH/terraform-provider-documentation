# Manage tag WAF rules

Using the Myra Security Terraform Provider it is easy to manage (create, read, update and delete) tag WAF rules.

To have a better structure in your Terraform project, create a new file `tag_waf_rules.tf`.

## Prerequisite

To manage tag WAF rules, you need a tag.

As a tag WAF rule has one or more conditions as well as one or more actions, you can load the available actions and conditions using a data provider.

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

## Create a new tag WAF rule
To create a new tag WAF rule, you have to add a new `resource` to the `tag_waf_rules.tf` file.

```hcl
resource "myrasec_tag_waf_rule" "tag_waf" {
  tag_id = myrasec_tag.tag_example.id
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
After saving the file, you can run `terraform plan` to see what Terraform will do:
```
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # myrasec_tag_waf_rule.tag_waf will be created
  + resource "myrasec_tag_waf_rule" "tag_waf" {
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
      + tag_id         = 000000
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
