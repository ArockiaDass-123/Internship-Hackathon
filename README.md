# Symplichain Software Engineering Intern Hackathon

This repository contains the submission for the Symplichain Software Engineering Intern Hackathon.

## Table of Contents
- [Submission Report](./Symplichain_Hackathon_Submission.md)
- [Architecture Improvements (Docker/Terraform)](./Part3_Improved.md)
- [CI/CD Workflows](./.github/workflows/)

## Why are the GitHub Actions failing?
The CI/CD workflows (`staging.yml` and `production.yml`) are designed to deploy to real AWS infrastructure (EC2 and S3). These currently show a "failed" status on GitHub because:
1. **Infrastructure Secrets:** The workflows require secrets (like SSH keys and AWS credentials) to be configured in the GitHub Repository settings (`Settings > Secrets and variables > Actions`).
2. **Project Files:** This repository is currently a "Submission-as-a-Docs" repo. In a real-world scenario, your Django backend (including `manage.py` and `requirements.txt`) and React frontend (including `package.json` and `vite.config.js`) would reside here. Since those files are currently on the Symplichain private codebase, the build steps like `npm install` and `pip install` will fail.

### How to set up Secrets (for future reference):
To make the workflows pass, you would need to add the following secrets to this repository:

| Secret Name | Description |
| ----------- | ----------- |
| `PROD_EC2_HOST` | The public IP or DNS of your production EC2 instance. |
| `PROD_SSH_PRIVATE_KEY` | The private PEM key to SSH into the EC2 instance. |
| `PROD_AWS_ACCESS_KEY_ID` | IAM User access key with S3/CloudFront permissions. |
| `PROD_AWS_SECRET_ACCESS_KEY` | IAM User secret key. |
| `PROD_S3_BUCKET` | The name of the S3 bucket for your static files. |
| `PROD_CF_DIST_ID` | The CloudFront Distribution ID for cache invalidation. |

## Report Summary
The core submission addresses:
- **Rate Limiting:** A Redis-backed Distributed Token Bucket system for fair request handling across customers.
- **Mobile Architecture:** A React Native-based AI-first interaction model.
- **CI/CD:** Automated workflows for staging/production (using SSH and S3 sync).
- **Debugging:** A priority-based troubleshooting methodology for mission-critical supply chain systems.

---

**Submitted by:** [Your Name]
**For:** Symplichain Technologies
