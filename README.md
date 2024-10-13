# goit-rdb-hw-04

# task number 1

CREATE DATABASE LibraryManagement;

USE LibraryManagement;

CREATE TABLE authors (
    author_id INT AUTO_INCREMENT PRIMARY KEY,
    author_name VARCHAR(255) NOT NULL
);

CREATE TABLE genres (
    genre_id INT AUTO_INCREMENT PRIMARY KEY,
    genre_name VARCHAR(255) NOT NULL
);

CREATE TABLE books (
    book_id INT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    publication_year YEAR NOT NULL,
    author_id INT,
    genre_id INT,
    FOREIGN KEY (author_id) REFERENCES authors(author_id),
    FOREIGN KEY (genre_id) REFERENCES genres(genre_id)
);

CREATE TABLE users (
    user_id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL
);

CREATE TABLE borrowed_books (
    borrow_id INT AUTO_INCREMENT PRIMARY KEY,
    book_id INT,
    user_id INT,
    borrow_date DATE NOT NULL,
    return_date DATE,
    FOREIGN KEY (book_id) REFERENCES books(book_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

# task number 2

INSERT INTO authors (author_id, author_name) 
VALUES 
(1, 'J.K. Rowling'),
(2, 'Mark Twain');

INSERT INTO books (book_id, title, publication_year, author_id, genre_id) 
VALUES 
(11, 'Harry Potter and the Philosopher''s Stone', 1997, 1, 1),
(12, 'The Adventures of Tom Sawyer', 1876, 2, 2);

INSERT INTO borrowed_books (borrow_id, book_id, user_id, borrow_date, return_date) 
VALUES 
(1, 11, 1, '2024-10-01', '2024-10-15'),
(2, 12, 2, '2024-09-20', '2024-10-05');

INSERT INTO genres (genre_id, genre_name) 
VALUES 
(1, 'Fantasy'),
(2, 'Adventure');

INSERT INTO users (user_id, username, email) 
VALUES 
(1, 'alex_smith', 'alex@fake.com'),
(2, 'emma_brown', 'emma@fake.com');

# task number 3

SELECT 
    orders_2.id AS order_id,
    customers.name AS customer_name,
    products_2.name AS product_name,
    categories.name AS category_name,
    employees.first_name AS employee_first_name,
    employees.last_name AS employee_last_name,
    shippers.name AS shipper_name,
    suppliers.name AS supplier_name,
    order_details.quantity,
    products_2.price AS product_price,
    orders_2.date AS order_date
FROM 
    order_details
INNER JOIN 
    orders_2 ON order_details.order_id = orders_2.id
INNER JOIN 
    customers ON orders_2.customer_id = customers.id
INNER JOIN 
    products_2 ON order_details.product_id = products_2.id
INNER JOIN 
    categories ON products_2.category_id = categories.id
INNER JOIN 
    employees ON orders_2.employee_id = employees.employee_id
INNER JOIN 
    shippers ON orders_2.shipper_id = shippers.id
INNER JOIN 
    suppliers ON products_2.supplier_id = suppliers.id;

# task number 4

1. Порахувати кількість рядків (COUNT):
SELECT COUNT(*) 
FROM order_details
INNER JOIN orders_2 ON order_details.order_id = orders_2.id
INNER JOIN customers ON orders_2.customer_id = customers.id
INNER JOIN products_2 ON order_details.product_id = products_2.id
INNER JOIN categories ON products_2.category_id = categories.id
INNER JOIN employees ON orders_2.employee_id = employees.employee_id
INNER JOIN shippers ON orders_2.shipper_id = shippers.id
INNER JOIN suppliers ON products_2.supplier_id = suppliers.id;

2. Заміна INNER JOIN на LEFT JOIN чи RIGHT JOIN:
З використанням LEFT JOIN:

SELECT COUNT(*) 
FROM order_details
INNER JOIN orders_2 ON order_details.order_id = orders_2.id
LEFT JOIN customers ON orders_2.customer_id = customers.id
LEFT JOIN products_2 ON order_details.product_id = products_2.id
INNER JOIN categories ON products_2.category_id = categories.id
INNER JOIN employees ON orders_2.employee_id = employees.employee_id
LEFT JOIN shippers ON orders_2.shipper_id = shippers.id
LEFT JOIN suppliers ON products_2.supplier_id = suppliers.id;

Запит для перевірки, чи є хоча б один рядок з NULL у правих таблицях 
після LEFT JOIN:

SELECT COUNT(*)
FROM 
    order_details
LEFT JOIN orders_2 ON order_details.order_id = orders_2.id
LEFT JOIN customers ON orders_2.customer_id = customers.id
LEFT JOIN products_2 ON order_details.product_id = products_2.id
WHERE customers.id IS NULL OR products_2.id IS NULL;

Висновок.
Відповідь:
Кількість рядків може змінюватися при використанні LEFT JOIN або RIGHT JOIN, 
оскільки ці типи з'єднань повертають всі записи з однієї таблиці навіть у 
випадку відсутності відповідних записів у іншій таблиці. Це призводить до того,
що можуть з'являтися додаткові рядки з NULL значеннями. У випадку з 
INNER JOIN, повертаються тільки ті рядки, де є відповідність
між об'єднаними таблицями.

3. Рядки, де employee_id > 3 та ≤ 10:

SELECT *
FROM order_details
INNER JOIN orders_2 ON order_details.order_id = orders_2.id
INNER JOIN employees ON orders_2.employee_id = employees.employee_id
WHERE employees.employee_id > 3 AND employees.employee_id <= 10;

4. Групування за іменем категорії, підрахунок рядків у групі
та середньої кількості товару:

SELECT categories.name AS category_name, 
       COUNT(*) AS row_count, 
       AVG(order_details.quantity) AS avg_quantity
FROM order_details
INNER JOIN products_2 ON order_details.product_id = products_2.id
INNER JOIN categories ON products_2.category_id = categories.id
GROUP BY categories.name;

5. Фільтрація за середньою кількістю товару > 21:

SELECT categories.name AS category_name, 
       COUNT(*) AS row_count, 
       AVG(order_details.quantity) AS avg_quantity
FROM order_details
INNER JOIN products_2 ON order_details.product_id = products_2.id
INNER JOIN categories ON products_2.category_id = categories.id
GROUP BY categories.name
HAVING AVG(order_details.quantity) > 21;

6. Сортування за спаданням кількості рядків:

SELECT categories.name AS category_name, 
       COUNT(*) AS row_count, 
       AVG(order_details.quantity) AS avg_quantity
FROM order_details
INNER JOIN products_2 ON order_details.product_id = products_2.id
INNER JOIN categories ON products_2.category_id = categories.id
GROUP BY categories.name
HAVING AVG(order_details.quantity) > 21
ORDER BY row_count DESC;

7. Виведення чотирьох рядків, пропустивши перший рядок:

SELECT categories.name AS category_name, 
       COUNT(*) AS row_count, 
       AVG(order_details.quantity) AS avg_quantity
FROM order_details
INNER JOIN products_2 ON order_details.product_id = products_2.id
INNER JOIN categories ON products_2.category_id = categories.id
GROUP BY categories.name
HAVING AVG(order_details.quantity) > 21
ORDER BY row_count DESC
LIMIT 4 OFFSET 1;
