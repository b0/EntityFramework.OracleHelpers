# EntityFramework.OracleHelpers
### Collection of Entity Framework Conventions implementing Oracle uppercase naming conventions.

OracleHelpers comes with uppercase naming conventions for columns, tables and foreign keys. Also there is convention for properties of type string.

## Installation

Install via [NuGet](https://www.nuget.org/packages/EntityFramework.OracleHelpers/):

    PM> Install-Package EntityFramework.OracleHelpers


## Usage

Add using to your *DbContext* class and apply conventions:
```csharp
  using EntityFramework.OracleHelpers;

  protected override void OnModelCreating(DbModelBuilder modelBuilder)
  {
        this.ApplyAllConventionsIfOracle(modelBuilder);
  }
```
That's it. One line and all default conventions will be applied if your connectionstring point to Oracle database.

## Conventions

OracleHelpers comes with following conventions:
* UpperCaseColumnNameConvention
* UpperCaseTableNameConvention
* UpperCaseForeignKeyNameConvention
* StringPropertyMaxLengthConvention

All UpperCase*NameConvention
convert all names from default C# [PascalCase](https://msdn.microsoft.com/en-us/library/ms229043(v=vs.110).aspx) to Oracle [UpperCase](https://docs.oracle.com/cd/B19306_01/server.102/b14200/sql_elements008.htm).
Basically from your class:
```csharp
  public class Product
  {
      public int Id { get; set; }
      public string ProductName { get; set; }
      public DateTime OnSaleFrom { get; set; }
  }
```

You will get table:

| PRODUCTS      | 
| ------------- |
| ID			      | 
| PRODUCT_NAME  |
| ON_SALE_FROM  |


> **Note:**
>
> All UpperCase*NameConvention defaults to uppercase with underscore separated words
> but that can be overriden by constructor parameter.

### StringPropertyMaxLengthConvention
As per Oracle [documentation](https://docs.oracle.com/cd/E63277_01/win.121/e63268/entityCodeFirst.htm#ODPNT8310) .NET Data Type String will be represented as Oracle NCLOB if *MaxLength* attribute is not set. StringPropertyMaxLengthConvention sets *MaxLength* to 2000 for all strings so you'll get *NVARCHAR2(2000)* column for your strings.

If needed you can override length per case if needed. So:
```csharp
  public class User
  {
      public int Id { get; set; }
      public string FirstName { get; set; }
      [StringLength(100)]
      public string MiddleName { get; set; }
      [StringLength(7000)]
      public string LastName { get; set; }
  }
```

Should look like this:

| USERS      	| TYPE 	 		 |
| ------------- |----------------|
| ID			| 				 |
| FIRST_NAME  	| NVARCHAR2(2000)|
| MIDDLE_NAME  	| NVARCHAR2(100) |
| LAST_NAME		| NCLOB			 |

## Examples
Apply all conventions with default values if *ProviderInvariantName* for current connection is *Oracle.ManagedDataAccess.Client* otherwise does nothing:
```csharp
  protected override void OnModelCreating(DbModelBuilder modelBuilder)
  {
      this.ApplyAllConventionsIfOracle(modelBuilder);

      base.OnModelCreating(modelBuilder);
  }
```       

You can check by yourself if on Oracle and apply some of conventions or change defaults:
```csharp
  protected override void OnModelCreating(DbModelBuilder modelBuilder)
  {
      if (this.IsOracle())
      {
          modelBuilder.ApplyAllUpperCaseConventions(convertToUnderscore: false);
          modelBuilder.ApplyStringMaxLengthConvention();
      }

      base.OnModelCreating(modelBuilder);
  }
```        
Ofcourse, you could apply those conventions even if you are not on Oracle database. 
