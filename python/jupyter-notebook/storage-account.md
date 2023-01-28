
# Access the data stored in storage account with `Jupyter Notebook` 

In case of missing dependencies, install:
```shell
pip install azure-storage-blob
```

```python
from azure.storage.blob import BlobServiceClient

account_name = '{your_storage_account_name}'
account_key = '{your_key}'

blob_service_client = BlobServiceClient(account_url="https://{}.blob.core.windows.net".format(account_name), credential=account_key)

containers = blob_service_client.list_containers()
for container in containers:
    print(container.name)
```

The above will print out all the containers in the storag account.

To get all files with folder filter:
```python
container_client = blob_service_client.get_container_client("{your_container_name}")
folder_name = "{your_file_prefix_filter}"
blobs = container_client.list_blobs(name_starts_with=folder_name)
for blob in blobs:
    print(blob.name)
```

## Data lake Gen2 access

```python
from azure.identity import ClientSecretCredential, ManagedIdentityCredential
from azure.storage.blob import BlobServiceClient
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings

def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global datalake_service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        datalake_service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)

def initialize_storage_account(storage_account_name, storage_account_key):
    
    try:  
        global datalake_service_client

        datalake_service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=storage_account_key)
    
    except Exception as e:
        print(e)

# Create a DataLakeServiceClient object
account_name = '{Account_Name}'
account_key = '{Account_Key}'
initialize_storage_account(account_name, account_key)

# List file systems in the account
file_systems = datalake_service_client.list_file_systems()

for file_system in file_systems:
    print(file_system.name)
```
