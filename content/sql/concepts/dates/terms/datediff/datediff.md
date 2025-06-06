---
Title: 'DATEDIFF()'
Description: 'Calculates the difference between two date or timestamp values and returns the result as an integer.'
Subjects:
  - 'Computer Science'
  - 'Web Development'
Tags:
  - 'Database'
  - 'Date'
  - 'Functions'
  - 'SQL'
CatalogContent:
  - 'learn-sql'
  - 'paths/analyze-data-with-sql'
---

The **`DATEDIFF()`** function calculates the difference between two date or timestamp values and returns the result as an integer in a specified unit of time. This powerful function allows developers and analysts to easily measure time intervals between dates, which is essential for reporting, data analysis, and application development.

`DATEDIFF()` serves as a cornerstone for date-based calculations in SQL Server, enabling users to perform operations like calculating ages, measuring durations of events, determining time elapsed between transactions, and creating date-based business metrics. Its versatility makes it invaluable for virtually any application that deals with temporal data.

## Syntax

```pseudo
DATEDIFF(interval, date1, date2)
```

**Parameters:**

- `interval`: The time unit in which the difference will be calculated. Valid values include:
  - `year`, `yy`, `yyyy`: Years
  - `quarter`, `qq`, `q`: Quarters
  - `month`, `mm`, `m`: Months
  - `dayofyear`, `dy`, `y`: Day of the year
  - `day`, `dd`, `d`: Days
  - `week`, `wk`, `ww`: Weeks
  - `hour`, `hh`: Hours
  - `minute`, `mi`, `n`: Minutes
  - `second`, `ss`, `s`: Seconds
  - `millisecond`, `ms`: Milliseconds
  - `microsecond`, `mcs`: Microseconds
  - `nanosecond`, `ns`: Nanoseconds
- `date1`: The start date for the calculation. Can be a date, datetime, datetime2, smalldatetime, or time data type, or an expression that resolves to one of these types.
- `date2`: The end date for the calculation. Can be a date, datetime, datetime2, smalldatetime, or time data type, or an expression that resolves to one of these types.

**Return value:**

The `DATEDIFF()` function returns an integer representing the number of time units (specified by the interval parameter) between date1 and date2.

## Example 1: Basic Date Difference Calculation

This example demonstrates how to calculate the difference between two dates in various time intervals:

```sql
-- Calculate difference between two dates in years, months, and days
SELECT
  DATEDIFF(year, '2020-01-15', '2023-09-20') AS YearDiff,
  DATEDIFF(month, '2020-01-15', '2023-09-20') AS MonthDiff,
  DATEDIFF(day, '2020-01-15', '2023-09-20') AS DayDiff;
```

Output produced by this code will be:

| YearDiff | MonthDiff | DayDiff |
| -------- | --------- | ------- |
| 3        | 44        | 1344    |

This example calculates the difference between January 15, 2020, and September 20, 2023, in years, months, and days. The results show there are 3 years, 44 months, or 1344 days between these dates.

## Example 2: Calculating Age in Years

This example demonstrates how to use `DATEDIFF()` to calculate a person's age in years from their birthdate.

```sql
-- Create a sample table with employee data
CREATE TABLE Employees (
  EmployeeID INT PRIMARY KEY,
  FirstName VARCHAR(50),
  LastName VARCHAR(50),
  BirthDate DATE,
  HireDate DATE
);

-- Insert sample data
INSERT INTO Employees (EmployeeID, FirstName, LastName, BirthDate, HireDate)
VALUES
  (1, 'John', 'Smith', '1985-06-15', '2010-03-20'),
  (2, 'Sarah', 'Johnson', '1992-11-30', '2015-07-10'),
  (3, 'Michael', 'Brown', '1978-02-23', '2005-09-15');

-- Calculate ages as of current date
SELECT
  EmployeeID,
  FirstName + ' ' + LastName AS EmployeeName,
  BirthDate,
  DATEDIFF(year, BirthDate, GETDATE()) AS Age
FROM
  Employees
ORDER BY
  Age DESC;
```

The output generated by this code will be:

| EmployeeID | EmployeeName  | BirthDate  | Age |
| ---------- | ------------- | ---------- | --- |
| 3          | Michael Brown | 1978-02-23 | 47  |
| 1          | John Smith    | 1985-06-15 | 40  |
| 2          | Sarah Johnson | 1992-11-30 | 33  |

This example shows how to calculate an employee's age by finding the difference in years between their birthdate and the current date.

> **Note:** This calculation provides the raw year difference and doesn't account for whether the birthday has occurred yet in the current year.

## Example 3: Business Metrics with `DATEDIFF()`

This example demonstrates how to use `DATEDIFF()` for business reporting metrics, such as calculating order processing times and identifying delayed shipments.

```sql
-- Create sample orders table
CREATE TABLE Orders (
  OrderID INT PRIMARY KEY,
  CustomerID INT,
  OrderDate DATETIME,
  ShipDate DATETIME,
  DeliveryDate DATETIME
);

-- Insert sample data
INSERT INTO Orders (OrderID, CustomerID, OrderDate, ShipDate, DeliveryDate)
VALUES
  (1001, 101, '2023-01-10 09:30:00', '2023-01-11 14:15:00', '2023-01-15 11:20:00'),
  (1002, 102, '2023-01-12 13:45:00', '2023-01-13 10:30:00', '2023-01-14 16:45:00'),
  (1003, 103, '2023-01-15 11:20:00', '2023-01-18 09:45:00', '2023-01-22 13:10:00'),
  (1004, 104, '2023-01-16 14:55:00', '2023-01-17 16:30:00', '2023-01-21 09:30:00'),
  (1005, 105, '2023-01-18 10:15:00', NULL, NULL);

-- Calculate processing, shipping, and total handling times
SELECT
  OrderID,
  OrderDate,
  ShipDate,
  DeliveryDate,
  -- Processing time (from order to shipment)
  DATEDIFF(hour, OrderDate, ShipDate) AS ProcessingHours,
  -- Shipping time (from shipment to delivery)
  DATEDIFF(day, ShipDate, DeliveryDate) AS ShippingDays,
  -- Total time (from order to delivery)
  DATEDIFF(day, OrderDate, DeliveryDate) AS TotalDays,
  -- Identify delayed shipments (processing > 24 hours)
  CASE
    WHEN DATEDIFF(hour, OrderDate, ShipDate) > 24 THEN 'Delayed'
    ELSE 'On Time'
  END AS ShipmentStatus
FROM
  Orders
WHERE
  ShipDate IS NOT NULL;
```

The output of this code will be:

| OrderID | OrderDate           | ShipDate            | DeliveryDate        | ProcessingHours | ShippingDays | TotalDays | ShipmentStatus |
| ------- | ------------------- | ------------------- | ------------------- | --------------- | ------------ | --------- | -------------- |
| 1001    | 2023-01-10 09:30:00 | 2023-01-11 14:15:00 | 2023-01-15 11:20:00 | 29              | 4            | 5         | Delayed        |
| 1002    | 2023-01-12 13:45:00 | 2023-01-13 10:30:00 | 2023-01-14 16:45:00 | 21              | 1            | 2         | On Time        |
| 1003    | 2023-01-15 11:20:00 | 2023-01-18 09:45:00 | 2023-01-22 13:10:00 | 70              | 4            | 7         | Delayed        |
| 1004    | 2023-01-16 14:55:00 | 2023-01-17 16:30:00 | 2023-01-21 09:30:00 | 26              | 4            | 5         | Delayed        |

This example demonstrates how `DATEDIFF()` can be used to calculate important business metrics for order processing. The query calculates the processing time in hours, shipping time in days, and total handling time in days. It also identifies delayed shipments based on processing times exceeding 24 hours.

## Frequently Asked Questions

### 1. How to calculate the date difference between two dates in SQL?

In SQL Server, use the `DATEDIFF()` function with an appropriate interval parameter like day, month, or year. For example, `DATEDIFF(day, '2023-01-01', '2023-01-15')` will return 14 days.

### 2. Does `DATEDIFF()` include both the start and end dates in its calculation?

The `DATEDIFF()` function counts the number of interval boundaries crossed between the two dates. For example, when using 'day', it counts the number of midnight boundaries crossed, not the full 24-hour periods.

### 3. Why does `DATEDIFF(year, '2022-12-31', '2023-01-01')` return 1 even though it's just one day apart?

Because `DATEDIFF()` counts calendar boundaries, not complete intervals. Since the dates span across a year boundary, it returns 1 year, even though the difference is only one day.

### 4. Does `DATEDIFF()` take time zones into account?

No, SQL Server's `DATEDIFF()` does not account for time zones or daylight saving time transitions. All calculations are done in the server's local time zone.

### 5. Can I use `DATEDIFF()` with time-only values?

Yes, you can use time data types with `DATEDIFF()`, but only with time-related intervals like seconds, minutes, and hours. Using day or larger intervals with time-only values will always return 0.
