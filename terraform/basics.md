---
marp: true
theme: gaia
paginate: true
style: @import url('https://unpkg.com/tailwindcss@^2/dist/utilities.min.css');
---

# Getting started with Terraform

An introduction to Terraform

----

## Agenda

<style scoped>
section {
  font-size: 21px;
}
</style>

- Infrastructure As Code
- Terraform vs OpenTofu
- plan and apply
- Providers
- Resources
- Data
- Variables
- Locals
- Loops
- Modules
- State
- Imports
- Functions
- Tips & Tricks

---

## Infrastructure As Code (IAC)

- Automation
- Repeatability
- source control
  - audit
  - code-review
- Following internal conventions and best practices

<!--
IAC IS NICE, especially for one off, documentation isn't good enough.
-->

---

## Terraform vs OpenTofu

- August 10, 2023, HashiCorp [changed](https://www.hashicorp.com/blog/hashicorp-adopts-business-source-license) Terraform License
  - To a none open-source license BSL v1.1
  - Done as a part of terraform v1.6 release
- OpenTofu is a fork that is a part of the Linux Foundation
- Most none vendor companies don't break BSL v1.1 by just using terraform.

<!--
I am no lawyer, do your own research if it's okay for you to use terraform in your own environment.
-->

---

## Plan & apply

<div class="grid grid-cols-2 gap-4">
<div>

- terraform plan
  - View current status
  - See what happens before applying changes
- terraform apply
  - Same as plan
  - Asks you to approve any changes

</div>
<div>

<font size="6">

```shell
Terraform used the selected providers to generate the following execution plan.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # module.foo.google_service_account.service_account will be created
  + resource "google_service_account" "service_account" {
      + account_id   = "hello"
      + disabled     = false
      + display_name = "hello"
      + email        = (known after apply)
      + id           = (known after apply)
      + member       = (known after apply)
      + name         = (known after apply)
      + project      = "mega-project-id"
      + unique_id    = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```

</font>

</div>
</div>

---

## Providers

<div class="grid grid-cols-2 gap-4">
<div>

<font size="6">

- Many providers [available](https://registry.terraform.io/browse/providers) for cloud and SaaS
- Many big providers maintained by Hashicorp under MPL-2.0
  - Google
  - Azure
  - AWS
  - Kubernetes
  - etc.
- Normally written in go using the [terraform plugin framework](https://developer.hashicorp.com/terraform/plugin/framework)
</font>

</div>
<div>

<font size="6">

```hcl
terraform {
  required_providers {
    google = {
      source  = "hashicorp/google"
      version = ">= 5.10.0, < 6"
    }
  }
}

provider "google" {
  region = "eu-west-1"
}
```

</font>

</div>
</div>

---

## Resources

<div class="grid grid-cols-2 gap-4">
<div>

- Used to create a object
- Each resource block describes one or more infrastructure objects
  - vm
  - Kubernetes cluster
  - etc.

</div>
<div>

<font size="6">

```hcl
resource "google_service_account" "this" {
  account_id   = "hello"
  display_name = "hello"
  project      = "mega-project-id"
}
```

</font>

</div>
</div>

---

## Data

<div class="grid grid-cols-2 gap-4">
<div>

- Reads already existing data from your provider

</div>
<div>

<font size="6">

```hcl
data "google_service_account" "this" {
  account_id = "hello"
}
```

</font>

</div>
</div>

---

## Variables

<div class="grid grid-cols-2 gap-4">
<div>

- Input variables
- Defaults
- Types
- Validation
- Objects

</div>
<div>

<font size="6">

```hcl
variable "iam_service_account" {
  type        = string
  description = "The name of the service account to create."
  default     = "hello"
  validation { # optional
    condition     = contains(["hello", "foobar"], var.iam_service_account)
    error_message = "iam_service_account must be hello or foobar"
  }
}

resource "google_service_account" "this" {
  account_id   = var.iam_service_account
  display_name = var.iam_service_account
  project      = mega-project-id
}

variable "vm" {
  description = "VM config"
  type = object({
    name     = string
    shutdown = optional(bool, false)
    networks = optional(list(string), false)
  })
}
```

</font>

</div>
</div>

---

## Locals

<div class="grid grid-cols-2 gap-4">
<div>

- A internal variable

</div>
<div>

<font size="6">

```hcl
locals {
  config = {
    project_id = "mega-project-id"
    region     = "europe-west1"
  }
}

resource "google_service_account" "this" {
  account_id   = "hello"
  display_name = "hello"
  project      = local.config.project_id
}
```

</font>

</div>
</div>

---

## Loops

<div class="grid grid-cols-2 gap-4">
<div>

- No if statements
- `count`
  - Order is important
  - If you change the order, a resource will be recreated
- In most cases use `for_each` instead

</div>
<div>

<font size="6">

```hcl
resource "google_service_account" "this" {
  for_each     = toset(["fooo", "bar", "Alice", "Dottie"])
  account_id   = each.key
  display_name = each.key
  project      = "mega-project-id"
}
```

</font>

</div>
</div>

---

## Modules

<div class="grid grid-cols-2 gap-4">
<div>

- Created by the community but also internally
- Think of it as a class, can create multiple resources
- Call the same module

</div>
<div>

<font size="6">

```hcl
module "terraform-repo" {
  source = "../modules/terraform-repo"

  gcp_sa_email                    = "tf-sa@mega-project-id.iam.gserviceaccount.com"
  github_create_repo              = true
}

```

</font>

</div>
</div>

---

## State

<div class="grid grid-cols-2 gap-4">
<div>

- Terraform logs info about managed resources
- Terraform knows which resource is under it's control
- Should be stored remotely
- A single user can manage the state at the same time.

</div>
<div>

<font size="6">

```hcl
terraform {
  backend "gcs" {
    bucket = "megabucket-2000"
    prefix = "terraform/terraform-domain1"
  }
}
```

</font>

</div>
</div>

---

## Imports

<div class="grid grid-cols-2 gap-4">
<div>

- Import existing resources created outside of terraform
- The name of the resource +
- Read the [docs](https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/google_service_account)

</div>
<div>

<font size="6">

```shell
terraform import google_service_account.default projects/{{project_id}}/serviceAccounts/{{email}}
```

```hcl
resource "google_service_account" "this" {
  account_id   = "hello"
  display_name = "hello"
  project      = "mega-project-id"
}
```

```shell
terraform import google_service_account.this projects/mega-project-id/serviceAccounts/hello@mega-project-id.iam.gserviceaccount.com
```

</font>

</div>
</div>

---

## Functions

- HCL got built in [functions](https://developer.hashicorp.com/terraform/language/functions)
- [toset](https://developer.hashicorp.com/terraform/language/functions/toset)
- [concat](https://developer.hashicorp.com/terraform/language/functions/concat)
- [split](https://developer.hashicorp.com/terraform/language/functions/split)
- etc.

<!--
Look in the docs, it mostly got the function you need
-->

---

## Tips & Tricks

- KISS
- HCL got it's limitations, try to write simple hcl
- Read the provider documentation

<!--
Better to repeat your self then to write something complex
When I try out a new resource I always look at the docs
-->
