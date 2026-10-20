# Telecom Order Dispatcher (UiPath)

A UiPath **Dispatcher** bot that reads new telecom service orders from Excel and loads them into an **Orchestrator queue**. It is the first half of a **Dispatcher / Performer** automation for telecom order management and service provisioning.

## What it does

1. Reads the `Orders` sheet from `Data/Input/Orders.xlsx` into a DataTable.
2. Logs how many orders were found.
3. Loops through each order and adds it to the `Telecom_Orders` queue in Orchestrator, with the order details as **Specific Content**.
4. Uses **OrderID** as the queue item **Reference**, so the same order can't be queued twice.
5. Wraps each item in a **Try Catch**, so one bad row is logged without stopping the whole run.

```
Orders.xlsx  ──►  Dispatcher bot  ──►  Orchestrator queue: Telecom_Orders  ──►  Performer bot (REFramework)
```

## Why Dispatcher / Performer

- **Scalable:** several Performer robots can work the same queue in parallel.
- **Reliable:** a failed order is retried on its own, without rerunning the whole batch.
- **Traceable:** every order has a status and history in Orchestrator (New → In Progress → Successful / Failed).
- **No duplicates:** unique references block the same OrderID from being processed twice.

## Tech used

- UiPath Studio (Windows, VB)
- UiPath Orchestrator: Queues, folders
- Excel automation with Workbook activities (no Excel installation needed)
- Exception handling with Try Catch and structured logging

**Packages:** `UiPath.Excel.Activities`, `UiPath.System.Activities`

## Project structure

```
Telecom_Order_Dispatcher/
├── Main.xaml               # Dispatcher workflow
├── project.json            # Project settings and dependencies
└── Data/
    └── Input/
        └── Orders.xlsx     # Sample orders (fake data)
```

## How to run

1. Clone the repo and open `project.json` in UiPath Studio.
2. Connect Studio to your Orchestrator tenant.
3. In Orchestrator, create a queue named **`Telecom_Orders`** in the **Shared** folder. Turn on *Enforce unique references* (optional).
4. Make sure `Data/Input/Orders.xlsx` has an `Orders` sheet with the expected column headers.
5. Click **Debug File** or **Run**.
6. Check Orchestrator → Queues → `Telecom_Orders`. You should see one **New** item per order.

## Sample input

| OrderID | CustomerName | ServiceType | ServiceAdress | Status |
|---|---|---|---|---|
| 1001 | John Smith | Internet | 100 Main St, Dallas, TX | New |
| 1002 | Maria Lopez | TV | 22 Oak Ave, Plano, TX | New |

All data in this repo is fake sample data.

## Next step

**Telecom Order Performer:** a REFramework bot that takes each queue item, checks business rules (valid service type, address present), provisions the service, and marks the item Successful or Failed (business vs. system exceptions).

## Author

Veysel "Jason" Akyol, RPA Developer / Agentic AI
UiPath Certified Professional Automation Developer Associate · UiPath Agentic Automation Developer Associate# Telecom_Order_Dispatcher
UiPath Dispatcher bot that reads telecom orders from Excel and loads them into an Orchestrator queue with unique references and exception handling — part of a Dispatcher/Performer automation.
