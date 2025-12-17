# Quick Start Guide for Azure Deployment Issues

## 🔥 **Immediate Actions**

If your chatbot isn't working in Azure, follow these steps in order:

### **Step 1: Check if Backend is Running**
Open this URL in your browser:
```
https://placematch-dqd7gnc7dhd6gjfy.centralus-01.azurewebsites.net/api/health
```

- ✅ **If you see a response**: Backend is running, skip to Step 3
- ❌ **If you see an error**: Continue to Step 2

---

### **Step 2: Configure Azure Environment Variables**

Your backend needs authentication credentials to work:

1. **Go to Azure Portal**: https://portal.azure.com
2. **Navigate to**: App Services → **placematch**
3. **Click**: Settings → **Configuration** (left menu)
4. **Click**: **+ New application setting** for each of these:

```bash
NODE_ENV = production
PORT = 8080
AZURE_TENANT_ID = 10c4a35b-ecf1-45e7-89d8-0f3b8aee0391
```

5. **Click**: **Save** at the top
6. **Click**: **Continue** when prompted to restart

#### **Option A: Use Managed Identity (Easier - Recommended)**
1. Go to **Identity** (left menu)
2. Under **System assigned**, toggle to **On**
3. Click **Save**
4. Copy the **Object (principal) ID**
5. Go to your **Azure AI Foundry** resource
6. Click **Access control (IAM)** → **+ Add** → **Add role assignment**
7. Select **Cognitive Services User** role
8. Select your App Service managed identity
9. Click **Review + assign**

#### **Option B: Use Service Principal**
If you have service principal credentials:
```bash
AZURE_CLIENT_ID = <your-client-id>
AZURE_CLIENT_SECRET = <your-client-secret>
```

---

### **Step 3: Restart the Backend**

After configuring settings:
1. In Azure Portal → App Services → **placematch**
2. Click **Restart** at the top
3. Wait 30-60 seconds
4. Test again: https://placematch-dqd7gnc7dhd6gjfy.centralus-01.azurewebsites.net/api/health

---

### **Step 4: Re-Deploy with Fixed Files**

The deployment was missing required module files. This has been fixed.

To deploy the fix:
```bash
git add .
git commit -m "Fix Azure deployment - include all required modules"
git push origin main
```

Wait 2-3 minutes for the GitHub Action to complete.

---

### **Step 5: Check Deployment Logs**

If still not working, check the logs:

**In Azure Portal:**
1. Go to App Services → **placematch**
2. Click **Log stream** (left menu under Monitoring)
3. Look for errors:
   - `Cannot find module` → Re-deploy needed (Step 4)
   - `Authentication failed` → Check Step 2
   - `ECONNREFUSED` → Port issue, check PORT=8080

**Or use Azure CLI:**
```powershell
az webapp log tail --name placematch --resource-group <your-resource-group-name>
```

---

### **Step 6: Test Frontend Connection**

Once backend is responding:
1. Open your Static Web App: https://calm-moss-0431c570f.3.azurestaticapps.net
2. Open browser DevTools (F12)
3. Go to **Console** tab
4. Try sending a message in the chat
5. Look for errors:
   - `CORS error` → Backend CORS needs updating
   - `404 Not Found` → Check backend URL in App.jsx
   - `Failed to fetch` → Backend not accessible

---

## 🎯 **Most Common Issues**

### **Issue 1: "Cannot find module" errors**
✅ **Fix**: Re-deploy using Step 4 above

### **Issue 2: "Authentication failed" or 401 errors**
✅ **Fix**: Configure environment variables in Step 2

### **Issue 3: Backend doesn't respond**
✅ **Fix**: Check environment variables and restart (Steps 2-3)

### **Issue 4: CORS errors in browser**
✅ **Fix**: Update CORS origins in [server.js](my-react-app/server.js#L78-L84)

---

## 📞 **Still Having Issues?**

### View Detailed Logs:
```powershell
# View last 100 log entries
az webapp log download --name placematch --resource-group <your-rg> --log-file logs.zip

# Or visit Kudu (advanced):
# https://placematch.scm.azurewebsites.net
```

### Common Log Locations in Azure:
- Application logs: `Monitoring` → `Log stream`
- Deployment logs: `Deployment` → `Deployment Center` → view workflow runs

---

## ✅ **Success Checklist**

- [ ] Backend health endpoint responds (Step 1)
- [ ] Environment variables configured (Step 2)
- [ ] Managed Identity enabled OR Service Principal configured (Step 2)
- [ ] App Service restarted (Step 3)
- [ ] Latest code deployed with all modules (Step 4)
- [ ] No errors in Log stream (Step 5)
- [ ] Frontend can connect to backend (Step 6)

Once all items are checked, your chatbot should be working! 🎉
