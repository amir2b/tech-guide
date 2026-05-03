# Normalization

Sample **Orders Table**

| OrderID | CustomerName | CustomerCity | ProductInfo     |
| :------ | :----------- | :----------- | :-------------- |
| 101     | John Smith   | Boston       | Laptop, Mouse   |
| 102     | Jane Doe     | Miami        | Monitor, Monitor |
| 103     | John Smith   | Boston       | Keyboard, Mouse Pad |

## 1. First Normal Form (1NF)

"Be atomic." No repeating fields or groups.

**Orders Table** (1NF)

| OrderID | CustomerName | CustomerCity | Product   |
| :------ | :----------- | :----------- | :-------- |
| 101     | John Smith   | Boston       | Laptop    |
| 101     | John Smith   | Boston       | Mouse     |
| 102     | Jane Doe     | Miami        | Monitor   |
| 102     | Jane Doe     | Miami        | Monitor   |
| 103     | John Smith   | Boston       | Keyboard  |
| 103     | John Smith   | Boston       | Mouse Pad |

## 2. Second Normal Form (2NF)

"Have one job." All non-key attributes must describe the full key.

**Table A: OrderItems** (Focuses on the products in the order)

| OrderID | Product   | Quantity |
| :------ | :-------- | :------- |
| 101     | Laptop    | 1        |
| 101     | Mouse     | 1        |
| 102     | Monitor   | 2        |
| 103     | Keyboard  | 1        |
| 103     | Mouse Pad | 1        |

**Table B: Orders** (Focuses on the order itself)

| OrderID | CustomerName | CustomerCity |
| :------ | :----------- | :----------- |
| 101     | John Smith   | Boston       |
| 102     | Jane Doe     | Miami        |
| 103     | John Smith   | Boston       |

## 3. Third Normal Form (3NF)

"Depend only on the key." Non-key attributes must not depend on other non-key attributes.

**Table A: OrderItems** (Unchanged)

| OrderID | Product   | Quantity |
| :------ | :-------- | :------- |
| 101     | Laptop    | 1        |
| 101     | Mouse     | 1        |
| 102     | Monitor   | 2        |
| 103     | Keyboard  | 1        |
| 103     | Mouse Pad | 1        |

**Table B: Orders** (Now links to a Customer)

| OrderID | CustomerID |
| :------ | :--------- |
| 101     | 1          |
| 102     | 2          |
| 103     | 1          |

**Table C: Customers** (The new table)

| CustomerID | CustomerName | CustomerCity |
| :--------- | :----------- | :----------- |
| 1          | John Smith   | Boston       |
| 2          | Jane Doe     | Miami        |
