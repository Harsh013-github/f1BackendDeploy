# AWS Elastic Beanstalk Setup Guide

## Prerequisites
- AWS Account with appropriate IAM permissions
- GitHub secrets configured (`AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`)

## Manual Steps to Create EB Environment

### Step 1: Create Elastic Beanstalk Application
1. Go to [AWS Elastic Beanstalk Console](https://console.aws.amazon.com/elasticbeanstalk)
2. Click **Create Application**
3. Fill in:
   - **Application name**: `f1-backend`
   - **Automatically upload your code** (optional, we'll deploy via GitHub)
4. Click **Create**

### Step 2: Create Environment
1. In the application dashboard, click **Create environment**
2. Configure environment:
   - **Environment name**: `f1-backend-env`
   - **Environment tier**: Web server environment
   - **Platform**: Node.js 22 running on 64bit Amazon Linux 2023
   - **Application code**: Sample application (we'll deploy real code after)
3. Click **Configure more options**

### Step 3: Configure Environment
1. **Instance configuration**:
   - Instance type: `t3.micro`
   - Environment type: **SingleInstance** (cost-effective)

2. **Network**:
   - VPC: Default VPC
   - Public IP address enabled: Yes

3. **Environment properties** (optional, can set via `.ebextensions/00_options.config`):
   - `NODE_ENV`: `production`
   - `PORT`: `8080`
   - `API`: `/api`

4. Click **Create environment** and wait ~10 minutes for initialization

### Step 4: Verify Environment
- Check EB Console for green health status
- Note the URL: `https://f1-backend-env.us-east-1.elasticbeanstalk.com`

## CI/CD Deployment

Once the environment exists, GitHub Actions will:
1. ✅ Build the application
2. ✅ Create S3 bucket (if not exists)
3. ✅ Package code and upload to S3
4. ✅ Deploy to EB environment
5. ✅ Auto-trigger on push to `main` branch

### Monitor Deployments
- GitHub: `https://github.com/Harsh013-github/f1BackendDeploy/actions`
- AWS EB Console: Check deployment history and health status

### Troubleshooting

**Health Check Failed:**
- Ensure `/api/health` endpoint is returning 200 OK
- Check CloudWatch logs in EB Console

**Application not starting:**
- Verify `npm start` works locally
- Check that all environment variables are set in EB
- Review logs: EB Console → Logs → Request logs

**Deployment slow:**
- First deployment takes longer (creating instances)
- Subsequent deployments are faster

## Environment Variables

Set these in AWS EB Environment Properties or `.ebextensions/00_options.config`:

```env
NODE_ENV=production
PORT=8080
API=/api
SUPABASE_URL=<your_supabase_url>
SUPABASE_KEY=<your_anon_key>
SUPABASE_SERVICE_ROLE_KEY=<your_service_role_key>
JWT_SECRET=<your_jwt_secret>
```

⚠️ **Do NOT commit secrets to GitHub!** Always use AWS EB Environment Properties.

## Next Steps

1. Create EB Application & Environment (see steps above)
2. Wait for environment to be healthy (green status)
3. Push to `main` branch to trigger deployment
4. Monitor in GitHub Actions
5. Test your API at the EB URL

---

**Created:** December 4, 2025
**Application:** f1-backend
**Environment:** f1-backend-env
**Region:** us-east-1
