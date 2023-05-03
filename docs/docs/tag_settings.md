# Manage tag settings

Using the Myra Security Terraform Provider it is easy to manage (read and update) tag settings.

To have a better structure in your Terraform project, create a new file `tag_settings.tf`.

## Prerequisite

To manage settings, you need a tag. 

## Update tag settings
To update the tag settings on your tag, you have to specify a `myrasec_tag_settings` resource. In this example, we want to update the `only_https` and the `limit_tls_version` settings.
```hcl
resource "myrasec_tag_settings" "tag_settings" {
  tag_id = myrasec_tag.test_tag.id
  only_https = true
  limit_tls_version = [
    "TLSv1.2",
    "TLSv1.3"
  ]
}
```
Executing `terraform plan` to check the changes, should output something similar to this:

```
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create
Terraform will perform the following actions:

  # myrasec_tag_settings.tag_settings will be created
  + resource "myrasec_tag_settings" "tag_settings" {
      + limit_tls_version               = [
          + "TLSv1.2",
          + "TLSv1.3",
        ]
      + only_https                      = true
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

After validating the changes, please apply those changes using the `terraform apply` command.

```
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # myrasec_tag_settings.tag_settings will be created
  + resource "myrasec_tag_settings" "tag_settings" {
      + limit_tls_version               = [
          + "TLSv1.2",
          + "TLSv1.3",
        ]
      + only_https                      = true
    }

Plan: 1 to add, 0 to change, 0 to destroy.
myrasec_tag_settings.tag_settings: Creating...
myrasec_tag_settings.tag_settings: Creation complete after 3s [id=000000000]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```
