### Lesson 8 (SQL Databases)

```
CREATE DATABASE lesson8;

USE lesson8;

CREATE TABLE lesson8.projector (
  id INT NOT NULL AUTO_INCREMENT,
  birthday DATE NOT NULL,
  PRIMARY KEY (`id`)
  ENGINE=InnoDB);
```

```
DELIMITER $$

CREATE PROCEDURE dorepeat(p1 INT)
BEGIN
    SET @x = 0;
    SET @y = 0;
    REPEAT 
        INSERT INTO projector (id, birthday) values (NULL, (NOW() + INTERVAL @y DAY)),(NULL, (NOW() - INTERVAL @y DAY)),(NULL, (NOW() + INTERVAL @y DAY)),(NULL, (NOW() - INTERVAL @y DAY)),(NULL, (NOW() + INTERVAL @y DAY)),(NULL, (NOW() - INTERVAL @y DAY)),(NULL, (NOW() + INTERVAL @y DAY)),(NULL, (NOW() - INTERVAL @y DAY)),(NULL, (NOW() + INTERVAL @y DAY)),(NULL, (NOW() - INTERVAL @y DAY));
        SET @x = @x + 1; 
	SET @y = @y + 1;
	IF (@y > 365 * 30) THEN
		SET @y = 0;
	END IF; 
    UNTIL @x > p1 END REPEAT;
END$$
DELIMITER ;

CALL dorepeat(4000000)

mysql> SELECT count(*) FROM projector;
+----------+
| count(*) |
+----------+
| 40000000 |
+----------+
1 row in set (21.70 sec)
```

Without index:
```
mysql> SELECT birthday FROM projector WHERE birthday = '1988-02-22';
...
122 rows in set (19.41 sec)

mysql> SELECT birthday FROM projector WHERE birthday = '1978-06-11';
...
109 rows in set (18.67 sec)
```

With index BTREE:
```
mysql> CREATE INDEX birthday_ind USING BTREE ON projector (birthday);
Query OK, 0 rows affected (1 min 37.86 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql> SELECT birthday FROM projector WHERE birthday IN ('1988-02-22','1990-12-08','1993-05-04','1982-08-08','1997-04-11');
...
3713 rows in set (0.01 sec)
mysql> SELECT birthday FROM projector WHERE birthday IN ('1971-02-22','1986-12-08','1983-05-04','1986-08-08','1994-04-11');
...
2156 rows in set (0.04 sec)
```

With index HASH:
```
mysql> CREATE INDEX birthday_ind USING HASH ON projector (birthday);
Query OK, 0 rows affected (1 min 29.08 sec)
Records: 0  Duplicates: 0  Warnings: 0
mysql> SELECT birthday FROM projector WHERE birthday IN ('1988-02-22','1990-12-08','1993-05-04','1982-08-08','1997-04-11');
...
3713 rows in set (0.02 sec)
mysql> SELECT birthday FROM projector WHERE birthday IN ('1971-02-22','1986-12-08','1983-05-04','1986-08-08','1994-04-11');
...
2156 rows in set (0.01 sec)
```
