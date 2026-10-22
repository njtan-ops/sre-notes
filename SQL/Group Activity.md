```sql
-- get tables
SELECT name
FROM sqlite_schema 
WHERE type = 'table' 
  AND name NOT LIKE 'sqlite_%'
ORDER BY name;

-- get schema
SELECT sql
FROM sqlite_schema 
WHERE type = 'table' 
  AND name NOT LIKE 'sqlite_%'
ORDER BY name;

-- G1

SELECT COUNT(shipment_id)
FROM shipments AS s
LEFT JOIN orders AS o
	on s.order_id = o.order_id
WHERE o.order_id ISNULL;


DELETE FROM shipments
WHERE shipment_id IN (
	SELECT shipment_id
	FROM shipments AS s
	LEFT JOIN orders AS o
		on s.order_id = o.order_id
	WHERE o.order_id ISNULL
);


WITH
	duplicates_ids AS (
		SELECT
			MAX(feed_row_id) AS feed_row_id
		FROM
			inventory_feed_raw
		GROUP BY
			warehouse_name,
			product_name
		HAVING
			COUNT(*) > 1
	)

DELETE FROM inventory_feed_raw
WHERE
	feed_row_id IN duplicates_ids;


WITH
	cleaned_feed AS (
		SELECT
			feed_row_id,
			warehouse_name,
			product_name,
			CASE
				WHEN LOWER(condition_label) LIKE 'ok%' THEN 'SELLABLE'
				WHEN LOWER(condition_label) LIKE 'sellable' THEN 'SELLABLE'
				WHEN LOWER(condition_label) LIKE 'd%m%g%' THEN 'DAMAGED'
				ELSE 'UNKNOWN'
			END AS condition_label,
			quantity
		FROM
			inventory_feed_raw
	),
	cleaned_to_inventory AS (
		SELECT
			warehouse_id,
			product_id,
			quantity AS quantity_on_hand,
			condition_label AS condition
		FROM
			cleaned_feed AS c
			JOIN warehouses ON c.warehouse_name = warehouses."name"
			JOIN products ON c.product_name = products."name"
	)
	
INSERT INTO
	inventory
SELECT
	*
FROM
	cleaned_to_inventory o_inventory;

SELECT * FROM inventory;



WITH
	combined_orders AS (
		SELECT
			*
		FROM
			orders
			JOIN order_items ON orders.order_id = order_items.order_id
			JOIN payments ON orders.order_id = payments.order_id
	),
	selected_order AS (
		SELECT
			combined_orders.order_id
		FROM
			combined_orders
		GROUP BY
			combined_orders.order_id
		HAVING
			COUNT(DISTINCT combined_orders.order_item_id) > 1
			AND COUNT(DISTINCT combined_orders.payment_id) > 1
		ORDER BY
			COUNT(combined_orders.order_id) ASC
		LIMIT
			1
		OFFSET
			0
	),
	naive_query AS (
		SELECT
			*
		FROM
			combined_orders
		WHERE
			combined_orders.order_id = selected_order.order_id
	)
SELECT
	selected_order.order_id,
	(
		SELECT
			COUNT(*)
		FROM
			naive_query
	) AS naive_row_count,
	(
		SELECT
			SUM(amount)
		FROM
			naive_query
	) AS naive_summed_amount,
	(
		SELECT
			SUM(amount)
		FROM
			payments
		WHERE
			payments.order_id = selected_order.order_id
	) AS true_amount_collected
FROM
	selected_order;







WITH
	aggregated_orders_per_shipment AS (
		SELECT
			warehouses.warehouse_id,
			warehouses."name" AS warehouse,
			shipments.shipment_id,
			shipments.order_id,
			COUNT(order_items.order_item_id) AS items,
			SUM(order_items.quantity * order_items.unit_price) AS revenue
		FROM
			warehouses
			JOIN shipments ON warehouses.warehouse_id = shipments.warehouse_id
			JOIN orders ON shipments.order_id = orders.order_id
			JOIN order_items ON shipments.order_id = order_items.order_id
		GROUP BY
			shipments.order_id
	),
	aggregated_payments_per_shipment AS (
		SELECT
			warehouses.warehouse_id,
			warehouses."name" AS warehouse,
			shipments.shipment_id,
			shipments.order_id,
			COUNT(payments.payment_id) AS payments_count,
			GROUP_CONCAT(payments.payment_id) AS payment_ids,
			SUM(payments.amount) AS total_amount
		FROM
			warehouses
			JOIN shipments ON warehouses.warehouse_id = shipments.warehouse_id
			JOIN orders ON shipments.order_id = orders.order_id
			JOIN payments ON orders.order_id = payments.order_id
		GROUP BY
			shipments.order_id
	)
SELECT
	warehouses."name" AS warehouse,
	SUM(aggregated_orders_per_shipment.revenue) AS revenue_shipped,
	SUM(aggregated_payments_per_shipment.total_amount) AS amount_collected
FROM
	warehouses
	JOIN aggregated_orders_per_shipment ON warehouses.warehouse_id = aggregated_orders_per_shipment.warehouse_id
	JOIN aggregated_payments_per_shipment ON warehouses.warehouse_id = aggregated_payments_per_shipment.warehouse_id
	AND aggregated_orders_per_shipment.shipment_id = aggregated_payments_per_shipment.shipment_id
GROUP BY
	warehouses.warehouse_id
ORDER BY
	warehouses.warehouse_id,
	total_amount;



WITH
	aggregated_orders AS (
		SELECT
			order_id,
			SUM(order_items.quantity * order_items.unit_price) AS revenue
		FROM
			order_items
		GROUP BY
			order_id
	),
	aggregated_payments AS (
		SELECT
			order_id,
			SUM(payments.amount) AS total_amount
		FROM
			payments
		GROUP BY
			order_id
	)
SELECT
	warehouses."name" AS warehouse,
	SUM(aggregated_orders.revenue) AS revenue_shipped,
	SUM(aggregated_payments.total_amount) AS amount_collected
FROM
	shipments
	JOIN warehouses ON shipments.warehouse_id = warehouses.warehouse_id
	JOIN orders ON shipments.order_id = orders.order_id
	LEFT JOIN aggregated_orders ON aggregated_orders.order_id = shipments.order_id
	LEFT JOIN aggregated_payments ON aggregated_payments.order_id = shipments.order_id
	
GROUP BY
	warehouses."name"
```