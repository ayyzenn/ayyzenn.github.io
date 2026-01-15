---
title: "Terraform: Infrastructure as Code"
date: 2025-12-14
tags: ["terraform", "infrastructure", "cloud", "automation", "IaC"]
---

## What is Terraform?

Think of Terraform as a blueprint for your cloud infrastructure. Instead of clicking buttons in the AWS console, you write simple code that describes what you want (servers, databases, networks, etc.), and Terraform builds it for you. It's like using a recipe instead of cooking from memory—repeatable and consistent every time.

Terraform works with major cloud providers like AWS, Azure, Google Cloud, and many others.

## Why Use Terraform?

- **No More Manual Clicking**: Automate infrastructure setup instead of clicking through AWS
- **Easy Updates**: Change your code and run `terraform apply` to update everything
- **Backup Your Infrastructure**: Keep your setup in version control (like Git)
- **Reuse Everywhere**: Run the same setup in dev, staging, and production without changes
- **Cost Preview**: See what resources cost before you create them
- **Work with Multiple Clouds**: Manage AWS, Azure, and Google Cloud from one tool

## Basic Concepts (Made Simple)

### Providers
A **provider** tells Terraform which cloud service to use. Think of it as picking AWS, Google Cloud, or Azure before you start building.

```hcl
provider "aws" {
  region = "us-east-1"
}
```

That's it! Now Terraform knows: "I'm using AWS, in the us-east-1 region."

### Resources
A **resource** is something you want to create—a server, a database, a network, etc.

```hcl
resource "aws_instance" "my_server" {
  ami           = "ami-0c55b159cbfafe1f0"  # The OS image to use
  instance_type = "t2.micro"               # The size (small, medium, large)

  tags = {
    Name = "MyWebServer"
  }
}
```

In plain English: "Create an AWS EC2 server called 'my_server' using a free-tier compatible image and size it as 't2.micro'."

### Variables
**Variables** make your code reusable. Instead of hardcoding values, you define them once and reuse them.

```hcl
variable "server_count" {
  description = "How many servers do you want?"
  type        = number
  default     = 1
}

resource "aws_instance" "servers" {
  count         = var.server_count
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"
}
```

Now if you change `server_count` to 3, Terraform creates 3 servers instead of 1.

## Common Commands (What They Actually Do)

```bash
terraform init      # Download necessary files (run this first!)
terraform plan      # Show what will happen (no changes yet)
terraform apply     # Actually create/change the resources
terraform destroy   # Delete all the resources (be careful!)
terraform show      # Display current infrastructure state
```

**Example workflow:**
```bash
$ terraform init      # Set up
$ terraform plan      # Preview: "Will create 1 server, 1 database"
$ terraform apply     # Do it: "Server and database created!"
$ terraform destroy   # Clean up: "All resources deleted"
```

## State Management (What's This `.tfstate` File?)

Terraform keeps a file called `terraform.tfstate` that tracks what resources exist. It's like a shopping list of everything Terraform created.

**Keep state files safe:**
- Don't lose them (Terraform won't know what resources exist)
- Don't commit them to Git (they contain sensitive data)
- Back them up to AWS S3 or similar storage

**Simple example - S3 backend:**
```hcl
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "prod/terraform.tfstate"
    region = "us-east-1"
  }
}
```

This tells Terraform: "Save my state file to this S3 bucket instead of locally."

## Working with Docker and Terraform

One of my favorite use cases is combining Terraform with Docker. Here's a real-world example: I want to launch a server on AWS and automatically run my app in a Docker container.

**Step 1: Create a Docker image**
```hcl
resource "aws_ecr_repository" "my_app" {
  name = "my-awesome-app"
}
```

This creates a private Docker registry on AWS where you can store your Docker images.

**Step 2: Launch a server with Docker installed**
```hcl
resource "aws_instance" "web_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.small"

  # Run these commands when the server starts
  user_data = <<-EOF
              #!/bin/bash
              sudo yum update -y
              sudo yum install -y docker
              sudo systemctl start docker
              EOF

  tags = {
    Name = "MyWebServer"
  }
}
```

**What's happening:**
1. Terraform creates an EC2 server
2. When it boots up, it automatically installs Docker
3. You can then push your Docker images and run them

**Example: Running your Docker app**
```bash
# Build and push your app to AWS
docker build -t my-awesome-app .
docker tag my-awesome-app:latest 123456789.dkr.ecr.us-east-1.amazonaws.com/my-awesome-app:latest
docker push 123456789.dkr.ecr.us-east-1.amazonaws.com/my-awesome-app:latest

# SSH into the server and run it
ssh ec2-user@your-server-ip
docker login --username AWS -p $(aws ecr get-login-password) 123456789.dkr.ecr.us-east-1.amazonaws.com
docker run -d -p 80:3000 123456789.dkr.ecr.us-east-1.amazonaws.com/my-awesome-app:latest
```

Now your app is running in a Docker container on AWS!

## Real-World Scenario: Multi-Environment Setup

I organize my Terraform files like this to keep things clean and easy to manage:

```
terraform/
├── main.tf              # Basic setup
├── variables.tf         # Things you can change
├── outputs.tf           # Things to remember after creation
├── networking.tf        # Networks and firewalls
├── servers.tf           # EC2 instances
└── environments/
    ├── dev.tfvars       # Settings for development
    ├── staging.tfvars   # Settings for testing
    └── prod.tfvars      # Settings for production
```

**Why organize this way?**
- Keep code organized and easy to find
- Separate settings for each environment
- Easy to scale and maintain

**Deploy to different environments:**
```bash
# Test it first (development)
terraform apply -var-file="environments/dev.tfvars"

# Later, deploy to production
terraform apply -var-file="environments/prod.tfvars"
```

The same Terraform code works everywhere, just with different settings!

## Getting Important Information Back

After Terraform creates resources, you might want to remember important details (like server IP addresses or database names). Use **outputs** for this:

```hcl
output "server_ip" {
  description = "The IP address of the web server"
  value       = aws_instance.web_server.public_ip
}

output "database_endpoint" {
  description = "Where to connect to your database"
  value       = aws_db_instance.main.endpoint
}
```

After you run `terraform apply`, Terraform will show you these values automatically.

## Tips from Experience

1. **Always preview before doing**: Run `terraform plan` and read the output carefully
2. **Use the same setup everywhere**: One set of files for dev, staging, and production
3. **Keep sensitive data private**: Never put passwords or API keys in your code
4. **Organize your files**: Group related resources (all network stuff together, all servers together)
5. **Name things clearly**: Use descriptive names so you remember what each resource does
6. **Save state files safely**: Keep them backed up, don't share them by accident

## Common Mistakes I've Made

- **Forgot to commit state to version control properly**: Couldn't remember what I created, had to rebuild
- **Ran `terraform apply` in the wrong directory**: Accidentally deleted some servers
- **Made changes in AWS console instead of Terraform**: Terraform got confused and tried to undo my changes
- **Hardcoded server IPs**: Had to change every file when I moved servers
- **Didn't test on development first**: Broke production because I didn't test the changes

**The lesson:** Always test on dev first, keep everything in code, never manually change things in AWS console.

## Getting Started (Super Simple)

**Requirement:** You need an AWS account (free tier is fine)

**Step 1: Install Terraform**
```bash

# Ubuntu/Debian
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install terraform
```

**Step 2: Create a simple file called `main.tf`**
```hcl
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "first_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  tags = {
    Name = "MyFirstServer"
  }
}
```

**Step 3: Run these commands**
```bash
terraform init    # Downloads AWS tools
terraform plan    # Shows what will happen
terraform apply   # Creates your server!
```

That's it! You now have a server running on AWS.

**When you're done:**
```bash
terraform destroy  # Delete everything and stop paying for it
```

## Conclusion

Terraform made a huge difference for me. Instead of manually clicking around AWS, I can now:
- Create the same setup in seconds
- Share my infrastructure with teammates
- Keep everything organized in code
- Track changes like I do with regular code

If you're managing servers manually, give Terraform a try. Start with a simple project, get comfortable with the basic commands, and soon you'll be managing entire cloud setups with just a few commands.
