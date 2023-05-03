# Manage error pages

Using the Myra Security Terraform Provider it is easy to manage (create, read, update and delete) error pages.

To have a better structure in your Terraform project, please create a new file `error_pages.tf`.

## Prerequisite

To manage error pages, you need a domain and a subdomain (DNS record). You can create a new domain and DNS record, import existing ones or load existing ones as a data source as described in the [Manage domains documentation](./domains.md) and [Manage DNS records documentation](./dns_records.md).

## Create a new error page
To create a new error page, you have to add a new `resource` to the `error_pages.tf` file.
```hcl
resource "myrasec_error_page" "500" {
  subdomain_name = myrasec_dns_record.www_example_com.name
  error_code = 500
  content = "<html><head><title>Error 500</title></head><body><h1>Error 500</h1></body></html>"
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

  # myrasec_error_page.500 will be created
  + resource "myrasec_error_page" "500" {
      + created        = (known after apply)
      + content        = "<html><head><title>Error 500</title></head><body><h1>Error 500</h1></body></html>"
      + error_code     = 500
      + id             = (known after apply)
      + modified       = (known after apply)
      + subdomain_name = "www.example.com"
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

  # myrasec_error_page.500 will be created
  + resource "myrasec_error_page" "500" {
    + created        = (known after apply)
    + content        = "<html><head><title>Error 500</title></head><body><h1>Error 500</h1></body></html>"
    + error_code     = 500
    + id             = (known after apply)
    + modified       = (known after apply)
    + subdomain_name = "www.example.com"
  }

Plan: 1 to add, 0 to change, 0 to destroy.
myrasec_error_page.500: Creating...
myrasec_error_page.500: Creation complete after 4s [id=0000000]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

If you login to the Myra application, you can see the new created error page for this subdomain.

## Update an error page
To update an error page, you have to change the values in the resource definition in your `.tf` file.
As an example, you can modify the `content`. Please update the resource definition in the `error_pages.tf` file to look like this:
```hcl
resource "myrasec_error_page" "500" {
  subdomain_name = myrasec_dns_record.www_example_com.name
  error_code = 500
  content = "<html><head><title>Error 500</title></head><body><h1>Something went wrong</h1></body></html>"
}
```

Executing `terraform plan` to check the changes, should output something similar to this:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_error_page.500: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # myrasec_error_page.500 will be updated in-place
  ~ resource "myrasec_error_page" "500" {
        id             = "0000000"
      ~ content        = "<html><head><title>Error 500</title></head><body><h1>Error 500</h1></body></html>" -> "<html><head><title>Error 500</title></head><body><h1>Something went wrong</h1></body></html>"
    }

Plan: 0 to add, 1 to change, 0 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

After validating the changes, please apply those changes using the `terraform apply` command.
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_error_page.500: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # myrasec_error_page.500 will be updated in-place
  ~ resource "myrasec_error_page" "500" {
        id             = "0000000"
      ~ content        = "<html><head><title>Error 500</title></head><body><h1>Error 500</h1></body></html>" -> "<html><head><title>Error 500</title></head><body><h1>Something went wrong</h1></body></html>"
    }

Plan: 0 to add, 1 to change, 0 to destroy.
myrasec_error_page.500: Modifying... [id=0000000]
myrasec_error_page.500: Modifications complete after 5s [id=0000000]

Apply complete! Resources: 0 added, 1 changed, 0 destroyed.
```

When you check the changes in the Myra application, you should see the updated `content` value in the error pages overview page of the subdomain.

You can check the data in the `tfstate` file, too.


## Delete an error page
Deleting an error page is done by deleting the resource (a managed resource) for this record from the `.tf` file.
When you remove the error page from your `error_pages.tf` file again (so the file is empty) and run `terraform plan`, you should get some output like this:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_error_page.500: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_error_page.500 will be destroyed
  # (because myrasec_error_page.500 is not in configuration)
  - resource "myrasec_error_page" "500" {
      - created        = "2022-03-25T08:27:27+01:00"
      - id             = "0000000"
      - modified       = "2022-02-17T12:41:19+01:00"
      - subdomain_name = "www.example.com."
      - error_code     = 500
      - content        = "<html><head><title>Error 500</title></head><body><h1>Something went wrong</h1></body></html>"
    }

Plan: 0 to add, 0 to change, 1 to destroy.

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if you run "terraform apply" now.
```

Running `terraform apply` will remove this error page from the Myra application:
```
myrasec_domain.example_com: Refreshing state... [id=0000000]
myrasec_dns_record.www_example_com: Refreshing state... [id=0000000]
myrasec_error_page.500: Refreshing state... [id=0000000]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_error_page.500 will be destroyed
  # (because myrasec_error_page.500 is not in configuration)
  - resource "myrasec_error_page" "500" {
      - created        = "2022-03-25T08:27:27+01:00"
      - id             = "0000000"
      - modified       = "2022-02-17T12:41:19+01:00"
      - subdomain_name = "www.example.com."
      - error_code     = 500
      - content        = "<html><head><title>Error 500</title></head><body><h1>Something went wrong</h1></body></html>"
    }

Plan: 0 to add, 0 to change, 1 to destroy.
myrasec_error_page.500: Destroying... [id=0000000]
myrasec_error_page.500: Destruction complete after 7s

Apply complete! Resources: 0 added, 0 changed, 1 destroyed.
```

## Importing an existing error page
If you want to manage an error page that is already known by Myra (already created in the app or using the API), you can `import` this error page to your Terraform project.
To import an existing error page, you have to create a resource for this error page in your `error_pages.tf` file.

Re-create an error page in the Myra app, so you can import it.

```hcl
resource "myrasec_error_page" "www_example_com_500" {

}
```

Importing an error page to your Terraform project requires the subdomain name and the error code of the error page you want to import.

Running `terraform import myrasec_error_page.www_example_com_500 www.example.com:500` will fetch the error page for the `500` error code, assigned to the subdomain with the name `www.example.com` from the Myra application and import it to your Terraform project (managed by the resource that you defined).

After importing this error page, you have to add the missing information to your resource, so that your infrastructure matches your configuration.

```hcl
resource "myrasec_error_page" "www_example_com_500" {
    subdomain_name = myrasec_dns_record.www_example_com.name
    error_code = 500
    content = "<html><head><title>Error 500</title></head><body><h1>Error 500</h1></body></html>"
}
```


## Read an error page
Terraform has resources and data sources. To only fetch information about a error page (but not changing any information about this error page), you can define a data source and load the error page to your `tfstate`.

To read/"import" all your error pages as a data source, please create a data source definition in your `error_pages.tf` file like this:
```hcl
data "myrasec_error_pages" "all" {
  filter {
      domain_name = myrasec_domain.example_com.name
  }
}
```
Running `terraform apply` will add the data to your `tfstate`.

Data sources in the `tfstate` have the mode `data` whereas resources have the mode `managed`.

Next steps:
[Manage cache settings](./cache_settings.md)
[Manage IP filters](./ip_filters.md)
[Manage settings](./settings.md)
[Manage WAF rules](./waf_rules.md)
[Manage SSL certificates](./ssl_certificates.md)

### Links
[Redirect resource documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/resources/redirect)
[Redirect setting data source documentation](https://registry.terraform.io/providers/Myra-Security-GmbH/myrasec/latest/docs/data-sources/redirects)