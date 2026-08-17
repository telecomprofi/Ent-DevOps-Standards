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
    CostCentre  = "tech-services"
    Project     = "CompName-platform"
    Owner       = "platform-services"
    Team        = "platform-services" # for DataDog Security center
    Service     = "sonarqube" # for DataDog Security center
    Env         = "uat"  # for DataDog Security center
    Environment = "staging"
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

