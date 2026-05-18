# Render Deployment Guide for E-commerce Backend

Complete guide to deploy your Java Spring Boot E-commerce application to Render.

## 📋 Prerequisites

- ✅ GitHub Account (you have this)
- ⚠️ Render Account (free at https://render.com)
- ⚠️ MySQL Database (choose one below)

## 🗄️ Database Setup (Choose One)

### Option 1: Render's Built-in PostgreSQL (Easiest for Free Tier)
1. Sign up at https://render.com
2. Dashboard → New → PostgreSQL
3. Choose Free tier
4. Note the connection details
5. **Note**: Requires changing `pom.xml` to use PostgreSQL instead of MySQL

### Option 2: AWS RDS MySQL (Recommended for Production)
1. Go to https://aws.amazon.com/rds/
2. Create MySQL database instance
3. Enable "Public accessibility"
4. Create security group allowing port 3306
5. Note: `Endpoint`, `username`, `password`

### Option 3: Railway MySQL (Easiest)
1. Go to https://railway.app
2. New Project → Add MySQL
3. Get connection details from `$DATABASE_URL`
4. Free tier provides 500 hours/month

### Option 4: PlanetScale MySQL (Free Forever)
1. Go to https://planetscale.com
2. Create MySQL database
3. Get connection URL
4. Free tier: 5GB storage, unlimited queries

## 🚀 Deployment Steps

### Step 1: Create Render Account
1. Go to https://render.com
2. Sign up with GitHub (recommended)
3. Grant repository access

### Step 2: Create MySQL Database (If not already done)
Choose from options above and have credentials ready:
- Database Host
- Database Name
- Username
- Password

### Step 3: Create Web Service in Render

1. In Render Dashboard, click **New +** → **Web Service**
2. Select your GitHub repository
3. Authorize GitHub if needed
4. Choose: `abbhiiii06/Java-E-commerce-Backend-Application`
5. Configure:
   - **Name**: `ecommerce-api`
   - **Environment**: `Docker`
   - **Region**: Select closest to you (or `Oregon`)
   - **Branch**: `main`
   - **Plan**: `Free` (or `Starter` for better performance)

### Step 4: Add Environment Variables

Before clicking "Create Web Service", scroll down to **Environment** section and add:

```
SPRING_DATASOURCE_URL=jdbc:mysql://your-db-host:3306/ecommerce_db
SPRING_DATASOURCE_USERNAME=your_username
SPRING_DATASOURCE_PASSWORD=your_password
JWT_SECRET=abcdefghijklmnopqrstuvwxyz123456
SPRING_PROFILES_ACTIVE=prod
```

**Replace with YOUR values**:
- `your-db-host`: Your MySQL hostname (e.g., `mysql.example.com` or AWS RDS endpoint)
- `your_username`: MySQL username
- `your_password`: MySQL password
- `abcdefghijklmnopqrstuvwxyz123456`: Generate a strong 32+ character secret

### Step 5: Deploy

1. Click **Create Web Service**
2. Render starts building your Docker image
3. Monitor the logs in real-time
4. Deployment takes 5-10 minutes
5. Once complete, you get a live URL: `https://ecommerce-api.onrender.com`

### Step 6: Initialize Database

Once deployed, create the database schema:

```bash
# SSH into Render service or run migration
curl https://ecommerce-api.onrender.com/api/health
```

If you get a 200 response, your app is running! ✅

## 🔄 Setup GitHub Actions Auto-Deploy (Optional)

For automatic deployment on every push to `main`:

### Step 6a: Get Render Deploy Hook

1. In Render Dashboard, go to your service
2. Click **Settings** (bottom left)
3. Scroll to **Deploy Hook**
4. Copy the URL (keep it SECRET!)

### Step 6b: Add GitHub Secret

1. Go to your GitHub repo
2. Click **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Name: `RENDER_DEPLOY_HOOK`
5. Value: Paste the hook URL from Step 6a
6. Click **Add secret**

### Step 6c: Test

Push to `main` branch:
```bash
git push origin main
```

GitHub Actions will automatically trigger Render deployment! 🚀

## ✅ Verify Deployment

Test these endpoints:

```bash
# Health check
curl https://ecommerce-api.onrender.com/api/health

# Or from your local machine
curl -H "Content-Type: application/json" https://ecommerce-api.onrender.com/api/products
```

## 🐛 Troubleshooting

### Build Fails
```
Error: Maven build failed
```
**Solution**:
- Check Java version (must be 17)
- Verify pom.xml path in Dockerfile
- Run locally: `mvn clean package -f E-commerce_Application-main/E-commerce-Ap/pom.xml`

### Database Connection Error
```
Error: Cannot connect to MySQL database
```
**Solutions**:
1. Verify credentials are correct
2. Check database host is publicly accessible
3. Ensure security groups/firewall allow port 3306
4. Test locally: `mysql -h your-host -u user -p`
5. Check environment variables in Render dashboard

### Application Won't Start
```
Error: Application startup failed
```
**Solutions**:
1. Check Render logs: Dashboard → View logs
2. Verify all environment variables are set
3. Check application-prod.properties
4. Ensure database exists and is initialized

### Slow Performance / Free Tier Limitations
- Free tier: Spins down after 15 minutes of inactivity
- First request after spin-down takes 30+ seconds
- **Upgrade to Starter plan** for always-on service

## 📊 Monitor Your Application

### Logs
- Render Dashboard → View logs (real-time)
- Check for errors and warnings

### Metrics
- Response times
- Memory usage
- CPU usage

### Database
- Query logs
- Connection pool status
- Disk usage

## 🔒 Security Best Practices

1. **Change JWT Secret**: Update `JWT_SECRET` to a strong random value
2. **Database Credentials**: Use strong passwords
3. **HTTPS**: Render provides free SSL/TLS
4. **CORS**: Configure allowed origins
5. **Rate Limiting**: Implement in your API
6. **Environment Variables**: Never commit secrets

## 📚 Useful Commands

```bash
# View logs
curl https://ecommerce-api.onrender.com/api/logs

# Check version
curl https://ecommerce-api.onrender.com/api/version

# Restart service (from Render dashboard)
# Dashboard → Manual Deploy → Deploy latest commit
```

## 🆘 Need Help?

- **Render Support**: https://render.com/docs
- **Spring Boot Docs**: https://spring.io/projects/spring-boot
- **GitHub Issues**: Create an issue in your repo
- **Render Community**: https://render.com/community

## ✨ Next Steps

1. ✅ Deploy to Render
2. ✅ Test API endpoints
3. ⏭️ Set up monitoring (New Relic, Datadog)
4. ⏭️ Configure custom domain
5. ⏭️ Set up database backups
6. ⏭️ Implement CI/CD pipeline
7. ⏭️ Add Docker health checks

---

**Happy Deploying! 🎉**
