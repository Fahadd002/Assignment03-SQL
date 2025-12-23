# Vehicle Rental System – SQL Project

## Project Overview
This project implements a **Vehicle Rental System** using PostgreSQL.  
It demonstrates database design, data modeling, and SQL query skills using real-world rental scenarios such as users, vehicles, and bookings.

The database supports:
- User management (Admin & Customer)
- Vehicle inventory management
- Booking and rental tracking
- Analytical queries using JOIN, EXISTS, WHERE, GROUP BY, and HAVING

---

## Database Design

### Entities
1. **Users**
   - Stores customer and admin information
2. **Vehicles**
   - Stores vehicle details and availability
3. **Bookings**
   - Stores booking records linking users and vehicles

### ENUM Types
- `user_role` → admin, customer
- `vehicle_type_enum` → car, bike, truck
- `vehicle_status` → available, rented, maintenance
- `booking_status` → pending, confirmed, completed, cancelled

---

## Schema Relationships
- One **User** can have many **Bookings**
- One **Vehicle** can have many **Bookings**
- Foreign key constraints ensure data integrity

---

## SQL Queries Explanation

### Query 1: Booking Details with JOIN
Retrieves booking information including customer name and vehicle name using `INNER JOIN`.

### Query 2: Vehicles Never Booked
Finds vehicles that have never been booked using `NOT EXISTS`.

### Query 3: Available Vehicles by Type
Retrieves all available vehicles filtered by a specific type using `WHERE`.

### Query 4: Vehicles with More Than 2 Bookings
Uses `GROUP BY` and `HAVING` to find vehicles that have been booked more than two times.

---

## How to Run
1. Create the database:
   ```sql
   CREATE DATABASE vehiclerental;
2. Connect to vehiclerental using Beekeeper Studio

3. Run schema and data scripts

4. Execute queries from queries.sql