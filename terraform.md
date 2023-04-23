



**Terraform Registry:**

https://registry.terraform.io/providers/kreuzwerker/docker/latest


Below error when the docker was not installed

[root@ip-x-x-x-x]# terraform plan

Planning failed. Terraform encountered an error while generating this plan.

╷
│ Error: Invalid provider configuration
│ 
│ Provider "registry.terraform.io/kreuzwerker/docker" requires explicit configuration. Add a provider block to the root module and configure the provider's required arguments as described in the provider
│ documentation.
│ 
╵
╷
│ Error: Error pinging Docker server: Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
│ 
│   with provider["registry.terraform.io/kreuzwerker/docker"],
│   on <empty> line 0:
│   (source code not available)
│ 
╵

  
  Then installed docker and terraform plan executed successfully, but terraform apply failed with below error:
  
  [root@ip-x-x-x-x]# terraform apply

 docker_image.nginx: Refreshing state... [id=sha256:080ed0ed8312deca92e9a769b518cdfa20f5278359bd156f3469dd8fa532db6bnginx:latest]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  docker_container.container will be created
  + resource "docker_container" "container" {
      + attach                                      = false
      + bridge                                      = (known after apply)
      + command                                     = (known after apply)
      + container_logs                              = (known after apply)
      + container_read_refresh_timeout_milliseconds = 15000
      + entrypoint                                  = (known after apply)
      + env                                         = (known after apply)
      + exit_code                                   = (known after apply)
      + hostname                                    = (known after apply)
      + id                                          = (known after apply)
      + image                                       = "docker_image.nginx.image_id"
      + init                                        = (known after apply)
      + ipc_mode                                    = (known after apply)
      + log_driver                                  = (known after apply)
      + logs                                        = false
      + must_run                                    = true
      + name                                        = "nginx-container"
      + network_data                                = (known after apply)
      + read_only                                   = false
      + remove_volumes                              = true
      + restart                                     = "no"
      + rm                                          = false
      + runtime                                     = (known after apply)
      + security_opts                               = (known after apply)
      + shm_size                                    = (known after apply)
      + start                                       = true
      + stdin_open                                  = false
      + stop_signal                                 = (known after apply)
      + stop_timeout                                = (known after apply)
      + tty                                         = false
      + wait                                        = false
      + wait_timeout                                = 60

      + ports {
          + external = 80
          + internal = 80
          + ip       = "0.0.0.0"
          + protocol = "tcp"
        }
    }

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

docker_container.container: Creating...
╷
│ Error: Unable to create container with image docker_image.nginx.image_id: unable to pull image docker_image.nginx.image_id: error pulling image docker_image.nginx.image_id: Error response from daemon: pull access denied for docker_image.nginx.image_id, repository does not exist or may require 'docker login': denied: requested access to the resource is denied
│ 
│   with docker_container.container,
│   on ngnix-instance.tf line 18, in resource "docker_container" "container":
│   18: resource "docker_container" "container" {
  
  
  
  
  Above error got solved by changing the below code 
  
  From
  
  # cat ngnix-instance.tf 
terraform {

   required_providers {
    docker = {
     source = "kreuzwerker/docker"
     version = "3.0.2"   <<< with this docker provider version
}
}
}

provider "docker" {}

resource "docker_image" "nginx"  {
  name = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "container" {
  image = "docker_image.nginx.latest"          <<< ====When using this it was throwing above error as it was written in double quotes
  
  name = "nginx-container"
  
  ports {
     internal = 80
     external = 80
}
}
  
  
To:
  
  # cat ngnix-instance.tf 
terraform {

   required_providers {
    docker = {
     source = "kreuzwerker/docker"
     version = "3.0.2"       <<< = When using latest docker provider
}
}
}

provider "docker" {}

resource "docker_image" "nginx"  {
  name = "nginx:latest"
  keep_locally = false
}

resource "docker_container" "container" {
  image = docker_image.nginx.image_id      <<<<= changing to this it executed successfully. As the "latest tag" was deprecated and removed in latest docker provided version so it will not work we need to give image_id instead of it.
  
  name = "nginx-container"
  
  ports {
     internal = 80
     external = 80
}
}


 ==========                                                      
 root@ip-x-x-x-x]# terraform apply
╷
│ Error: Unsupported attribute
│ 
│   on ngnix-instance.tf line 19, in resource "docker_container" "container":
│   19:   image = docker_image.nginx.latest
│ 
│ This object has no argument, nested block, or exported attribute named "latest".
==========

                                                       
Reference:
                                                       
https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/container
https://github.com/kreuzwerker/terraform-provider-docker
https://dev.to/af/hashicorp-configuration-language-hcl-blocks-5627
                                                       
                                                       
** Terraform Meta Argument **
                                                       
Was getting below error while using for_each meta arument.
The "count" and "for_each" meta-arguments are mutually-exclusive, only one should be used to be explicit about the number of resources to be created.  
 
On checking found that I had used the count meta argument along with for_each due to which it was throwing the above error, as below:
                                                       
terraform {

required_providers {
  aws = {
   version = "~>4.16"
   source = "hashicorp/aws"

}

}
}

provider "aws" {
 region = "ap-northeast-1"
 
}

resource "aws_instance" "instance_meta" {
   for_each = toset(var.instance.name)
   ami = var.instance.ami 
   count = var.instances                                               
   instance_type = var.instance.type
   tags = {
    Name = each.key
}

}                                                       

                                                       
                                                       
# cat variable.tf 
variable "instance" {
 type = object({
   name = list(string)
   instances = number
   type = string
   ami = string

})

default = {
 instances = 2
 type = "t2.micro"
 ami = "ami-01b32aa8589df6208"
 name = ["ec2-meta-instance", "2instance"]


}

}                                                       
                                                       
