



**Terraform Registry:**

https://registry.terraform.io/providers/kreuzwerker/docker/latest


Below error when the docker was not installed

[root@ip-x.x.x.x]# terraform plan

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
