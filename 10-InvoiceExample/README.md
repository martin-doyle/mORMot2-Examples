# 10-InvoiceExample

> **Note:** This example and its database belong to the mORMot2 repository:
> [synopse/mORMot2 - ex/ThirdPartyDemos/martin-doyle/10-InvoiceExample](https://github.com/synopse/mORMot2/tree/master/ex/ThirdPartyDemos/martin-doyle/10-InvoiceExample)

An invoice/order management demo application built with the
[Synopse mORMot2](https://github.com/synopse/mORMot2) ORM framework.
The project manages customers, employees, vendors, parts inventory, shopping
carts, and customer orders for a fictional dive-equipment business.

## Project Structure

```
10-InvoiceExample/
  Data/
    dbdemos.mdb    - Original Borland DBDEMOS database (MS Access / Jet4)
    Project10.db   - SQLite3 database (mORMot2 ORM, migrated from DBDEMOS)
    rgdata.pas     - ORM model definitions (Object Pascal)
```

## Original Database: dbdemos.mdb

The `dbdemos.mdb` file is the classic **Borland DBDEMOS** sample database
(MS Access / Jet4 format) that shipped with Delphi. It contains the original
relational data for a fictional dive-equipment business. The data in
`Project10.db` was migrated from this source.

### DBDEMOS Tables

#### customer (55 rows)

| Column            | Type     | Description                      |
|:------------------|:---------|:---------------------------------|
| `CustNo`          | Number   | Primary key - customer number    |
| `Company`         | Text     | Company name                     |
| `Addr1`           | Text     | Address line 1                   |
| `Addr2`           | Text     | Address line 2                   |
| `City`            | Text     | City                             |
| `State`           | Text     | State / province                 |
| `Zip`             | Text     | Postal / ZIP code                |
| `Country`         | Text     | Country                          |
| `Phone`           | Text     | Phone number                     |
| `FAX`             | Text     | Fax number                       |
| `TaxRate`         | Number   | Tax rate (%)                     |
| `Contact`         | Text     | Contact person name              |
| `LastInvoiceDate` | Date     | Date of last invoice             |

#### orders (205 rows)

| Column          | Type     | Description                        |
|:----------------|:---------|:-----------------------------------|
| `OrderNo`       | Number   | Primary key - order number         |
| `CustNo`        | Number   | FK to `customer.CustNo`           |
| `SaleDate`      | Date     | Date of sale                       |
| `ShipDate`      | Date     | Date shipped                       |
| `EmpNo`         | Number   | FK to `employee.EmpNo`            |
| `ShipToContact` | Text     | Shipping contact name              |
| `ShipToAddr1`   | Text     | Shipping address line 1            |
| `ShipToAddr2`   | Text     | Shipping address line 2            |
| `ShipToCity`    | Text     | Shipping city                      |
| `ShipToState`   | Text     | Shipping state                     |
| `ShipToZip`     | Text     | Shipping postal code               |
| `ShipToCountry` | Text     | Shipping country                   |
| `ShipToPhone`   | Text     | Shipping contact phone             |
| `ShipVIA`       | Text     | Shipping method                    |
| `PO`            | Text     | Purchase order number              |
| `Terms`         | Text     | Payment terms                      |
| `PaymentMethod` | Text     | Payment method                     |
| `ItemsTotal`    | Currency | Total of line items                |
| `TaxRate`       | Number   | Tax rate (%)                       |
| `Freight`       | Currency | Freight charges                    |
| `AmountPaid`    | Currency | Amount paid                        |

#### items (945 rows)

Order line items (detail table for `orders`).

| Column     | Type     | Description                          |
|:-----------|:---------|:-------------------------------------|
| `OrderNo`  | Number   | FK to `orders.OrderNo`              |
| `ItemNo`   | Number   | Line item sequence number            |
| `PartNo`   | Number   | FK to `parts.PartNo`               |
| `Qty`      | Number   | Quantity ordered                     |
| `Discount` | Number   | Discount percentage                  |

#### parts (59 rows)

| Column        | Type     | Description                        |
|:--------------|:---------|:-----------------------------------|
| `PartNo`      | Number   | Primary key - part number          |
| `VendorNo`    | Number   | FK to `vendors.VendorNo`          |
| `Description` | Text     | Part description                   |
| `OnHand`      | Number   | Quantity in stock                  |
| `OnOrder`     | Number   | Quantity on order                  |
| `Cost`        | Currency | Cost price                         |
| `ListPrice`   | Currency | List / retail price                |

#### vendors (23 rows)

| Column       | Type    | Description                          |
|:-------------|:--------|:-------------------------------------|
| `VendorNo`   | Number  | Primary key - vendor number          |
| `VendorName` | Text    | Vendor company name                  |
| `Address1`   | Text    | Address line 1                       |
| `Address2`   | Text    | Address line 2                       |
| `City`       | Text    | City                                 |
| `State`      | Text    | State / province                     |
| `Zip`        | Text    | Postal / ZIP code                    |
| `Country`    | Text    | Country                              |
| `Phone`      | Text    | Phone number                         |
| `FAX`        | Text    | Fax number                           |
| `Preferred`  | Boolean | Preferred vendor flag                |

#### employee (42 rows)

| Column     | Type     | Description                          |
|:-----------|:---------|:-------------------------------------|
| `EmpNo`    | Number   | Primary key - employee number        |
| `LastName`  | Text    | Last name                            |
| `FirstName` | Text    | First name                           |
| `PhoneExt` | Text     | Phone extension                      |
| `HireDate` | Date     | Date hired                           |
| `Salary`   | Currency | Annual salary                        |

#### country (18 rows)

Reference table of countries.

| Column       | Type   | Description                           |
|:-------------|:-------|:--------------------------------------|
| `Name`       | Text   | Country name                          |
| `Capital`    | Text   | Capital city                          |
| `Continent`  | Text   | Continent                             |
| `Area`       | Number | Area in km²                           |
| `Population` | Number | Population                            |

#### Sequence Tables

| Table        | Rows | Description                                |
|:-------------|:-----|:-------------------------------------------|
| `nextcust`   | 1    | Auto-increment counter for `customer.CustNo` |
| `nextitem`   | 1    | Auto-increment counter for `items.ItemNo`    |
| `nextord`    | 1    | Auto-increment counter for `orders.OrderNo`  |

### DBDEMOS Relationships

```
vendors.VendorNo  ──<  parts.VendorNo
customer.CustNo   ──<  orders.CustNo
employee.EmpNo    ──<  orders.EmpNo
orders.OrderNo    ──<  items.OrderNo
parts.PartNo      ──<  items.PartNo
```

---

## Database: Project10.db

The SQLite3 database was migrated from `dbdemos.mdb`. The flat relational
DBDEMOS tables were restructured into mORMot2 ORM tables with **embedded JSON**
for nested data: separate address/contact/line-item columns were collapsed
into JSON objects stored in TEXT columns (e.g., the `orders` shipping fields
became a single `ShipAddress` JSON object). The `items` detail table was
absorbed into `CustomerOrder.Items` as a JSON array.

The database contains **7 application tables** (plus the internal
`sqlite_sequence` table). All application tables share a common set of audit
columns inherited from `TOrmBase`.

### Common Columns (all tables)

| Column       | Type    | Description                              |
|:-------------|:--------|:-----------------------------------------|
| `ID`         | INTEGER | Primary key (auto-increment)             |
| `Created`    | INTEGER | Creation timestamp (`TCreateTime`)       |
| `CreatedBy`  | TEXT    | User who created the record              |
| `Modified`   | INTEGER | Last-modified timestamp (`TModTime`)     |
| `ModifiedBy` | TEXT    | User who last modified the record        |
| `Version`    | INTEGER | Record version for replication (`TRecordVersion`) |

### Tables

#### Employee (42 rows)

Employees / sales personnel.

| Column       | Type  | Description                                    |
|:-------------|:------|:-----------------------------------------------|
| `EmployeeNo` | TEXT  | Unique employee number                        |
| `Contact`    | TEXT  | JSON object (`TPersonItem`) - name, phones, emails, addresses |
| `HireDate`   | INTEGER | Hire date (`TTimeLog`)                      |
| `Salary`     | FLOAT | Salary amount                                 |

#### Customer (55 rows)

Customer companies.

| Column       | Type  | Description                                    |
|:-------------|:------|:-----------------------------------------------|
| `CustomerNo` | TEXT  | Unique customer number                        |
| `Company`    | TEXT  | Company name (required, non-void)             |
| `Contacts`   | TEXT  | JSON array of `TPersonItem` - multiple contact persons with phones, emails, and addresses |

#### Vendor (23 rows)

Parts suppliers / vendors.

| Column     | Type  | Description                                      |
|:-----------|:------|:-------------------------------------------------|
| `VendorNo` | TEXT  | Unique vendor number                             |
| `Company`  | TEXT  | Company name                                     |
| `Category` | TEXT  | Vendor category code                             |
| `Contacts` | TEXT  | JSON array of `TPersonItem` - contact persons    |

#### Part (59 rows)

Product / parts catalog.

| Column        | Type    | Description                                |
|:--------------|:--------|:-------------------------------------------|
| `PartNo`      | TEXT    | Part number                                |
| `Description` | TEXT    | Part description                           |
| `Vendor`      | INTEGER | Foreign key to `Vendor.ID`                 |
| `Cost`        | FLOAT   | Cost price                                 |
| `ListPrice`   | FLOAT   | List / retail price                        |
| `OnHand`      | FLOAT   | Quantity in stock                          |
| `OnOrder`     | FLOAT   | Quantity on order from vendor              |
| `OnCarts`     | FLOAT   | Quantity currently in shopping carts       |
| `Carted`      | TEXT    | JSON array - cart references and quantities |

#### ShoppingCart (22 rows)

Pending carts not yet converted to orders.

| Column      | Type    | Description                                  |
|:------------|:--------|:---------------------------------------------|
| `OrderBook` | INTEGER | Order book reference                         |
| `Status`    | INTEGER | Cart status code                             |
| `Items`     | TEXT    | JSON array of `TItem` - line items (part no, description, list price, quantity, discount, position) |
| `Customer`  | INTEGER | Foreign key to `Customer.ID`                 |

#### CustomerOrder (205 rows)

Completed customer orders / invoices.

| Column        | Type    | Description                                |
|:--------------|:--------|:-------------------------------------------|
| `OrderNo`     | TEXT    | Unique order number                        |
| `SaleDate`    | INTEGER | Sale date (`TTimeLog`)                     |
| `ShipDate`    | INTEGER | Ship date (`TTimeLog`)                     |
| `Customer`    | INTEGER | Foreign key to `Customer.ID`               |
| `Seller`      | INTEGER | Foreign key to `Employee.ID`               |
| `Items`       | TEXT    | JSON array of `TItem` - line items         |
| `ItemsTotal`  | FLOAT   | Calculated order total                     |
| `ShipAddress` | TEXT    | JSON object (`TAddressItem`) - shipping address |
| `ShipContact` | TEXT    | JSON object (`TPersonItem`) - shipping contact |
| `AmountPaid`  | FLOAT   | Amount paid so far                         |

#### TableDeleted (0 rows)

Internal mORMot table tracking deleted record IDs for replication.

## JSON Data Structures

The database stores complex nested data as JSON in TEXT columns.

**TPersonItem** (contact person):
```json
{
  "FirstName": "Roberto",
  "MiddleName": "",
  "LastName": "Nelson",
  "Phones": ["250"],
  "Emails": [],
  "Addresses": [
    {
      "Street1": "4-976 Sugarloaf Hwy",
      "Street2": "Suite 103",
      "Code": "94766-1234",
      "City": "Kapaa Kauai",
      "CityArea": "",
      "Region": "HI",
      "Country": "US"
    }
  ]
}
```

**TAddressItem** (address):
```json
{
  "Street1": "1 Neptune Lane",
  "Street2": "",
  "Code": "",
  "City": "Kato Paphos",
  "CityArea": "",
  "Region": "",
  "Country": "Cyprus"
}
```

**TItem** (order/cart line item):
```json
{
  "PartNo": "1313",
  "Description": "Regulator System",
  "ListPrice": 250,
  "Quantity": 5,
  "Discount": 0,
  "Position": 0
}
```

## ORM Model (rgdata.pas)

The Pascal unit `rgdata.pas` defines the mORMot2 ORM classes. The ORM model
registered via `CreateModel` includes three tables:

- **TOrmEmployee** - employee records with embedded `TPersonItem` contact
- **TOrmCustomer** - customers with a collection of `TPersonItem` contacts
- **TOrmCustomerOrder** - orders linking customers and employees, with line
  items, shipping address, and shipping contact

Additional tables in the database (`Part`, `Vendor`, `ShoppingCart`) are
defined in other units of the full project.

### Class Hierarchy

```
TOrm
  TOrmBase            (audit fields: Created, CreatedBy, Modified, ModifiedBy, Version)
    TOrmEmployee      (EmployeeNo, Contact, HireDate, Salary)
    TOrmCustomer      (CustomerNo, Company, Contacts)
    TOrmCustomerOrder (OrderNo, SaleDate, ShipDate, Customer, Seller, Items, ...)
```

### Key Design Patterns

- **Embedded JSON objects** - Complex types (`TPersonItem`, `TAddressItem`,
  `TItemCollection`) are serialized as JSON into TEXT columns, avoiding
  extra join tables.
- **TRecordVersion** - Enables record-level versioning for synchronization
  and replication.
- **TCreateTime / TModTime** - Automatic timestamp tracking via mORMot's
  `TTimeLog` encoding.
- **AS_UNIQUE** - `EmployeeNo`, `CustomerNo`, and `OrderNo` are declared as
  unique fields.
- **AddFilterNotVoidText** - Validation ensuring that `Company`, `EmployeeNo`,
  and `OrderNo` are never empty.

## License

MIT License - Copyright (c) 2017-2025 Martin Doyle
