# ERPNext Mobile Store Setup

This repository contains the ERPNext/Frappe Docker setup for the Mobile Store project, including the custom **Mobile store Workspace**.

## What is already configured

The following custom ERPNext Workspace has already been created:

**Mobile store**

It contains:

### 📦 Inventory

* Items
* Item Attribute
* Item Prices
* Warehouses
* Stock Entry
* Serial No / IMEI

### 🛒 Sales

* Sales Invoice
* Customers

### 📥 Purchasing

* Purchase Receipt
* Purchase Order
* Supplier

### 📊 Reports

* Sales Register
* Gross Profit
* Purchase Analytics
* Stock Ledger
* Stock Balance
* Item-wise Sales Register

### Dashboard

* Total Active Items
* Active Suppliers
* Stock Value by Item Group
* Welcome header

The Workspace configuration is exported as:

```text
mobile_store_workspace.json
```

---

# VPS Deployment Instructions

## 1. Clone the repository

Clone this repository on the VPS:

```bash
git clone https://github.com/rishavvv11/erpppp.git
cd erpppp
```

## 2. Configure the environment

Use the existing Docker configuration in the repository.

Make sure the ERPNext/Frappe version and environment variables are correctly configured before starting the containers.

Do **not** create a new ERPNext customization from scratch.

---

## 3. Start ERPNext

Start the Docker Compose services using the repository's compose configuration.

```bash
docker compose -f compose.yaml -f compose.custom.yaml up -d
```

Check the containers:

```bash
docker compose ps
```

Make sure the backend, frontend, database, Redis and worker services are running.

---

# 4. Find the ERPNext site

The site used during development was:

```text
erp.localhost
```

The site directory should be:

```text
/home/frappe/frappe-bench/sites/erp.localhost
```

Verify it with:

```bash
docker exec frappe-backend-1 ls -la /home/frappe/frappe-bench/sites
```

---

# 5. Apply the Mobile Store Workspace

The repository contains:

```text
mobile_store_workspace.json
```

This file contains the Workspace configuration.

After the ERPNext site is running, import/apply this JSON to the VPS site.

First copy the file into the backend container:

```bash
docker cp mobile_store_workspace.json frappe-backend-1:/tmp/mobile_store_workspace.json
```

Then enter the ERPNext console:

```bash
docker exec -it frappe-backend-1 bench --site erp.localhost console
```

Verify that the Workspace does not already exist:

```python
frappe.db.exists("Workspace", "Mobile store")
```

If it returns `None`, import the Workspace JSON.

Use the JSON file as the source of the Workspace configuration rather than manually recreating the Workspace.

After importing, verify:

```python
frappe.get_all(
    "Workspace",
    filters={"name": "Mobile store"},
    fields=["name", "title"]
)
```

Expected result:

```text
[{'name': 'Mobile store', 'title': 'Mobile store'}]
```

Then exit:

```python
exit()
```

---

# 6. Clear cache

After applying the Workspace:

```bash
docker exec frappe-backend-1 bench --site erp.localhost clear-cache
```

Restart the services if necessary:

```bash
docker compose restart
```

---

# 7. Verify the Workspace

Open ERPNext and log in.

The **Mobile store** Workspace should contain:

```text
Welcome to the Mobile store workspace

📦 INVENTORY
    Items
    Item Attribute
    Item Prices
    Warehouses
    Stock Entry
    Serial No / IMEI

🛒 SALES
    Sales Invoice
    Customers

📥 PURCHASING
    Purchase Receipt
    Purchase Order
    Supplier

📊 REPORTS
    Sales Register
    Gross Profit
    Purchase Analytics
    Stock Ledger
    Stock Balance
    Item-wise Sales Register
```

The dashboard should also show:

```text
Total Active Items
Active Suppliers
Stock Value by Item Group
```

---

# Important: Test Data

The Workspace JSON contains **configuration only**.

It does NOT contain the development database data.

Therefore, the VPS will NOT automatically contain:

* Development Items
* Development Suppliers
* Development Customers
* Development Stock
* Development Sales
* Development Purchase transactions

The number cards and reports will use the data that exists on the VPS.

For example:

```text
Total Active Items
```

will count the Items created on the VPS.

---

# Important: Do Not Modify ERPNext Source Code

The standard Frappe and ERPNext source code does not need to be copied into this repository as a customization.

The custom project files are:

```text
compose.custom.yaml
mobile_store_workspace.json
```

The repository also contains the normal Frappe Docker project files required for deployment.

Do not manually copy files from:

```text
/home/frappe/frappe-bench/apps/frappe
/home/frappe/frappe-bench/apps/erpnext
```

into the repository just because they exist inside the Docker container.

---

# Current Site Information

Development site:

```text
erp.localhost
```

ERPNext version used during development:

```text
v16
```

The actual version should be confirmed from the Docker configuration before VPS deployment.

---

# Deployment Goal

The final VPS setup should provide:

```text
                    VPS
                     │
             ┌───────┴───────┐
             │   ERPNext     │
             │   + Frappe    │
             └───────┬───────┘
                     │
             Mobile Store
               Workspace
                     │
       ┌─────────────┼─────────────┐
       │             │             │
   Inventory       Sales       Purchasing
       │             │             │
       └─────────────┼─────────────┘
                     │
                  Reports
```

After ERPNext is successfully deployed, the POS application can connect to the VPS ERPNext instance through the ERPNext API.

## Final Checklist

* [ ] Clone repository
* [ ] Configure VPS environment
* [ ] Start Docker services
* [ ] Confirm `erp.localhost` site exists
* [ ] Apply `mobile_store_workspace.json`
* [ ] Clear ERPNext cache
* [ ] Verify Mobile store Workspace
* [ ] Verify Inventory links
* [ ] Verify Sales links
* [ ] Verify Purchasing links
* [ ] Verify Reports
* [ ] Confirm ERPNext API is accessible
* [ ] Connect the POS application to the VPS ERPNext API
