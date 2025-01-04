# Healthcare-Data-Processing-System

### **Project : Healthcare Data Processing System**

#### **Client Requirement:**
- Process and store patient health data.
- Secure data with Azure Key Vault and encryption.
- Implement automated backups and disaster recovery.
- Provide real-time data ingestion using Azure Event Hubs.

---

#### **Solution Overview:**
1. **Data Ingestion:**  
   Use Azure Event Hubs for real-time data ingestion.
2. **Storage:**  
   Store structured data in Cosmos DB (NoSQL) and blobs in Azure Storage.
3. **Security:**  
   Encrypt data using Azure Key Vault.
4. **Automation:**  
   Configure Azure Data Factory for data transformation and integration.
5. **Backup & Recovery:**  
   Use Azure Backup for disaster recovery.

---

#### **Code Snippet:**
```bash
# Create Event Hub Namespace and Hub
az eventhubs namespace create --name HealthcareNamespace --resource-group HealthRG --location eastus
az eventhubs eventhub create --name PatientDataHub --namespace-name HealthcareNamespace --resource-group HealthRG

# Create Cosmos DB Account
az cosmosdb create --name HealthDataDB --resource-group HealthRG --kind MongoDB --locations regionName=eastus failoverPriority=0 isZoneRedundant=false

# Configure Azure Key Vault
az keyvault create --name HealthVault --resource-group HealthRG --location eastus
az keyvault secret set --vault-name HealthVault --name "DatabaseKey" --value "MySecureKey123"
```

---

#### **Architecture Diagram:**

- IoT Devices → Azure Event Hubs → Azure Data Factory  
- Azure Data Factory → Azure Cosmos DB + Azure Blob Storage  
- Azure Key Vault (encryption)  
- Azure Backup for disaster recovery  

---
