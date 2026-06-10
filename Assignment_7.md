<!-- Assignment - Find out a way to query and store json flattened data in from one postgres tbl to another postgres tbl -->

INSERT INTO sales_flat (
    sale_id,
    rep_name,
    department,
    city,
    amount,
    status
)
SELECT
    (payload->>'sale_id')::INT,
    payload->>'rep_name',
    payload->>'department',
    payload->>'city',
    (payload->>'amount')::NUMERIC,
    payload->>'status'
FROM api_data;