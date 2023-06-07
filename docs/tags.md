# Manage tags

Using the Myra Security Terraform Provider it is easy to manage tags.

To have a better structure in your Terraform project, create a new file `tags.tf`.

## Create a new tag
To create a new tag, you have to add a new `resource` to the `tags.tf` file.

```hcl
resource "myrasec_tag" "example_tag" {
    name = "example tag"
    type = "WAF"
    assignments {
        type = "DOMAIN"
        title = "example.com"
        subdomain_name = "example.com"
    }
}
```
Available types for the tag are:
* [CACHE](./docs/tag_cache_settings.md)
* [SETTINGS](./docs/tag_settings.md)
* [WAF](./docs/tag_waf_rules.md)

Available types for the assignments are, **it's not possible to mix the types in one tag**
* DOMAIN
* SUBDOMAIN

After saving the file, you can run `terraform plan` to see what Terraform will do:
```
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # myrasec_tag.example_tag will be created
  + resource "myrasec_tag" "example_tag" {
      + created  = (known after apply)
      + id       = (known after apply)
      + modified = (known after apply)
      + name     = "example tag"
      + tag_id   = (known after apply)
      + type     = "WAF"

      + assignments {
          + created        = (known after apply)
          + id             = (known after apply)
          + modified       = (known after apply)
          + subdomain_name = "example.com"
          + title          = "example.com"
          + type           = "DOMAIN"
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```
After checking this information, please run `terraform apply`.
```
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # myrasec_tag.example_tag will be created
  + resource "myrasec_tag" "example_tag" {
      + created  = (known after apply)
      + id       = (known after apply)
      + modified = (known after apply)
      + name     = "example tag"
      + tag_id   = (known after apply)
      + type     = "WAF"

      + assignments {
          + created        = (known after apply)
          + id             = (known after apply)
          + modified       = (known after apply)
          + subdomain_name = "example.com"
          + title          = "example.com"
          + type           = "DOMAIN"
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.
myrasec_tag.example_tag: Creating...
myrasec_tag.example_tag: Creation complete after 1s [id=1000460]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```
If you login to the Myra application, you can see the new created tag

### Update a tag
To update a tag, you have to change the values in the resource definition in your `.tf` file. 

On tags you can only update the name, if you change the type, this will force to delete the old tag and create a new one.

When you change the assignments, it will also delete the old assignment and create a new one, but the tag will be the same.

```hcl
resource "myrasec_tag" "example_tag" {
    name = "updated example tag"
    type = "WAF"
    assignments {
        type = "SUBDOMAIN"
        title = "www.example.com"
        subdomain_name = "www.example.com"
    }
}
```
Executing `terraform plan` to check the changes, should output something similar to this:
```
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # myrasec_tag.example_tag will be updated in-place
  ~ resource "myrasec_tag" "example_tag" {
        id       = "0000000"
      ~ name     = "example tag" -> "updated example tag"
        # (4 unchanged attributes hidden)

      - assignments {
          - created        = "2023-05-03T11:32:10+02:00" -> null
          - id             = 0000000 -> null
          - modified       = "2023-05-03T11:32:10+02:00" -> null
          - subdomain_name = "example.com" -> null
          - title          = "example.com" -> null
          - type           = "DOMAIN" -> null
        }
      + assignments {
          + created        = (known after apply)
          + id             = (known after apply)
          + modified       = (known after apply)
          + subdomain_name = "www.example.com"
          + title          = "www.example.com"
          + type           = "SUBDOMAIN"
        }
    }

Plan: 0 to add, 1 to change, 0 to destroy.
```
After validating those changes you can use `terraform apply` to apply the changes.

## Delete a tag
Deleting a tag is done by deleting the resource from the .tf file. 
When you run `terraform plan`, you should get some output like this:
```
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  # myrasec_tag.example_tag will be destroyed
  # (because myrasec_tag.example_tag is not in configuration)
  - resource "myrasec_tag" "example_tag" {
      - created  = "2023-05-03T11:31:11+02:00" -> null
      - id       = "0000000" -> null
      - modified = "2023-05-03T11:47:35+02:00" -> null
      - name     = "updated example tag" -> null
      - tag_id   = 0000000 -> null
      - type     = "WAF" -> null

      - assignments {
          - created        = "2023-05-03T11:47:35+02:00" -> null
          - id             = 0000000 -> null
          - modified       = "2023-05-03T11:47:35+02:00" -> null
          - subdomain_name = "www.example.com." -> null
          - title          = "www.example.com" -> null
          - type           = "SUBDOMAIN" -> null
        }
    }

Plan: 0 to add, 0 to change, 1 to destroy.
```

## Import Tag
If you want to manage a tag that is already known by Myra (already created in the app or using the API), you can `import` this tag to your terraform project. To import an existing tag, you have to create a resource for this tag in your `tags.tf` file.
```hcl
resource "myrasec_tag" "tag_import" {

}
```
Importing a tag to your terraform project requires the id of the tag you want to import.

Running `terraform import myrasec_tag.tag_import 0000000` will fetch the tag from Myra application and import it to your terraform project.

**You can get the tag id from the url in Myra application, or you can use the a data source described in the next section***

After importing this tag, you have to add the missing information to your resource, so that your infrastructure matches your configuration.
```hcl
resource "myrasec_tag" "tag_import" {
    name = "imported tag"
    type = "WAF"
    assignments {
        type = "DOMAIN"
        title = "example.com"
        subdomain_name = "example.com"
    }
}
```

## Read tags
Terraform has resources and data sources. To only fetch information about a tag (but not changing any information about it), you can define a data source and load the redirect to your `tfstate`.
```hcl
// all tags
data "myrasec_tags" "all_tags" {
    filter {
    }
}

// specific tag by it's name
data "myrasec_tags" "tag_data" {
    filter {
        name = "tag name"
    }
}
```
Running `terraform apply` will add the data to your `tfstate`.
