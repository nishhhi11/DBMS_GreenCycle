# GreenCycle – Community Waste Collection & Recycling Scheduler

GreenCycle is a PostgreSQL-based database project designed to manage household waste collection and recycling schedules. The system stores information about households, routes, vehicles, waste types, and collection schedules.

The database also uses SQL queries for collection analysis and a PostgreSQL trigger to automatically generate the next collection schedule when a collection is marked as completed.

## Problem Statement

GreenCycle aims to provide a database for a municipal recycling service that schedules household waste pickups and automatically generates the next collection date after the previous collection is completed.

## Objectives

- Design an ER diagram for the waste collection system.
- Convert the ER model into a normalized relational schema.
- Maintain primary key and foreign key relationships.
- Store households, routes, vehicles, waste types, and collection schedules.
- Perform SQL queries using joins, aggregate functions, subqueries, and filtering.
- Identify households that missed their scheduled collection.
- Analyze the number of households served by each route.
- Automatically generate the next collection schedule using a database trigger.

## Database Design

The database consists of five main tables:

### 1. Vehicle
- VehicleID – Primary Key
- VehicleNumber
- VehicleType

### 2. Route
- RouteID – Primary Key
- RouteName
- VehicleID – Foreign Key

### 3. Household
- HouseholdID – Primary Key
- HouseNumber
- Address
- ResidentName
- Phone
- RouteID – Foreign Key

### 4. WasteType
- WasteTypeID – Primary Key
- WasteTypeName
- Description

### 5. CollectionSchedule
- ScheduleID – Primary Key
- HouseholdID – Foreign Key
- RouteID – Foreign Key
- WasteTypeID – Foreign Key
- ScheduledDate
- Status
- CompletedDate

## Relationships

- Each Route is assigned to a Vehicle.
- Each Household is assigned to a Route.
- Each CollectionSchedule belongs to a Household.
- Each CollectionSchedule is associated with a Route and WasteType.
- Foreign keys are used to maintain referential integrity.

## Normalization

The database is normalized to reduce data redundancy and avoid insertion, update, and deletion anomalies.

The schema follows:

- 1NF – Atomic attributes and no repeating groups.
- 2NF – Non-key attributes depend on the complete primary key.
- 3NF – Entities are separated to avoid unnecessary transitive dependencies.

## SQL Implementation

The project uses PostgreSQL and includes:

### DDL
- CREATE TABLE
- Primary Keys
- Foreign Keys
- NOT NULL
- UNIQUE

### DML
- INSERT
- UPDATE
- SELECT

Sample data has been inserted for vehicles, routes, waste types, households, and collection schedules.

## SQL Queries

### 1. Finding Missed Collections

Uses:
- LEFT JOIN
- NULL checking

This identifies households without a completed collection record for the scheduled date.

### 2. Route Analysis

Uses:
- COUNT()
- GROUP BY
- LEFT JOIN

This calculates the number of completed household collections for each route.

### 3. Highly Served Routes

Uses:
- Subquery
- GROUP BY
- HAVING COUNT() > 2

This identifies households belonging to routes that completed more than two collections during the specified collection period.

## Trigger Implementation

A PostgreSQL trigger is implemented to automate the next collection schedule.

When a collection's status changes to `Completed`:

1. The trigger detects the status update.
2. A new CollectionSchedule record is created.
3. The same household, route, and waste type are used.
4. The next scheduled date is set to 7 days later.
5. The new record receives the status `Scheduled`.
6. CompletedDate remains `NULL`.

### Trigger

```sql
CREATE TRIGGER trg_generate_next_collection
AFTER UPDATE OF Status ON CollectionSchedule
FOR EACH ROW
EXECUTE FUNCTION generate_next_collection();
