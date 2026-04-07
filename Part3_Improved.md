# Architecture Improvements: Docker & Terraform

To transform Symplichain's semi-manual deployments into a modern, robust, and reproducible pipeline, we should adopt Containerization (Docker) and Infrastructure as Code (Terraform).

## 1. Why Docker? 🐳
Currently, each EC2 instance is a "pet"—meaning it requires manual setup of Python, libraries, and system dependencies. This leads to drift. Docker solves this by:
- **Reproducibility:** A single container image runs identically on a developer's laptop, the Staging EC2, and Production EC2.
- **Isolation:** You can run multi-tenant services on the same EC2 instance without dependency conflicts.
- **Zero-Downtime:** Using tools like Docker Swarm or Kubernetes (or even blue-green with Nginx) becomes trivial.

### Sample Dockerfile (Django Backend)
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["gunicorn", "symflow.wsgi:application", "--bind", "0.0.0.0:8000"]
```

## 2. Why Terraform? 🏗️
Terraform replaces manual clicking in the AWS Console. It gives us:
- **Disaster Recovery:** If an RDS instance is accidentally deleted, we can recreate it in minutes using `terraform apply`.
- **Environment Parity:** We can clone the entire Production stack into a "Staging-2" environment instantly for testing complex updates.
- **Audit Log:** Versioning infrastructure files in GitHub allows us to see *who* changed *what* on AWS.

### Sample Terraform (Partial)
```hcl
resource "aws_db_instance" "production" {
  engine                = "postgres"
  instance_class        = "db.t3.medium"
  allocated_storage      = 20
  db_name                = "symflow_prod"
  username              = var.db_username
  password              = var.db_password
  publicly_accessible   = false
  skip_final_snapshot   = true
}

resource "aws_instance" "app_server" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t3.small"
  tags = {
    Name = "SymFlow-API"
  }
}
```

## 3. Improved CI/CD Flow
Instead of `git pull` on the server:
1. `Actions`: Build Docker image -> Push to AWS ECR (Elastic Container Registry).
2. `Actions`: SSH into EC2 -> `docker pull` image -> `docker compose up -d`.
3. This is faster and fails before hitting the server if the build is broken.
