# insert your company's IaC standard below

## Mandatory Resource tagging
Make sure you tag resources you create with the minimum set of mandatory tags like below
make sure tags exist, values not empty and longer than 3 chars (that’s checked by Open Policy Agent rule when /envs/production/*.tf terraform code is merged into main branch.
```
cat ./variables.tf
variable "common_tags" {
  description = "Common tags to apply to all resources"
  type        = map(string)
  default = {
    Organization = empeek
    CostCentre   = "tech-services"
    Project      = "CompName-platform"
    Owner        = "platform-services"
    Team         = "platform-services" # for DataDog Security center
    Service      = "sonarqube" # for DataDog Security center
    Env          = "uat"  # for DataDog Security center
    Environment  = "staging"
  }
}
```

 
Optional, but good for lower environments to shut-/scale down to save cost over weekends:
"env" = "holiday-pause"
"auto-stop" = "true"
Define them in varaibles.tf, override with terraform.prod.tfvars for other environments


and merge with others in resource code blocks:
#vpc.tf
```

module "vpc" {
 <skipped for brevity>

tags = merge(      

   var.common_tags,
   {
     Description = "VPC for platform"
   }
 )
}
```


Also use repo-specific Tags within providers' block to allow tracking of how resources were created - manually or with terraform code:

```
provider "aws" {
 region = "us-east-1"
 default_tags {
   tags = {
     Terraform  = "true"
     GithubRepo = "CompName-platform/"
     GithubPath = "envs/${basename(path.cwd)}"
   }
 }
}
```
## Mandatory Resource Naming convention

Make sure resources have meaningful names that allow identification of Application/Component name and Environment they represent (in addition to tagging as indicated above)
Cloud resource should contain 
- Short App/Component name (e.g. bckstg-be for Backstage backend container)
- Short Environment name (dev, qa, uat, prod)
- short resource type name (rds-db)
- id number for resources that need more than one instance (e.g. EC2 instances)
divided by skewer-type separators:
bckstg-be-prod-rds-db-001

Total resource name have to be shorter than 47 symbols.

## Use repository's metadata from catalog-info.yaml from root directory

Each IaC repository has in it root folder metadata file where Items like Project(Name), CostCentre, Owner, Team, Service(name) or Application/Component (name), environments (names) could be extracted to populate mandatory tags described above.

## Use directory name and/or .tfvars files to poupulate Environment/Env tag(s)

Each Infrastructure environment directory is either named with Env name (e.g. qa, staging, production) or has corresponding file with variables that contain env name.

## Precedence of metadata sources for tagging
Use following precedence: 
1. Top priority: catalog-info.yaml
2. If catalog-info.yaml has no project/Application/Component name or does not exist, use repository name as Project name
3. Use directory name to populate Environment/Env tag if that conforms to one of the below: production (or prod), staging (or uat), dev (or development), qa (or test). 
4. If directory is named other than variants listed in section 3., use ,tfvars file for env name instead.
5. If .tfvars file is not available use 'uat' for Env tag and 'staging' for Environment tag.

## Prepend Project/Application/Component tag with organization's short name when it is available

1. If Organization name is available in catalog-info.yaml use it to prepend value of Project, and use colon as delimiter: 
e.g. 
```
Project:"empeek:platform"
```

2. If organization name is not available, use GitHub repository slug for org. name
e.g. for 'https://github.com/myenterprise/platform' use 'myenterprise' as short org. name:
```
Project:"myenterprise:platform"
```
