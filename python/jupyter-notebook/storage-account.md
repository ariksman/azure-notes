
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

## Data lake Gen2 access utility functions

```python
from azure.identity import ClientSecretCredential
from azure.storage.filedatalake import DataLakeServiceClient

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

def list_directory_contents(file_system_name, directory_path):
    try:
        
        file_system_client = datalake_service_client.get_file_system_client(file_system=file_system_name)

        paths = file_system_client.get_paths(path=directory_path)

        return list(paths)

    except Exception as e:
     print(e)

def download_file_from_directory(file_system_name, directory_path, file_path):
    try:
        file_system_client = datalake_service_client.get_file_system_client(file_system=file_system_name)
        directory_client = file_system_client.get_directory_client(directory_path)
        file_client = directory_client.get_file_client(file_path)

        download = file_client.download_file()
        file_content = download.readall().decode("utf-8")

        return file_content

    except Exception as e:
     print(e)
 ```
 
 Download all files within Data Lake Gen2 directory and read them into Pandas Data Frame
 
```python
import pandas as pd
import json

# Create a DataLakeServiceClient object
account_name = '{Account_Name}'
account_key = '{Account_Key}'
initialize_storage_account(account_name, account_key)

# List file systems in the account
file_systems = datalake_service_client.list_file_systems()

for file_system in file_systems:
    print(file_system.name)

gateway = "{Container_Name}"
directory = "{Directory_Within_Data_Lake}"

files = list_directory_contents(gateway, directory)

# List to store each json-file's data
data = []

for filePath in files:
        file_content = download_file_from_directory(gateway, directory, filePath)
        # Check if the file is a JSON file
        if filePath.name.endswith(".json"):
            # Load the contents of the file into a dictionary
            file_data = json.loads(file_content)
            # Add json file content into array
            data.append(file_data)

# Create a DataFrame from the data array
df = pd.DataFrame(data)
df.head()
```


