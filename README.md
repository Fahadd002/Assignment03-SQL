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
  create database "vehiclerental";
  ```
2. Connect to vehiclerental using Beekeeper Studio

3. Run schema and data scripts

4. Execute queries from queries.sql


# 📄 queries.sql

## Create ENUM TYPES

```sql
create type user_role as enum ('admin', 'customer');
create type vehicle_type_enum as enum ('car', 'bike', 'truck');
create type vehicle_status as enum ('available', 'rented', 'maintenance');
create type booking_status as enum ('pending', 'confirmed', 'completed', 'cancelled');
```
## Create TABLES
```sql
create table users (
    user_id serial primary key,
    role user_role not null,
    name varchar(100) not null,
    email varchar(150) unique not null,
    password text not null,
    phone_number varchar(20),
    created_at timestamp default current_timestamp
);

create table vehicles (
    vehicle_id serial primary key,
    vehicle_name varchar(100) not null,
    vehicle_type vehicle_type_enum not null,
    model varchar(100),
    registration_number varchar(50) unique not null,
    rental_price_per_day numeric(10,2) not null,
    availability_status vehicle_status not null default 'available',
    created_at timestamp default current_timestamp
);

create table bookings (
    booking_id serial primary key,
    user_id int not null,
    vehicle_id int not null,
    start_date date not null,
    end_date date not null,
    booking_status booking_status not null default 'pending',
    total_cost numeric(10,2) not null,
  
    constraint fk_user foreign key (user_id) references users(user_id) on delete cascade,
    constraint fk_vehicle foreign KEY (vehicle_id) references vehicles(vehicle_id) on delete restrict,
    constraint chk_booking_dates check (end_date >= start_date)
);
```
## Insert SAMPLE DATA
```sql

insert into users (name, email, phone_number, role, password)
values
('Alice', 'alice@example.com', '1234567890', 'customer', 'bcrypt_hash_here'),
('Bob', 'bob@example.com', '0987654321', 'admin', 'bcrypt_hash_here'),
('Charlie', 'charlie@example.com', '1122334455', 'customer', 'bcrypt_hash_here');

insert into vehicles (vehicle_name, vehicle_type, model, registration_number, rental_price_per_day, availability_status)
values
('Toyota Corolla', 'car', '2022', 'ABC-123', 50, 'available'),
('Honda Civic', 'car', '2021', 'DEF-456', 60, 'rented'),
('Yamaha R15', 'bike', '2023', 'GHI-789', 30, 'available'),
('Ford F-150', 'truck', '2020', 'JKL-012', 100, 'maintenance');

insert into bookings (user_id, vehicle_id, start_date, end_date, booking_status, total_cost)
values
(1, 2, '2023-10-01', '2023-10-05', 'completed', 240),
(1, 2, '2023-11-01', '2023-11-03', 'completed', 120),
(3, 2, '2023-12-01', '2023-12-02', 'confirmed', 60),
(1, 1, '2023-12-10', '2023-12-12', 'pending', 100);
```
## QUERY 1: JOIN
Retrieve booking information with customer and vehicle names
```sql
select v.vehicle_id, u.name as customer_name , v.vehicle_name as vehicle_name, b.start_date, b.end_date, b.booking_status as status
from bookings b
inner join users u on b.user_id = u.user_id
inner join vehicles v on b.vehicle_id = v.vehicle_id
order by b.start_date;
```
## QUERY 2: EXISTS
Vehicles that have never been booked
```sql
select   v.vehicle_id, v.vehicle_name as name, v.vehicle_type as type, v.model, v.registration_number, v.rental_price_per_day as rental_price, v.availability_status as status
from vehicles v
where not exists (
    select * from bookings b
    where b.vehicle_id = v.vehicle_id
);
```
## QUERY 3: WHERE
Available vehicles of a specific type=

```sql
select vehicle_id, vehicle_name as name, vehicle_type as type, model, registration_number, rental_price_per_day as rental_price, availability_status as status
from vehicles
where availability_status = 'available';
```

## QUERY 4: GROUP BY & HAVING
```sql

select v.vehicle_name, count(*) as total_bookings
from bookings b join vehicles v
    on b.vehicle_id = v.vehicle_id
group by v.vehicle_id
having count(*) > 2;
```
