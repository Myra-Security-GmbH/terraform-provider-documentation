# Manage SSL certificates

Using the Myra Security Terraform Provider it is easy to manage (create, read, update and delete) SSL certificates.

To have a better structure in your Terraform project, create a new file `ssl_certificates.tf`.

## Prerequisite

To manage SSL certificates, you need a domain and a subdomain (DNS record). You can create a new domain and DNS record, import existing ones or load existing ones as a data source as described in the [Manage domains documentation](./domains.md) and [Manage DNS records documentation](./dns_records.md).  

## Create a new SSL certificate
To create a new SSL certificate, you have to add a new `resource` to the `ssl_certificates.tf` file.
```hcl
resource "myrasec_ssl_certificate" "www_example_com" {
  domain_name = myrasec_domain.example_com.name
  subdomains = [
    myrasec_dns_record.www_example_com.name
  ]
  cert_refresh_forced = true
  cert_to_refresh = 0

  certificate = <<EOT
-----BEGIN CERTIFICATE-----
MIIFGDCC...................
...........................
...........................
..............XJrj3q
-----END CERTIFICATE-----
EOT

  key = <<EOF
-----BEGIN PRIVATE KEY-----  
MIIEvwIB...................
...........................
...........................
......ZbLiewuw==
-----END PRIVATE KEY-----
EOF
}
```
**NOTE** Instead of `myrasec_domain.example_com.name` and `myrasec_dns_record.www_example_com.name` you could have used `"example.com"` and `"www.example.com"`, too. `myrasec_dns_record.www_example_com.name` will point to the existing DNS record resource with the (resource) name `www_example_com`. 

After saving the file, you can run `terraform plan` to see what Terraform will do:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # myrasec_ssl_certificate.www_example_com will be created
  + resource "myrasec_ssl_certificate" "www_example_com" {
      + algorithm            = (known after apply)
      + cert_refresh_forced  = true
      + cert_to_refresh      = 0
      + certificate          = <<-EOT
            -----BEGIN CERTIFICATE-----
            MIIFGDCC...................
            ...........................
            ...........................
            ..............XJrj3q
            -----END CERTIFICATE-----
        EOT
      + certificate_id       = (known after apply)
      + created              = (known after apply)
      + domain_name          = "example.com"
      + extended_validation  = (known after apply)
      + fingerprint          = (known after apply)
      + id                   = (known after apply)
      + key                  = <<-EOT
            -----BEGIN PRIVATE KEY-----  
            MIIEvwIB...................
            ...........................
            ...........................
            ......ZbLiewuw==
            -----END PRIVATE KEY-----
        EOT
      + modified             = (known after apply)
      + serial_number        = (known after apply)
      + subdomains           = [
          + "www.example.com",
        ]
      + subject              = (known after apply)
      + subject_alternatives = (known after apply)
      + valid_from           = (known after apply)
      + valid_to             = (known after apply)
      + wildcard             = (known after apply)
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

  # myrasec_ssl_certificate.www_example_com will be created
  + resource "myrasec_ssl_certificate" "www_example_com" {
      + algorithm            = (known after apply)
      + cert_refresh_forced  = true
      + cert_to_refresh      = 0
      + certificate          = <<-EOT
            -----BEGIN CERTIFICATE-----
            MIIFGDCC...................
            ...........................
            ...........................
            ..............XJrj3q
            -----END CERTIFICATE-----
        EOT
      + certificate_id       = (known after apply)
      + created              = (known after apply)
      + domain_name          = "example.com"
      + extended_validation  = (known after apply)
      + fingerprint          = (known after apply)
      + id                   = (known after apply)
      + key                  = <<-EOT
            -----BEGIN PRIVATE KEY-----  
            MIIEvwIB...................
            ...........................
            ...........................
            ......ZbLiewuw==
            -----END PRIVATE KEY-----
        EOT
      + modified             = (known after apply)
      + serial_number        = (known after apply)
      + subdomains           = [
          + "www.example.com",
        ]
      + subject              = (known after apply)
      + subject_alternatives = (known after apply)
      + valid_from           = (known after apply)
      + valid_to             = (known after apply)
      + wildcard             = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
myrasec_ssl_certificate.www_example_com: Creating...
myrasec_ssl_certificate.www_example_com: Creation complete after 2s [id=0000000]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```
If you login to the Myra application, you can see the new created SSL certificate for this domain, assigned to the subdomain.


## Read a SSL certificate
Terraform has resources and data sources. To only fetch information about a SSL certificate (but not changing any information about this filter), you can define a data source and load the IP filters to your `tfstate`.

To read/"import" all your SSL certificates as a data source, create a data source definition in your `ssl_certificates.tf` file like this:
```hcl
data "myrasec_ssl_certificates" "example_com" {
    filter{
        domain_name = myrasec_domain.example_com.name
    }
}
```

Running `terraform apply` will add the data to your `tfstate`.

Data sources in the `tfstate` have the mode `data` whereas resources have the mode `managed`

Next steps:  
[Manage cache settings](./cache_settings.md)  
[Manage IP filters](./ip_filters.md)  
[Manage rate limits](./ratelimits.md)  
[Manage settings](./settings.md)  
[Manage redirects](./redirects.md)  
[Manage WAF rules](./waf_rules.md)  
[Manage error pages](./error_pages.md)    

### Links
[SSL certificate resource documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/resources/ssl_certificate)  
[SSL certificates data source documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/data-sources/ssl_certificates)