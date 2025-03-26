# border_crossings_gov_sql

-- Step 1: Create Table
CREATE TABLE border_crossings (
    port_name VARCHAR(255),
    state VARCHAR(255),
    port_code INT,
    border VARCHAR(255),
    date DATE,
    measure VARCHAR(255),
    value INT,
    latitude FLOAT,
    longitude FLOAT,
    point GEOMETRY
);

-- Step 2: Remove Duplicates (if applicable)
DELETE FROM border_crossings
WHERE ctid NOT IN (
    SELECT MIN(ctid)
    FROM border_crossings
    GROUP BY port_name, state, port_code, border, date, measure, value, latitude, longitude, point
);

-- Step 3: Handle Missing Values (example: replace nulls in 'value' with 0)
UPDATE border_crossings
SET value = COALESCE(value, 0);

-- Step 4: Sort Data by 'Value' in Descending Order
SELECT * FROM border_crossings
ORDER BY value DESC;

-- Step 5: Load Cleaned Data into Table
COPY border_crossings (port_name, state, port_code, border, date, measure, value, latitude, longitude, point)
FROM '/path/to/cleaned_border_data.csv'
DELIMITER ','
CSV HEADER;
