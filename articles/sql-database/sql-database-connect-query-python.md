---
title: Connect to Azure SQL Database by using Python | Microsoft Docs
description: Presents a Python code sample you can use to connect to and query Azure SQL Database.
services: sql-database
documentationcenter: ''
author: meet-bhagdev
manager: jhubbard
editor: ''

ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/17/2017
ms.author: meetb;carlrab;sstein

---
# Azure SQL Database: Use Python to connect and query data

 This quick start demonstrates how to use Use [Python](https://python.org) to connect to an Azure SQL database, and then use Transact-SQL statements to query, insert, update, and delete data in the database from Mac OS, Ubuntu Linux, and Windows platforms.

This quick start uses as its starting point the resources created in one of these quick starts:

- [Create DB - Portal](sql-database-get-started-portal.md)
- [Create DB - CLI](sql-database-get-started-cli.md)

## Install the Python and database communication libraries

### **Mac OS**
Open your terminal and navigate to a directory where you plan on creating your python script. Enter the following commands to install **brew**, **Microsoft ODBC Driver for Mac** and **pyodbc**. pyodbc uses the Microsoft ODBC Driver on Linux to connect to SQL Databases.

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### **Linux (Ubuntu)**
Open your terminal and navigate to a directory where you plan on creating your python script. Enter the following commands to install the **Microsoft ODBC Driver for Linux** and **pyodbc**. pyodbc uses the Microsoft ODBC Driver on Linux to connect to SQL Databases.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

### **Windows**
Install the [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339) (upgrade the driver if prompted). Pyodbc uses the Microsoft ODBC Driver on Linux to connect to SQL Databases. 

Then install **pyodbc** using pip.

```cmd
pip install pyodbc==3.1.1
```

Instructions to enable the use pip can be found [here](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)

## Get connection information

Use the information in the following steps, if needed, to get the connection information for your Azure SQL Database server and database. You need this information to connect and query your Azure SQL database using Python. 

1. Log in to the [Azure portal](https://portal.azure.com/).
2. Select **SQL Databases** from the left-hand menu, and click your database on the **SQL databases** page. 
3. On the **Overview** page for your database, review the fully qualified server name as shown in the image below. You can hover over the server name to bring up the **Click to copy** option. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. If you have forgotten the login information for your Azure SQL Database server, navigate to the SQL Database server page to view the server admin name and, if necessary, reset the password.     
   
## Select Data

Use the following code to query your Azure SQL database using the [pyodbc.connect]((https://github.com/mkleehammer/pyodbc/wiki)) function with a [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL statement. The [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) function is used to retrieve a result set from a query against SQL Database. This function accepts a query and returns a result set that can be iterated over with the use of [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html). Replace the server, database, username, and password parameters with the values that you specified when you created the database with the AdventureWorksLT sample data.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

## Insert data
Use the following code to insert a new product into the SalesLT.Product table in the specified database using the [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) function and the [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL statement. Replace the server, database, username, and password parameters with the values that you specified when you created the database with the AdventureWorksLT sample data.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
	print ('Successfuly Inserted!')
cnxn.commit()
```

## Update data
Use the following code to update data in your Azure SQL database using the [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) function and the [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL statement. Replace the server, database, username, and password parameters with the values that you specified when you created the database with the AdventureWorksLT sample data.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
	print ('Successfuly Updated!')
cnxn.commit()

```

## Delete data
Use the following code to delete data in your Azure SQL database using the [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) function and the [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL statement. Replace the server, database, username, and password parameters with the values that you specified when you created the database with the AdventureWorksLT sample data.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## Next steps

- More information on the [Microsoft Python Driver for SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/).
- Visit the [Python Developer Center](/develop/python/).
- To connect and query using SQL Server Management Studio, see [Connect and query with SSMS](sql-database-connect-query-ssms.md)
- To connect and query using Visual Studio, see [Connect and query with Visual Studio Code](sql-database-connect-query-vscode.md).
- To connect and query using .NET, see [Connect and query with .NET](sql-database-connect-query-dotnet.md).
- To connect and query using PHP, see [Connect and query with PHP](sql-database-connect-query-php.md).
- To connect and query using Node.js, see [Connect and query with Node.js](sql-database-connect-query-nodejs.md).
- To connect and query using Java, see [Connect and query with Java](sql-database-connect-query-java.md).
- To connect and query using Ruby, see [Connect and query with Ruby](sql-database-connect-query-ruby.md).
