# MyHouse Showcase

This repository provides an overview of MyHouse, an inventory control web app for tracking current stock and shipments.

---

## 📽 Video Presentation
Check out the live demonstration of the Django web app:  
🎥 **[Watch the Demo](https://www.youtube.com/watch?v=WvH4TcxvG14)**

---

## 🌟 Features
- **Feature 1: Login and User Authentication ([timestamp](https://youtu.be/WvH4TcxvG14?t=18))**: Created using Django LoginRequiredMixin and UserCreationForm
- **Feature 2: Item Query ([Search Timestamp](https://youtu.be/WvH4TcxvG14?t=103), [Adding Items Timestamp](https://youtu.be/WvH4TcxvG14?t=141))**: Search items, add new items to the warehouse, update item details.
- **Feature 3: Shipment Logic([New Shipment Timestamp](https://youtu.be/WvH4TcxvG14?t=217), [Pending Shipments Timestamp](https://youtu.be/WvH4TcxvG14?t=256), [Sending Status Updates Timestamp (starting here)](https://youtu.be/WvH4TcxvG14?t=316))**: Functionality for shipments, such as sending them out, checking status, and deleting shipments.
- Items and Shipments made using Django models, forms, and views.

---

## 🌟 Database Modeling Choices
This section documents the database schema design and reasoning for a Django backend implementation of key features. Each feature represents a database entity or relationship critical to the functionality of the system.

---

### 1️⃣ Feature: Warehouse
**Purpose**: To store and manage warehouse data.

**Attributes**:
- `warehouse_name` (string): Name of the warehouse.
- `warehouse_location` (string): Location of the warehouse.
- `user` (foreign key to `django.contrib.auth.models.User`): Each warehouse is associated with one authenticated user.

**Reasoning**:
- In the current implementation, one user is linked to one warehouse upon account creation in a **one-to-one** relationship. Future implementations may feature a **one-to-many** relationship, where one user can track multiple warehouses.
- The `django.contrib.auth.models.User` model’s built-in primary key is used to uniquely identify each user.

---

### 2️⃣ Feature: Item
**Purpose**: To represent an individual item in the overarching system. Not necessarily linked to a warehouse yet.

**Attributes**:
- `item_name` (string): The name of the item.
- `itemID` (string, primary key): Unique identifier of the item in the database. Multiple warehouses can recognize this id.
- description (string): Additional details of the item.

**Reasoning**:
- I chose to give items its own schema because it streamlines the possibility of multiple warehouses storing the same item, as well as updating attributes when shipments come into a warehouse (through the primary key).
- It also allows items to not be present in any warehouse but still be existing in the overarching database, ensuring that items are not dependent on a link to a warehouse.

---

### 3️⃣ Feature: WarehouseItem
**Purpose**: To track associations between warehouses and the items stored within them.

**Attributes**:
- `warehouse` (foreign key to Warehouse): The warehouse where the item is currently stored.
- `item` (foreign key to Item): The corresponding item in the overarching database.
- `count` (integer): Total quantity in the warehouse.
- `available_count` (integer): Available quantity that is able to be shipped.
- `location` (string): Where the item is located in the warehouse.

**Reasoning**:
- This an **intermediary table** that facilitates a **many-to-many** relationship between warehouses and items.
- Separating `count` and `available_count` is an important nuance. A shipment can claim item quantities, but not be shipped out yet. We don't want future shipments to claim item amounts that previous shipments already claimed. 

---

### 4️⃣ Feature: Shipment
**Purpose**: To model the movement of goods between warehouses.

**Attributes**:
- `shipment_ID` (primary key): Unique identifier for the shipment.
- `origin_warehouse` (foreign key to Warehouse): The warehouse the shipment is sent from.
- `destination_warehouse` (foreign key to Warehouse): The warehouse the shipment is sent to.
- `date_shipped` (date): date shipment leaves the origin warehouse
- `estimated_arrival` (date): Expected arrival date of the shipment
- `status` (integer): Tracks the status ("Pending", "Shipped", "Received").
- `skids (integer): Number of shipping units the shipment contains. Think of skids as pallets that contain items.

**Reasoning**:
- `origin_warehouse` and `destination_warehouse` establish a **self-referencing relationship** on the warehouse model, linking shipments to warehouses.
- `status` helps monitor the shipment’s lifecycle.
- `date-shipped` and `estimated-arrival` are critical for scheduling and tracking transit times.
- The origin warehouse creates the shipment, and the shipment_ID is automatically assigned.

---

### 5️⃣ Feature: ShipmentItem
**Purpose**: To link items to shipments, tracking the quantity of items involved in each shipment.

**Attributes**:
- `shipment` (foreign key to Shipment): Links to the associated shipment.
- `item` (foreign key to WarehouseItem): Links to the WarehouseItem being moved in the shipment. 
- `count` (integer): The number of items in the shipment.

**Reasoning**:
- As shipments can involve multiple items and each item can be involved in multiple shipments, a **many-to-many relationship** is modeled using this intermediate table.
- `count` ensures that the shipment tracks the count of each item.
- Upon shipping out, count and available_count of the origin warehouse are updated.
- Upon receival, count is updated in the target warehouse.
  
---

## 📚 Summary
This database design emphasizes:
1. **Relational & Referential Integrity**: Proper use of foreign keys to establish relationships between models.
2. **Modular Design**: Relating warehouses, items, and shipments in clearly defined schemas.
3. **Scalability**: Allowing for multiple warehouses, items, and shipments, which makes the system extendable.
I designed this schema based on what made sense to me and the project, but please feel free to share how you would approach this!
   

---

## 🚀 Technologies Used
- **Django**: Backend framework for web development.
- **Python**: The foundation for building robust models and logic.
- **HTML/CSS**: For designing the front-end.

---

## 🎯 Goals and Purpose
### Why I built this:
- **Learning goal**: Experimented with Django’s Model-View-Template (MVT) architecture, gaining experience in data modeling and developing a potentially useful website from scratch.
- **Inspiration**: This was a project inspired by my experience as an associate at David Gooding Inc. and working with the company software and tracking system. I made this project with the hope of implementing my own logic and creativity on the way warehouse inventory could be managed and how shipments can be tracked.

---

## 🔒 Private Repository
The source code for this project cannot be shared publicly. Feel free to watch the video demo or reach out for further discussion about the app.
