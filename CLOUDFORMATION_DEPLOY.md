# Deploy EB Environment using CloudFormation

## Quick Start (5 minutes)

### Step 1: Download the CloudFormation Template
```bash
# Template is already in the repository:
cf-eb-template.json
```

### Step 2: Deploy using AWS CLI
Run this command in your terminal (make sure AWS CLI is configured):

```bash
aws cloudformation create-stack \
  --stack-name f1-backend-stack \
  --template-body file://cf-eb-template.json \
  --parameters \
    ParameterKey=ApplicationName,ParameterValue=f1-backend \
    ParameterKey=EnvironmentName,ParameterValue=f1-backend-env \
    ParameterKey=InstanceType,ParameterValue=t3.micro \
  --region us-east-1 \
  --capabilities CAPABILITY_IAM
```

### Step 3: Monitor Stack Creation
```bash
# Watch stack creation progress
aws cloudformation describe-stacks \
  --stack-name f1-backend-stack \
  --region us-east-1 \
  --query 'Stacks[0].StackStatus'
```

Wait for status: `CREATE_COMPLETE` (takes ~10 minutes)

### Step 4: Get the Environment URL
```bash
aws cloudformation describe-stacks \
  --stack-name f1-backend-stack \
  --region us-east-1 \
  --query 'Stacks[0].Outputs[?OutputKey==`EnvironmentURL`].OutputValue' \
  --output text
```

---

## OR Deploy via AWS Console (Manual)

1. Go to [AWS CloudFormation Console](https://console.aws.amazon.com/cloudformation)
2. Click **Create stack**
3. Choose **Upload a template file**
4. Upload `cf-eb-template.json`
5. Click **Next**
6. Stack name: `f1-backend-stack`
7. Parameters (keep defaults):
   - Application Name: `f1-backend`
   - Environment Name: `f1-backend-env`
   - Instance Type: `t3.micro`
8. Click **Next** → **Next** → **Create stack**
9. Wait ~10 minutes for `CREATE_COMPLETE` status

---

## After Stack Creation

✅ Your EB Application and Environment are ready!

### Next Steps:
1. Push code to GitHub `main` branch
2. GitHub Actions workflow auto-deploys
3. Monitor at: `https://github.com/Harsh013-github/f1BackendDeploy/actions`
4. Access your API at the EB URL from outputs

### View Logs:
```bash
# Get recent deployment logs
aws elasticbeanstalk retrieve-environment-logs \
  --environment-name f1-backend-env \
  --info-type bundle \
  --region us-east-1
```

### Delete Stack (if needed):
```bash
aws cloudformation delete-stack \
  --stack-name f1-backend-stack \
  --region us-east-1
```

---

**Template includes:**
- ✅ Elastic Beanstalk Application: `f1-backend`
- ✅ Elastic Beanstalk Environment: `f1-backend-env`
- ✅ Node.js 22 runtime
- ✅ t3.micro instance (cost-effective)
- ✅ CloudWatch logging enabled
- ✅ Health check endpoint: `/api/health`
- ✅ Environment variables configured
