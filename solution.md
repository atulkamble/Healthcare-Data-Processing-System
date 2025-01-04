Here’s the detailed implementation of the **Healthcare Data Processing System** project, including full project code, setup steps, and a sample dataset for testing.

---

### **Step 1: Create an Azure Resource Group**
Start by creating a resource group to organize all project resources.

```bash
az group create --name HealthRG --location eastus
```

---

### **Step 2: Set Up Azure Event Hubs for Real-Time Data Ingestion**
Azure Event Hubs will handle the ingestion of patient health data.

1. **Create an Event Hubs Namespace:**
   ```bash
   az eventhubs namespace create \
     --name HealthcareNamespace \
     --resource-group HealthRG \
     --location eastus
   ```

2. **Create an Event Hub:**
   ```bash
   az eventhubs eventhub create \
     --name PatientDataHub \
     --namespace-name HealthcareNamespace \
     --resource-group HealthRG
   ```

3. **Create a Consumer Group for Data Processing:**
   ```bash
   az eventhubs eventhub consumer-group create \
     --eventhub-name PatientDataHub \
     --namespace-name HealthcareNamespace \
     --resource-group HealthRG \
     --name HealthConsumerGroup
   ```

---

### **Step 3: Create Azure Cosmos DB for Structured Data**
Azure Cosmos DB will store structured patient health records.

1. **Create a Cosmos DB Account:**
   ```bash
   az cosmosdb create \
     --name HealthDataDB \
     --resource-group HealthRG \
     --kind MongoDB \
     --locations regionName=eastus failoverPriority=0 isZoneRedundant=false
   ```

2. **Create a Database and Collection:**
   ```bash
   az cosmosdb mongodb database create \
     --account-name HealthDataDB \
     --name PatientDB \
     --resource-group HealthRG

   az cosmosdb mongodb collection create \
     --account-name HealthDataDB \
     --database-name PatientDB \
     --name PatientRecords \
     --resource-group HealthRG
   ```

---

### **Step 4: Configure Azure Key Vault for Encryption**
Secure sensitive data using Azure Key Vault.

1. **Create a Key Vault:**
   ```bash
   az keyvault create \
     --name HealthVault \
     --resource-group HealthRG \
     --location eastus
   ```

2. **Store a Secret in Key Vault:**
   ```bash
   az keyvault secret set \
     --vault-name HealthVault \
     --name "DatabaseKey" \
     --value "MySecureKey123"
   ```

---

### **Step 5: Set Up Azure Storage for Blob Storage**
Blob Storage is used for unstructured data like diagnostic images.

1. **Create a Storage Account:**
   ```bash
   az storage account create \
     --name HealthStorageAccount \
     --resource-group HealthRG \
     --location eastus \
     --sku Standard_LRS
   ```

2. **Create a Blob Container:**
   ```bash
   az storage container create \
     --account-name HealthStorageAccount \
     --name health-records \
     --public-access blob
   ```

---

### **Step 6: Implement Azure Data Factory for Integration**
Azure Data Factory automates data movement and transformation.

1. **Create a Data Factory Instance:**
   ```bash
   az datafactory create \
     --name HealthDataFactory \
     --resource-group HealthRG \
     --location eastus
   ```

2. **Define a Pipeline for Data Transformation:**
   Use the Azure Portal or ARM templates to define a pipeline that reads from Event Hubs and writes to Cosmos DB.

---

### **Step 7: Configure Backup and Recovery**
1. **Set Up Azure Backup for Cosmos DB:**
   ```bash
   az cosmosdb backup-policy update \
     --account-name HealthDataDB \
     --resource-group HealthRG \
     --backup-policy-type Continuous
   ```

2. **Enable Recovery Services Vault for Other Resources:**
   ```bash
   az backup vault create \
     --name HealthRecoveryVault \
     --resource-group HealthRG \
     --location eastus

   az backup policy set \
     --name DefaultPolicy \
     --vault-name HealthRecoveryVault \
     --resource-group HealthRG
   ```

---

### **Step 8: Deploy a Sample Application**
Create a Python application to simulate real-time data ingestion.

#### **Sample Dataset:**
```json
[
  {"patient_id": "P001", "name": "John Doe", "age": 35, "condition": "Hypertension", "timestamp": "2025-01-01T10:00:00Z"},
  {"patient_id": "P002", "name": "Jane Smith", "age": 28, "condition": "Diabetes", "timestamp": "2025-01-01T10:05:00Z"}
]
```

#### **Python Code for Data Ingestion:**
```python
from azure.eventhub import EventHubProducerClient, EventData
import json

connection_str = "Endpoint=sb://HealthcareNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<key>"
event_hub_name = "PatientDataHub"

data = [
    {"patient_id": "P001", "name": "John Doe", "age": 35, "condition": "Hypertension", "timestamp": "2025-01-01T10:00:00Z"},
    {"patient_id": "P002", "name": "Jane Smith", "age": 28, "condition": "Diabetes", "timestamp": "2025-01-01T10:05:00Z"}
]

def send_events(data):
    producer = EventHubProducerClient.from_connection_string(conn_str=connection_str, eventhub_name=event_hub_name)
    event_data_batch = producer.create_batch()
    for record in data:
        event_data_batch.add(EventData(json.dumps(record)))
    producer.send_batch(event_data_batch)
    print("Events sent successfully!")

send_events(data)
```

---

### **Architecture Diagram**
```plaintext
+----------------+       +--------------------+       +----------------+
| IoT Devices    | --->  | Azure Event Hubs   | --->  | Azure Data     |
| (Data Sources) |       | (Real-Time Ingest) |       | Factory        |
+----------------+       +--------------------+       +----------------+
                                                          |
                                                          v
+----------------------+       +-----------------+      +------------------+
| Azure Key Vault      |       | Azure Cosmos DB |      | Azure Blob       |
| (Encryption & Secrets)|       | (Structured Data)|      | Storage (Files) |
+----------------------+       +-----------------+      +------------------+
                                                          |
                                                          v
                                                  +------------------+
                                                  | Azure Backup &   |
                                                  | Recovery Services |
                                                  +------------------+
```

---

### **Testing the Solution**
1. **Run the Python Script:** Verify data ingestion into Event Hubs.
2. **Check Cosmos DB:** Ensure the records are transformed and stored.
3. **Validate Blob Storage:** Upload and retrieve files via the Azure Portal.
4. **Simulate Failover:** Test recovery using Azure Backup.

Let me know if you need further details or refinements!
