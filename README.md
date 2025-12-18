-- =========================================
-- 1. CREATE DATABASE
-- =========================================
CREATE DATABASE IF NOT EXISTS ecommerce;
USE ecommerce;


-- =========================================
-- 2. CREATE CUSTOMERS TABLE
-- =========================================
CREATE TABLE customers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    address VARCHAR(255)
);

-- =========================================
-- 3. CREATE PRODUCTS TABLE
-- =========================================
CREATE TABLE products (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    price DECIMAL(10,2) NOT NULL,
    description TEXT
);

-- =========================================
-- 4. CREATE ORDERS TABLE
-- =========================================
CREATE TABLE orders (
    id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total_amount DECIMAL(10,2),
    FOREIGN KEY (customer_id) REFERENCES customers(id)
);

-- =========================================
-- 5. INSERT SAMPLE DATA
-- =========================================

-- Customers
INSERT INTO customers (name, email, address) VALUES
('John Doe', 'john@example.com', 'Chennai'),
('Jane Smith', 'jane@example.com', 'Bangalore'),
('Robert Brown', 'robert@example.com', 'Mumbai');

-- Products
INSERT INTO products (name, price, description) VALUES
('Product A', 50.00, 'Electronics item'),
('Product B', 75.00, 'Home appliance'),
('Product C', 40.00, 'Accessories'),
('Product D', 120.00, 'Gadget');

-- Orders
INSERT INTO orders (customer_id, order_date, total_amount) VALUES
(1, CURDATE() - INTERVAL 10 DAY, 200.00),
(2, CURDATE() - INTERVAL 40 DAY, 100.00),
(3, CURDATE() - INTERVAL 5 DAY, 180.00);

-- =========================================
-- 6. QUERIES
-- =========================================

-- 1. Customers who placed orders in last 30 days
SELECT DISTINCT c.*
FROM customers c
JOIN orders o ON c.id = o.customer_id
WHERE o.order_date >= CURDATE() - INTERVAL 30 DAY;

-- 2. Total amount spent by each customer
SELECT c.name, SUM(o.total_amount) AS total_spent
FROM customers c
JOIN orders o ON c.id = o.customer_id
GROUP BY c.name;

-- 3. Update Product C price to 45.00
UPDATE products
SET price = 45.00
WHERE name = 'Product C';

-- 4. Add discount column to products
ALTER TABLE products
ADD discount DECIMAL(5,2) DEFAULT 0.00;

-- 5. Top 3 highest priced products
SELECT *
FROM products
ORDER BY price DESC
LIMIT 3;

-- 6. Join customers and orders
SELECT c.name, o.order_date
FROM customers c
JOIN orders o ON c.id = o.customer_id;

-- 7. Orders with total > 150
SELECT *
FROM orders
WHERE total_amount > 150.00;

-- =========================================
-- 7. NORMALIZATION (ORDER ITEMS TABLE)
-- =========================================

CREATE TABLE order_items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    price DECIMAL(10,2),
    FOREIGN KEY (order_id) REFERENCES orders(id),
    FOREIGN KEY (product_id) REFERENCES products(id)
);

-- Insert order items
INSERT INTO order_items (order_id, product_id, quantity, price) VALUES
(1, 1, 2, 50.00),
(1, 2, 1, 75.00),
(3, 4, 1, 120.00);

-- 8. Customers who ordered Product A
SELECT DISTINCT c.name
FROM customers c
JOIN orders o ON c.id = o.customer_id
JOIN order_items oi ON o.id = oi.order_id
JOIN products p ON oi.product_id = p.id
WHERE p.name = 'Product A';

-- 9. Average order total
SELECT AVG(total_amount) AS average_order_value
FROM orders;
