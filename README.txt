## Intelligence Databases - Assignment Solutions (PostgreSQL 16)

This folder contains PostgreSQL-compatible solutions for the Intelligence Databases assignments (Tasks 1-5). Each task is provided as a standalone SQL script. Use `psql` or any Postgres client connected to a PostgreSQL 16 instance. Some scripts require extensions (PostGIS).

## Files
- `task1_safe_prescriptions.sql` - Creates `healthnet` schema, `patient` and `patient_med` tables with constraints. Includes passing and failing insert examples (failing inserts are commented out).
- `task2_bill_totals_trigger.sql` - `billing` schema with `bill`, `bill_item`, `bill_audit`; uses a temp table plus row-level and statement-level triggers to recompute totals once per statement and record audits. Includes a small mixed-DML test.
- `task3_supervision_chain.sql` - `org` schema and `staff_supervisor` table. Recursive CTE computes each employee's top supervisor and hops, with cycle-avoidance.
- `task4_infectious_rollup.sql` - `kb` schema and `triple` table. Recursive transitive closure over `isA` and final query to list patients diagnosed (transitively) with `InfectiousDisease`.
- `task5_spatial_clinics.sql` - `spatial` schema and `clinic` table using PostGIS `geography`. Queries clinics within 1km and nearest 3 clinics. **Requires PostGIS extension**.
- `zip_contents.json` - Metadata for the generated zip (filename list).

## How to run
1. Copy the `.sql` files to your server or run them directly via psql, e.g.:
   psql -d yourdb -f task1_safe_prescriptions.sql
2. For Task 5, ensure PostGIS is installed on your Postgres server and the `postgis` extension is available. Run:
   CREATE EXTENSION IF NOT EXISTS postgis;
   before executing `task5_spatial_clinics.sql` (the script also attempts to create the extension).

## Notes and tips
- The bill totals trigger uses a session-local temporary table `tmp_changed_bills` and two triggers (row-level collector + statement-level recompute) to emulate Oracle's compound-trigger behavior and to ensure each bill is recomputed only once per statement.
- Task 1 uses explicit `CHECK` constraints and `NOT NULL` to ensure bad rows are rejected at insert time.
- Task 3 guards against cycles by tracking the path in the recursive CTE using arrays and `= ANY(path)` checks.
- Task 4's closure correctly propagates `isA` relationships so diagnoses that are several hops away still roll up to `InfectiousDisease`.
- Task 5 stores points as `geography(POINT,4326)` for simple metric distance calculations in meters.

## adapt scripts:
- Use `SERIAL`/`IDENTITY` for auto-incrementing primary keys where preferred.
- Integrate these into a single migration file (with safety checks).
- Produce a Docker `docker-compose.yml` + init SQL to run everything locally (including PostGIS).

