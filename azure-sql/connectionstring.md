Typically there are two options how to develop locally. The first connection string is for Microsoft SQL Server and the second one is for SQL Server Express.


```powershell
"Server=.;Database={DATABASE_NAME};Trusted_Connection=True;MultipleActiveResultSets=true;TrustServerCertificate=True;"
```
- `Server=.` specifies the server name, "." means the current machine.
- `Database={DATABASE_NAME}` specifies the name of the database to be used.
- `Trusted_Connection=True` means that the connection will be made using Windows Authentication.
- `MultipleActiveResultSets=true` enables the use of multiple result sets in a single query.
- `TrustServerCertificate=True` allows the connection to be made even if the server certificate cannot be verified.

```powershell
"Data Source=.\\SQLExpress;Initial Catalog={DATABASE_NAME};Integrated Security=True;TrustServerCertificate=True"
```
- `Data Source=.\\SQLExpress` specifies the server name and instance name of the SQL Server Express.
- `Initial Catalog={DATABASE_NAME}` specifies the name of the database to be used.
- `Integrated Security=True` means that the connection will be made using Windows Authentication.
- `TrustServerCertificate=True` allows the connection to be made even if the server certificate cannot be verified.

In general, the main difference between the two connection strings is that the first one is for the full version of SQL Server and the second one is for SQL Server Express.
Also, the first one uses `Trusted_Connection=True` and the second one uses `Integrated Security=True` for the same purpose.
