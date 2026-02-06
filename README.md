# AI Digital Twin

An AI-powered conversational Digital Twin deployed on AWS with full infrastructure automation using Terraform and GitHub Actions CI/CD pipelines.

## Overview

This project deploys a conversational AI chatbot that acts as a "digital twin" - representing me and answering questions on my behalf. The emphasis is on production-grade cloud infrastructure and best DevOps practices.

## Infrastructure & Deployment

### AWS Services
**AWS Lambda**: Serverless Python backend 
**API Gateway**: RESTful API management with CORS 
**S3**: Static frontend hosting + conversation memory storage 
**CloudFront**: Global CDN with HTTPS 
**Bedrock**: AI responses using Amazon Nova models
**DynamoDB**: Terraform state locking 
**IAM**: Role-based access with OIDC for GitHub Actions 

### Infrastructure as Code

- **Terraform** manages all AWS resources with workspace-based environment isolation (dev/test/prod)
- Remote state stored in S3 with DynamoDB locking

### CI/CD Pipeline

- **GitHub Actions** automates deployments on push to `main`
- OIDC authentication (no long-lived AWS credentials)
- Manual workflow dispatch for test/prod environments
- Dedicated destroy workflow with confirmation safeguard

## Tech Stack

**Frontend:** Next.js, React, Tailwind CSS  
**Backend:** Python, FastAPI, AWS Bedrock (Nova models)

## Prerequisites

- AWS CLI configured with credentials
- Terraform >= 1.0
- Node.js >= 20
- Python 3.12 + [uv](https://docs.astral.sh/uv/)
- Docker (for Lambda packaging)

## Local Development

```bash
# Backend
cd backend 
uv add -r requirements.txt 
uv run uvicorn server:app --reload

# Frontend (new terminal)
cd frontend 
npm install 
npm run dev
```

Visit http://localhost:3000

## Deploy to AWS

1. **Enable Bedrock models** in AWS Console (Nova Micro, Lite, Pro)

2. **Initialize Terraform state backend:**
   ```bash
   cd terraform && terraform init
   terraform apply -target=aws_s3_bucket.terraform_state -target=aws_dynamodb_table.terraform_locks
   ```

3. **Deploy:**
   ```bash
   ./scripts/deploy.sh dev  # or test, prod
   ```

4. **Access:** Get URL from `terraform output cloudfront_url`

## GitHub Actions (Optional)

1. Create OIDC role: `terraform apply -var="github_repository=USER/REPO"`
2. Add secrets in GitHub: `AWS_ROLE_ARN`, `AWS_ACCOUNT_ID`, `DEFAULT_AWS_REGION`
3. Push to `main` to auto-deploy

## Tear Down

```bash
./scripts/destroy.sh dev  # or test, prod
```

## Customization

If you want this conversational chatbot to represent you instead, edit these files to personalize:
- `backend/data/facts.json` - Basic info
- `backend/data/summary.txt` - Bio
- `backend/data/style.txt` - Communication style
- `backend/data/resume.txt` - Resume content
- `frontend/public/avatar.jpg` - Profile picture (optional)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

