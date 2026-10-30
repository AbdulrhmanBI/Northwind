# Data model

Source file: [`data/Northwind.xlsx`](../data/Northwind.xlsx) (sheets are loaded as separate tables).

## Tables

| Table | Rows | Key columns | Notes |
|---|---:|---|---|
| `Customer` | 91 | `Id`, `FirstName`, `LastName`, `City`, `Country`, `Phone` | 21 countries |
| `Orders` | 830 | `Id`, `OrderDate`, `OrderNumber`, `CustomerId`, `TotalAmount` | 2012-07-04 to 2014-05-06 |
| `OrderItem` | 2,155 | `Id`, `OrderId`, `ProductId`, `UnitPrice`, `Quantity` | order lines |
| `Product` | 78 | `Id`, `ProductName`, `SupplierId`, `UnitPrice`, `Package`, `IsDiscontinued` | `IsDiscontinued`: `-1` = discontinued, `0` = active |
| `Supplier` | 29 | `Id`, `CompanyName`, `ContactName`, `ContactTitle`, `City`, `Country`, `Phone`, `Fax` | 16 countries |

(`Sheet1` in the workbook is empty and is not used.)

## Relationships

```
Customer.Id     = Orders.CustomerId
Orders.Id       = OrderItem.OrderId
OrderItem.ProductId = Product.Id
Product.SupplierId  = Supplier.Id
```

## Calculated fields and parameters

| Name | Type | Definition | Used for |
|---|---|---|---|
| `Sales Amount` | Calculated field | `[UnitPrice] * [Quantity]` | Sales KPI, map, top lists, monthly sales |
| `AVG ORDER VALUE` | Calculated field | `SUM([TotalAmount]) / COUNTD([Id (Orders)])` | Avg Order KPI |
| `Product Status` | Calculated field | `IF [IsDiscontinued] = -1 THEN "Discontinued" ELSEIF [IsDiscontinued] = 0 THEN "Active" ELSE "Check Value" END` | Portfolio mix, price distribution, colour |
| `Cal` | Calculated field | `COUNTD(IF [OrderDate] >= #2013-05-07# THEN [CustomerId] END)` | "Active in last 12M" under the Customers KPI |
| `Weighted Avg Selling Price` | Calculated field | `SUM(<line-level helper field>) / SUM([Quantity])` | Product performance |
| `Top N` | Parameter (integer, default 10) | - | Controls the Top Customers / Suppliers / Products charts |

> `Cal` uses a fixed cut-off date (`2013-05-07`, twelve months before the last order). If the data is refreshed with newer orders, update this date.

## Dashboards

| Dashboard | Worksheets |
|---|---|
| **OVERVIEW** | Total Sales, Orders, Units Sold, Avg Order, Customers (KPIs), Global Sales (map), Monthly Sales, Product Portfolio Mix, Top Customers, Top Suppliers |
| **Product Analysis** | Total Sales, Units Sold, Product, Discontinued, Active (KPIs), Top Products, Product Performance, Monthly Product Demand, Product Price Distribution |

Filters are driven by **Country** and **Product Name** controls plus the **Top N** parameter, and by dashboard filter actions between sheets.
