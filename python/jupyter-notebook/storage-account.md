
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


