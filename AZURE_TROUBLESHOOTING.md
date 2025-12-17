# Azure Deployment Troubleshooting Guide

## Common Issues and Solutions

### Issue: Backend Failing to Start

#### **Symptom**: The chatbot doesn't respond when deployed to Azure
#### **Root Causes**:
1. Missing environment variables
2. Missing module files in deployment
3. Authentication issues with Azure AI Foundry

---

## ✅ **Solution 1: Configure Environment Variables**

Your backend needs these environment variables configured in Azure App Service:

### Navigate to Azure Portal:
1. Go to [Azure Portal](https://portal.azure.com)
2. Find your App Service: **placematch**
3. Go to **Configuration** → **Application settings**

### Add these settings:

```bash
# Required for production
NODE_ENV=production
PORT=8080

# Azure Authentication (Service Principal)
AZURE_TENANT_ID=10c4a35b-ecf1-45e7-89d8-0f3b8aee0391
AZURE_CLIENT_ID=<your-service-principal-client-id>
AZURE_CLIENT_SECRET=<your-service-principal-secret>

# Application Insights (already in code but can override)
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=0baf9587-13b7-41a1-a146-64b308ab3084;IngestionEndpoint=https://eastus-8.in.applicationinsights.azure.com/;LiveEndpoint=https://eastus.livediagnostics.monitor.azure.com/;ApplicationId=efe29f25-bb02-4ddd-bcb6-127cca07ad11
```

### To create a Service Principal:
```bash
az ad sp create-for-rbac --name "marketlens-backend-sp" --role Contributor --scopes /subscriptions/<your-subscription-id>/resourceGroups/<your-resource-group>
```

This will output:
- `appId` → Use as **AZURE_CLIENT_ID**
- `password` → Use as **AZURE_CLIENT_SECRET**
- `tenant` → Use as **AZURE_TENANT_ID**

---

## ✅ **Solution 2: Enable Managed Identity (Alternative to Service Principal)**

Instead of using a service principal, you can use Azure Managed Identity:

1. In Azure Portal → Your App Service → **Identity**
2. Enable **System assigned** identity
3. Go to your Azure AI Foundry resource → **Access control (IAM)**
4. Add role assignment: **Cognitive Services User** to your App Service's managed identity
5. Remove the `AZURE_CLIENT_ID` and `AZURE_CLIENT_SECRET` from configuration (keep NODE_ENV and PORT)

---

## ✅ **Solution 3: Check Deployment Logs**

### View Application Logs:
```bash
az webapp log tail --name placematch --resource-group <your-resource-group>
```

### Or in Azure Portal:
1. Go to your App Service → **Log stream**
2. Look for errors like:
   - `Cannot find module` → Missing files in deployment
   - `Authentication failed` → Credential issues
   - `ECONNREFUSED` → Backend not starting

---

## ✅ **Solution 4: Verify CORS Configuration**

The backend allows requests from your static web app. Verify the URL matches:

### Check your Static Web App URL:
1. Go to Azure Portal → Static Web Apps → **calm-moss-0431c570f**
2. Copy the URL (e.g., `https://calm-moss-0431c570f.3.azurestaticapps.net`)

### Update server.js if needed:
The CORS origin in [server.js](my-react-app/server.js#L78-L84) should match your Static Web App URL.

---

## ✅ **Solution 5: Test Backend Directly**

### Test the backend health endpoint:
```bash
curl https://placematch-dqd7gnc7dhd6gjfy.centralus-01.azurewebsites.net/api/health
```

If this fails, the backend isn't running properly.

### Test from local:
```bash
cd my-react-app
npm install
npm run server
```

Then test: `http://localhost:3001/api/health`

---

## ✅ **Solution 6: Re-deploy Backend with All Files**

The updated deployment workflow now includes all required files. To re-deploy:

```bash
git add .
git commit -m "Fix backend deployment - include all module files"
git push origin main
```

This will trigger the GitHub Action workflow.

---

## ✅ **Solution 7: Enable Diagnostic Logging**

In Azure App Service:
1. Go to **Monitoring** → **App Service logs**
2. Enable:
   - **Application Logging (Filesystem)** → Verbose
   - **Detailed error messages** → On
   - **Failed request tracing** → On
3. Click **Save**

Then check logs in **Log stream** or download from **Advanced Tools (Kudu)** → `https://placematch.scm.azurewebsites.net/`.

---

## Quick Diagnostic Checklist

- [ ] Environment variables configured in Azure App Service
- [ ] Service Principal created and credentials added OR Managed Identity enabled
- [ ] All JavaScript modules included in deployment (check latest workflow run)
- [ ] Backend URL in App.jsx matches actual Azure App Service URL
- [ ] CORS origin matches Static Web App URL
- [ ] Application logs enabled and checked
- [ ] Backend responds to `/api/health` endpoint

---

## Contact Points for Issues

### Backend not starting:
- Check: Application logs in Azure Portal
- Look for: Module import errors, authentication errors

### Frontend can't reach backend:
- Check: Network tab in browser DevTools
- Look for: CORS errors, 404 errors, connection refused

### Authentication errors:
- Check: Service Principal has correct permissions on AI Foundry
- Verify: Tenant ID matches (10c4a35b-ecf1-45e7-89d8-0f3b8aee0391)

---

## Test Commands

```bash
# Test backend locally
cd my-react-app
npm run server

# Test deployed backend
curl https://placematch-dqd7gnc7dhd6gjfy.centralus-01.azurewebsites.net/api/health

# View Azure logs
az webapp log tail --name placematch --resource-group <your-rg>

# Restart Azure App Service
az webapp restart --name placematch --resource-group <your-rg>
```
