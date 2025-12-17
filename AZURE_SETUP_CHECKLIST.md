# Complete Azure Deployment Setup Checklist

This checklist covers everything needed to get your chatbot working in Azure.

## ✅ **Part 1: GitHub Repository Configuration**

### Configure GitHub Secret for Backend Deployment

1. **Get the Publish Profile from Azure:**
   - Go to [Azure Portal](https://portal.azure.com)
   - Navigate to: App Services → **placematch**
   - Click **Download publish profile** (top toolbar)
   - This downloads an XML file

2. **Add Secret to GitHub:**
   - Go to your GitHub repository
   - Click: Settings → Secrets and variables → Actions
   - Click: **New repository secret**
   - Name: `AZURE_WEBAPP_PUBLISH_PROFILE`
   - Value: Paste the **entire contents** of the downloaded XML file
   - Click: **Add secret**

---

## ✅ **Part 2: Azure App Service Configuration**

### Configure Environment Variables

1. **Navigate to App Service:**
   - [Azure Portal](https://portal.azure.com) → App Services → **placematch**

2. **Add Application Settings:**
   - Click: Configuration (left menu under Settings)
   - Click: **+ New application setting** for each:

   ```bash
   Name: NODE_ENV
   Value: production
   
   Name: PORT
   Value: 8080
   
   Name: AZURE_TENANT_ID
   Value: 10c4a35b-ecf1-45e7-89d8-0f3b8aee0391
   ```

3. **Click Save** at the top
4. **Click Continue** when prompted to restart

---

## ✅ **Part 3: Authentication Setup**

Choose **ONE** of these options:

### **Option A: Managed Identity (Recommended)**

1. **Enable Managed Identity:**
   - In App Service (placematch) → Identity (left menu)
   - Under **System assigned**, toggle to **On**
   - Click **Save**
   - Copy the **Object (principal) ID** that appears

2. **Grant Permissions to AI Foundry:**
   - Navigate to your **Azure AI Foundry** resource
   - Click: Access control (IAM) → **+ Add** → **Add role assignment**
   - Select: **Cognitive Services User** role → Next
   - Select: **Managed identity** → **+ Select members**
   - Choose: App Service → **placematch**
   - Click: Select → Review + assign

### **Option B: Service Principal**

If you already have a service principal:

1. **Add these settings in App Service Configuration:**
   ```bash
   Name: AZURE_CLIENT_ID
   Value: <your-service-principal-client-id>
   
   Name: AZURE_CLIENT_SECRET
   Value: <your-service-principal-client-secret>
   ```

2. **Create new service principal (if needed):**
   ```bash
   az ad sp create-for-rbac --name "marketlens-backend-sp" \
     --role "Cognitive Services User" \
     --scopes /subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.CognitiveServices/accounts/<ai-foundry-name>
   ```

---

## ✅ **Part 4: Deploy Updated Code**

### Push Changes to GitHub

```bash
# From the T20-Housing-Project directory
git add .
git commit -m "Fix Azure deployment - include all modules and configure backend URL"
git push origin main
```

### Monitor Deployment

1. **GitHub Actions:**
   - Go to your GitHub repository
   - Click: Actions tab
   - Watch for two workflows:
     - ✅ "Azure Static Web Apps CI/CD"
     - ✅ "Deploy Backend to Azure Web App"

2. **Wait for completion** (~5 minutes total)

---

## ✅ **Part 5: Restart and Verify**

### Restart the Backend

1. Azure Portal → App Services → **placematch**
2. Click **Restart** (top toolbar)
3. Click **Yes** to confirm
4. Wait 30-60 seconds

### Test the Backend

Open this URL in a browser:
```
https://placematch-dqd7gnc7dhd6gjfy.centralus-01.azurewebsites.net/api/health
```

**Expected:** You should see a JSON response like:
```json
{
  "status": "healthy",
  "timestamp": "..."
}
```

**If you see an error:** Check logs (see Part 6)

### Test the Frontend

1. Open: https://calm-moss-0431c570f.3.azurestaticapps.net
2. Type a message in the chat: "Tell me about housing in Seattle"
3. The chatbot should respond

---

## ✅ **Part 6: Troubleshooting**

### View Application Logs

**Azure Portal:**
1. App Services → **placematch**
2. Monitoring → **Log stream** (left menu)
3. Look for errors

**Azure CLI:**
```bash
az webapp log tail --name placematch --resource-group <your-resource-group>
```

### Common Errors and Solutions

| Error | Solution |
|-------|----------|
| `Cannot find module 'tracing.js'` | Re-deploy code (Part 4) |
| `Authentication failed` | Check Part 3 (authentication) |
| `getaddrinfo ENOTFOUND` | Check environment variables (Part 2) |
| `CORS error` in browser | Update CORS in server.js |
| `404 Not Found` | Check VITE_API_URL in .env.production |

### Enable Detailed Logging

1. App Services → **placematch**
2. Monitoring → **App Service logs** (left menu)
3. Set **Application Logging (Filesystem)** to **Verbose**
4. Click **Save**

---

## ✅ **Part 7: Verification Checklist**

Use this final checklist to confirm everything is working:

- [ ] GitHub secret `AZURE_WEBAPP_PUBLISH_PROFILE` is configured
- [ ] GitHub Actions workflows completed successfully
- [ ] Azure App Service environment variables set (NODE_ENV, PORT, AZURE_TENANT_ID)
- [ ] Authentication configured (Managed Identity OR Service Principal)
- [ ] Backend responds at `/api/health` endpoint
- [ ] Frontend loads at Static Web App URL
- [ ] Chat message successfully gets a response
- [ ] No CORS errors in browser console
- [ ] No errors in Azure App Service logs

---

## 📞 **Getting Help**

### Check These First:
1. Run diagnostic: `cd my-react-app && node check-azure-deployment.js`
2. Check backend logs: See Part 6 above
3. Review: [QUICKFIX_AZURE.md](QUICKFIX_AZURE.md)

### Useful Links:
- Backend URL: https://placematch-dqd7gnc7dhd6gjfy.centralus-01.azurewebsites.net
- Frontend URL: https://calm-moss-0431c570f.3.azurestaticapps.net
- Azure Portal: https://portal.azure.com
- GitHub Actions: [Your repo]/actions

### Azure CLI Quick Commands:
```bash
# View logs
az webapp log tail --name placematch --resource-group <your-rg>

# Restart app
az webapp restart --name placematch --resource-group <your-rg>

# Download logs
az webapp log download --name placematch --resource-group <your-rg>

# View app settings
az webapp config appsettings list --name placematch --resource-group <your-rg>
```

---

## 🎉 **Success!**

Once all checklist items are complete and the chat responds, you're done!

Your app is now:
- ✅ Deployed to Azure Static Web Apps (frontend)
- ✅ Deployed to Azure App Service (backend)
- ✅ Connected to Azure AI Foundry (AI agents)
- ✅ Monitored with Application Insights (telemetry)
