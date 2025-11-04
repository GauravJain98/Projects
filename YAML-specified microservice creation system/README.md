# YAML-specified microservice creation system

## Purpose

Each microservice creation required around 4 Terraform workspaces to be updated with the required configuration which was tedious and error-prone.
In order to simplify it I created 2 modules:

- 1 for environment-specific resources, such as image registry, S3 buckets, etc.
- 1 for global resources, such as DNS, GitHub, etc.

A large map was used for the configuration of microservices, with the key being the service name.
Initially it was doable, but as the number of services increased to 30, the `config.tf` was over 300 lines.

So I asked Claude to restructure the map to handle this line of code, converting each key to a file with the name key.yaml and setting defaults for each.

```HCL
  applications = {
    for file in fileset("../applications", "*.yaml") :
    trimsuffix(file, ".yaml") => yamldecode(file("../applications/${file}"))
  }
```

Due to this, getting the configuration of any service was a shortcut away as well as creating a new service as simple as creating a new file.

Also, this helped reduce the time required to spin up a new microservice from hours to 30 mins.
