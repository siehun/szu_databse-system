```
SELECT DBMS_METADATA.GET_DDL('VIEW', 'ACTOR_INFO','SAKILA')DDL FROM DUAL;
```



```
CREATE
OR
REPLACE FORCE VIEW "SAKILA"."ACTOR_INFO" ("ACTOR_ID","FIRST_NAME","LAST_NAME","FILM_INFO") AS
SELECT
    a.actor_id, a.first_name, a.last_name, LISTAGG(
    c.name || ': ' || (
    SELECT
    LISTAGG(f.title, ', ') WITHIN GROUP (
    ORDER BY
    f.title
    )
    FROM
    film f
    INNER JOIN film_category fc ON f.film_id = fc.film_id
    INNER JOIN film_actor fa ON f.film_id = fa.film_id
    WHERE
    fc.category_id = c.category_id
    AND fa.actor_id = a.actor_id
    ), '; '
    ) WITHIN
GROUP (
    ORDER BY
    c.name
    ) AS film_info
FROM
    actor a
    LEFT JOIN film_actor fa
ON a.actor_id = fa.actor_id
    LEFT JOIN film_category fc ON fa.film_id = fc.film_id
    LEFT JOIN category c ON fc.category_id = c.category_id
GROUP BY
    a.actor_id,
    a.first_name,
    a.last_name
```



```
SELECT DBMS_METADATA.GET_DDL('VIEW', 'FILM_LIST','SAKILA')DDL FROM DUAL;
```

```
CREATE OR REPLACE FORCE VIEW "SAKILA"."FILM_LIST" ("FID","TITLE","DESCRIPTION","CATEGORY","PRICE","LENGTH","RATING","ACTORS") AS SELECT
    film.film_id AS FID,
    film.title AS title,
    film.description AS description,
    category.name AS category,
    film.rental_rate AS price,
    film.length AS length,
    film.rating AS rating,
    LISTAGG(actor.first_name || ' ' || actor.last_name, ', ') WITHIN GROUP (
        ORDER BY
            actor.first_name,
            actor.last_name
    ) AS actors
FROM
    film
    LEFT JOIN film_category ON film_category.film_id = film.film_id
    LEFT JOIN category ON category.category_id = film_category.category_id
    LEFT JOIN film_actor ON film.film_id = film_actor.film_id
    LEFT JOIN actor ON film_actor.actor_id = actor.actor_id
GROUP BY
    film.film_id,
    film.title,
    film.description,
    category.name,
    film.rental_rate,
    film.length,
    film.rating
```

```
SELECT DBMS_METADATA.GET_DDL('VIEW',
'SALES_BY_FILM_CATEGORY','SAKILA') DDL FROM DUAL;
```

```
CREATE OR REPLACE FORCE VIEW "SAKILA"."SALES_BY_FILM_CATEGORY" ("CATEGORY","TOTAL_SALES") AS SELECT
    c.name AS category,
    SUM(p.amount) AS total_sales
FROM
    payment p
    INNER JOIN rental r ON p.rental_id = r.rental_id
    INNER JOIN inventory i ON r.inventory_id = i.inventory_id
    INNER JOIN film f ON i.film_id = f.film_id
    INNER JOIN film_category fc ON f.film_id = fc.film_id
    INNER JOIN category c ON fc.category_id = c.category_id
GROUP BY
    c.name
ORDER BY
    total_sales DESC
```

```
CREATE VIEW vw_customer_address AS
SELECT
    c.customer_id,
    c.first_name,
    c.last_name,
    a.address,
    a.address2,
    a.district,
    a.city_id

FROM
    SAKILA.customer c
JOIN
    SAKILA.address a ON c.address_id = a.address_id;
    
select * from vw_customer_address;
```

```

    SELECT 
        r.rental_date,
        r.return_date,
        f.title,
        p.amount
    FROM 
        SAKILA.rental r
    JOIN 
        SAKILA.inventory i ON r.inventory_id = i.inventory_id
    JOIN 
        SAKILA.film f ON i.film_id = f.film_id
    JOIN 
        SAKILA.payment p ON r.rental_id = p.rental_id
    WHERE 
        r.customer_id = 1
        AND r.rental_date BETWEEN TIMESTAMP('1968-06-08 12:00:00') 
        AND TIMESTAMP('2024-05-09 12:00:00');

call get_customer_rental_history9(1, TIMESTAMP('1968-06-08 12:00:00'),TIMESTAMP('2024-05-09 12:00:00'))
```

```
CREATE TRIGGER SAKILA.UPD_FILM
AFTER INSERT OR UPDATE OR DELETE ON film
FOR EACH ROW
BEGIN
    DECLARE old_film_id INT;
    DECLARE new_film_id INT;

    -- 删除操作
    IF DELIMITER = 'DELETE' THEN
        DELETE FROM film_text WHERE film_id = OLD.film_id;
    END IF;

    -- 插入操作
    IF DELIMITER = 'INSERT' THEN
        INSERT INTO film_text (film_id, title, description)
        VALUES (NEW.film_id, NEW.title, NEW.description);
    END IF;

    -- 更新操作
    IF DELIMITER = 'UPDATE' THEN
        IF OLD.title != NEW.title OR OLD.description != NEW.description OR OLD.film_id != NEW.film_id THEN
            UPDATE film_text
            SET title = NEW.title, description = NEW.description, film_id = NEW.film_id
            WHERE film_id = OLD.film_id;
        END IF;
    END IF;
END;
```

```
INSERT INTO SAKILA.FILM (
    FILM_ID,
    TITLE,
    DESCRIPTION,
    RELEASE_YEAR,
    LANGUAGE_ID,
    ORIGINAL_LANGUAGE_ID,
    RENTAL_DURATION,
    RENTAL_RATE,
    LENGTH,
    REPLACEMENT_COST,
    RATING,
    SPECIAL_FEATURES,
    LAST_UPDATE
)
VALUES (
    9824, -- 假设 FILM_ID 为 9824
    'New Film Title', -- 电影标题
    'This is a description of the new film.', -- 电影描述
    2024, -- 发行年份
    1, -- 语言 ID
    NULL, -- 原始语言 ID，如果不知道可以设为 NULL
    3, -- 租赁时长
    2.99, -- 租赁费率
    120, -- 电影时长（分钟）
    19.99, -- 替换成本
    'PG', -- 评级
    'Special features include behind the scenes footage.', -- 特殊功能
    NOW() -- 最后更新时间，使用当前时间戳
);

UPDATE SAKILA.FILM
SET TITLE = '喜洋洋与灰太狼'
WHERE FILM_ID = 9824;
```

