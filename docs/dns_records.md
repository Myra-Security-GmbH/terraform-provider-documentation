# Manage DNS records

Using the Myra Security Terraform Provider it is easy to manage (create, read, update and delete) DNS records.

To have a better structure in your Terraform project, please create a new file `dns_records.tf`.

## Prerequisite

To manage DNS records, you need a domain. You can create a new domain, import an existing one or load an existing one as a data source as described in the [Manage domains documentation](./domains.md).  

## Create a new DNS record
To create a new DNS record, you have to add a new `resource` to the `dns_records.tf` file.
```hcl
resource "myrasec_dns_record" "www_example_com" {
    domain_name = myrasec_domain.example_com.name
    name = "www.example.com"
    value = "192.168.0.1"
    record_type = "A"
    ttl = 300
}
```

**NOTE** Instead of `myrasec_domain.example_com.name` you could have used `"example.com"`. `myrasec_domain.example_com.name` will point to the existing domain resource with the (resource) name `example_com`.  

After saving the file, you can run `terraform plan` to see what Terraform will do:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # myrasec_dns_record.www_example_com will be created
  + resource "myrasec_dns_record" "www_example_com" {
      + active            = true
      + alternative_cname = (known after apply)
      + created           = (known after apply)
      + domain_name       = "example.com"
      + enabled           = true
      + id                = (known after apply)
      + modified          = (known after apply)
      + name              = "www.example.com"
      + record_id         = (known after apply)
      + record_type       = "A"
      + ttl               = 300
      + value             = "192.168.0.1"

      + upstream_options {
          + backup       = (known after apply)
          + created      = (known after apply)
          + down         = (known after apply)
          + fail_timeout = (known after apply)
          + max_fails    = (known after apply)
          + modified     = (known after apply)
          + upstream_id  = (known after apply)
          + weight       = (known after apply)
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

After checking this information, please run `terraform apply`.
```
myrasec_domain.example_com: Refreshing state... [id=0000000]                                                                                                                        
                                                                                                                                                                                    
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:                                          
  + create                                                                                                                                                                          
                                                                                                                                                                                    
Terraform will perform the following actions:                                                                                                                                       
                                                                                                                                                                                    
  # myrasec_dns_record.www_example_com will be created                                                                                                                              
  + resource "myrasec_dns_record" "www_example_com" {                                                                                                                               
      + active            = true                                                                                                                                                    
      + alternative_cname = (known after apply)                                                                                                                                     
      + created           = (known after apply)                                                                                                                                     
      + domain_name       = "example.com"                                                                                                                                           
      + enabled           = true                                                                                                                                                    
      + id                = (known after apply)                                                                                                                                     
      + modified          = (known after apply)                                                                                                                                     
      + name              = "www.example.com"                                                                                                                                       
      + record_id         = (known after apply)                                                                                                                                     
      + record_type       = "A"                                                                                                                                                     
      + ttl               = 300                                                                                                                                                     
      + value             = "192.168.0.1"                                                                                                                                           
                                                                                                                                                                                    
      + upstream_options {                                                                                                                                                          
          + backup       = (known after apply)                                                                                                                                      
          + created      = (known after apply)                                                                                                                                      
          + down         = (known after apply)                                                                                                                                      
          + fail_timeout = (known after apply)                                                                                                                                      
          + max_fails    = (known after apply)                                                                                                                                      
          + modified     = (known after apply)                                                                                                                                      
          + upstream_id  = (known after apply)                                                                                                                                      
          + weight       = (known after apply)                                                                                                                                      
        }                                                                                                                                                                           
    }                                                                                                                                                                               
                                                                                                                                                                                    
Plan: 1 to add, 0 to change, 0 to destroy.                                                                                                                                          
myrasec_dns_record.www_example_com: Creating...                                                                                                                                     
myrasec_dns_record.www_example_com: Creation complete after 2s [id=000000]                                                                                                         
                                                                                                                                                                                    
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```
If you login to the Myra application, you can see the new created DNS record / subdomain.

## Update a DNS record
To update a DNS record, you have to change the values in the resource definition in your `.tf` file.
As an example, you can change the TTL from `300` to `600`. Update the resource definition in the `dns_records.tf` file to look like this:
```hcl
resource "myrasec_dns_record" "www_example_com" {
    domain_name = myrasec_domain.example_com.name
    name = "www.example.com"
    value = "192.168.0.1"
    record_type = "A"
    ttl = 600
}
```

Executing `terraform plan` to check the changes, should output something similar to this:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # myrasec_dns_record.www_example_com will be updated in-place
  ~ resource "myrasec_dns_record" "www_example_com" {
        id                = "0000000"
        name              = "www.example.com"
      ~ ttl               = 300 -> 600
        # (11 unchanged attributes hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

After validating the changes, please apply those changes using the `terraform apply` command.
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # myrasec_dns_record.www_example_com will be updated in-place
  ~ resource "myrasec_dns_record" "www_example_com" {
        id                = "0000000"
        name              = "www.example.com"
      ~ ttl               = 300 -> 600
        # (11 unchanged attributes hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.
myrasec_dns_record.www_example_com: Modifying... [id=0000000]
myrasec_dns_record.www_example_com: Modifications complete after 2s [id=0000000]

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.
```
When you check the changes in the Myra application, you should see the updated TTL value in the DNS records overview page.

You can check the data in the `tfstate` file, too. 

## Delete a DNS record
Deleting a DNS record is done by deleting the resource (a managed resource) for this record from the `.tf` file.  
When you remove the DNS record from your `dns_records.tf` file again (so the file is empty) and run `terraform plan`, you should get some output like this:

```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_dns_record.www_example_com will be destroyed
  # (because myrasec_dns_record.www_example_com is not in configuration)
  - resource "myrasec_dns_record" "www_example_com" {
      - active            = true -> null
      - alternative_cname = "www-example-com.ax4z.com." -> null
      - created           = "2022-02-16T13:07:01+01:00" -> null
      - domain_name       = "example.com" -> null
      - enabled           = true -> null
      - id                = "0000000" -> null
      - modified          = "2022-02-16T13:07:01+01:00" -> null
      - name              = "www.example.com" -> null
      - port              = 0 -> null
      - priority          = 0 -> null
      - record_id         = 0000000 -> null
      - record_type       = "A" -> null
      - ttl               = 600 -> null
      - value             = "192.168.0.1" -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

Running `terraform apply` will remove this DNS record from the Myra application:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_dns_record.www_example_com will be destroyed
  # (because myrasec_dns_record.www_example_com is not in configuration)
  - resource "myrasec_dns_record" "www_example_com" {
      - active            = true -> null
      - alternative_cname = "www-example-com.ax4z.com." -> null
      - created           = "2022-02-16T13:07:01+01:00" -> null
      - domain_name       = "example.com" -> null
      - enabled           = true -> null
      - id                = "0000000" -> null
      - modified          = "2022-02-16T13:07:01+01:00" -> null
      - name              = "www.example.com" -> null
      - port              = 0 -> null
      - priority          = 0 -> null
      - record_id         = 0000000 -> null
      - record_type       = "A" -> null
      - ttl               = 600 -> null
      - value             = "192.168.0.1" -> null
    }

Plan: 0 to add, 0 to change, 1 to destroy.
myrasec_dns_record.www_example_com: Destroying... [id=0000000]
myrasec_dns_record.www_example_com: Destruction complete after 6s

Apply complete! Resources: 0 added, 0 changed, 1 destroyed.
```

## Importing an existing DNS record
If you want to manage a DNS record that is already known by Myra (already created in the app or using the API), you can `import` this DNS record to your Terraform project.  
To import an existing DNS record, you have to create a resource for this DNS record in your `dns_records.tf` file.

Re-create a DNS record in the Myra app, so you can import it.

```hcl
resource "myrasec_dns_record" "www_example_com" {
    domain_name = myrasec_domain.example_com.name
}
```
Importing an element to your Terraform project requires the ID of the element you want to import. In this case, you need the ID of the existing DNS record you want to import.  
In most cases, it is required to add the domain name or subdomain name in front of the ID (name:ID).

Running `terraform import myrasec_dns_record.www_example_com example.com:0000000` will fetch the DNS record with ID = `0000000` on the domain with the name `example.com` from the Myra application and import it to your Terraform project (managed by the resource that you defined).

```
myrasec_dns_record.www_example_com: Importing from ID "example.com:0000000"...
myrasec_dns_record.www_example_com: Import prepared!
  Prepared myrasec_dns_record for import
myrasec_dns_record.www_example_com: Refreshing state... [id=example.com:0000000]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.
```

After importing this DNS record, you have to add the missing information to your resource, so that your infrastructure matches your configuration.

```hcl
resource "myrasec_dns_record" "www_example_com" {
    domain_name = myrasec_domain.example_com.name
    value = "192.168.0.1"
    record_type = "A"
    ttl = 300
}
```

## Read a DNS record
Terraform has resources and data sources. To only fetch information about a DNS record (but not changing any information about this record), you can define a data source and load the DNS records to your `tfstate`.

To read/"import" all your DNS records as a data source, please create a data source definition in your `dns_records.tf` file like this:
```hcl
data "myrasec_dns_records" "all" {
    filter{
        domain_name = myrasec_domain.example_com.name
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
[Manage redirects](./redirects.md)  
[Manage WAF rules](./waf_rules.md)  
[Manage SSL certificates](./ssl_certificates.md)  


### Links
[DNS record resource documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/resources/dns_record)  
[DNS record data source documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/data-sources/dns_records)