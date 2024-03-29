# Manage settings

Using the Myra Security Terraform Provider it is easy to manage (read and update) subdomain settings.

To have a better structure in your Terraform project, create a new file `settings.tf`.

## Prerequisite

To manage settings, you need a domain and a subdomain (DNS record). You can create a new domain and DNS record, import existing ones or load existing ones as a data source as described in the [Manage domains documentation](./domains.md) and [Manage DNS records documentation](./dns_records.md).


## Update settings
To update the settings on your test subdomain, you have to specify a `myrasec_settings` resource. In this example, we want to update the `only_https` and the `limit_tls_version` settings.
```hcl
resource "myrasec_settings" "settings" {
  subdomain_name = myrasec_dns_record.www_example_com.name
  only_https = true
  limit_tls_version = [
    "TLSv1.2",
    "TLSv1.3"
  ]
}
```
Executing `terraform plan` to check the changes, should output something similar to this:

```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create
Terraform will perform the following actions:

  # myrasec_settings.settings will be created
  + resource "myrasec_settings" "settings" {
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
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # myrasec_settings.settings will be created
  + resource "myrasec_settings" "settings" {
      + limit_tls_version               = [
          + "TLSv1.2",
          + "TLSv1.3",
        ]
      + only_https                      = true
    }

Plan: 1 to add, 0 to change, 0 to destroy.
myrasec_settings.settings: Creating...
myrasec_settings.settings: Creation complete after 3s [id=000000000]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

**NOTE** In this example, there was no existing `myrasec_setting` resource in our state. On the first run, it will load all exising settings to the `tfstate`. When you change another setting, it will show less changes.

Next steps:
[Manage cache settings](./cache_settings.md)
[Manage IP filters](./ip_filters.md)
[Manage redirects](./redirects.md)
[Manage WAF rules](./waf_rules.md)
[Manage SSL certificates](./ssl_certificates.md)
[Manage error pages](./error_pages.md)

### Links
[Settings resource documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/resources/settings)