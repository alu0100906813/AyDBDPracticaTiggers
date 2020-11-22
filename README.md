Práctica Triggers

Alumno: Francisco Javier Arocas Herrera

Alu: Alu0100906813

---

1. Dada la base de dato de viveros:

Para el procedimiento **crear_email**:

```sql
DELIMITER //

CREATE PROCEDURE crear_email(IN nombre VARCHAR(45), IN apellido VARCHAR(45), IN dominio VARCHAR(45), OUT email VARCHAR(128))
BEGIN
	SET email := CONCAT(nombre, apellido, "@", dominio);
END //

DELIMITER ;
```

Las variables de entrada, utilizamos **IN** o **OUT** para indicar si es de entrada o salida, seguido del nombre de variable y de su tipo.

Entre **Begin** y **End**, pues añadimos lo que va a hacer el procedimiento. En este caso, la variable de salida email,  la igualaremos a la concatenación de las variables de entrada nombre, apellido y dominio, además de añadirle un @.

Para hacer los **triggers**:

```sql
DELIMITER $$

CREATE TRIGGER trigger_crear_email_before_insert
	BEFORE INSERT
    ON Cliente FOR EACH ROW
BEGIN
	IF new.email = NULL THEN
		SET new.email := crear_email(new.nombre, new.apellido, new.dominio);
	END IF;
END$$

DELIMITER ;
```

El before insert, nos indica que se hará antes de realizar el insert, y luego indicamos con ON Cliente, la tabla en la que hacerlo.

Posteriormanete, con la variable **new**, tenemos acceso a las variables nuevas que se van a insertar, lo que hacemos es comparar si el usuario ha introducido un email no, viendo si el valor de la variable email insertada es null o no. En caso de que sea null, pues cambiamos el null por el email generado por el procedimiento **crear_email**, que hicimos anteriormente.

---

2. ***Crear un trigger permita verificar que las personas en el Municipio del catastro no pueden vivir en dos viviendas diferentes.***



---

3. ***Crear el o los trigger que permitan mantener actualizado el stock de la base de dato de viveros.***

Para controlar el Stock de los viveros:

```sql
DELIMITER $$

CREATE TRIGGER checkStock
	BEFORE INSERT
	ON Pedido FOR EACH ROW
BEGIN
	DECLARE stock INT;
	SELECT @stock = Stock FROM Productos WHERE CódigoBarras = NEW.CodigoBarrasPedido;
	SET stock = stock - 1;
	UPDATE Productos SET Stock = stock WHERE CodigoBarrasPedido = NEW.CodigoBarrasPedido;
END$$

DELIMITER ;
```

Recordemos que tenemos dos tablas, una **Productos**, donde tenemos los productos junto con su stock, y otra de **Pedido**, que son los pedidos hechos por los clientes. Hemos de tener en cuenta, que un pedido corresponde a un producto. La creación de columnas en Productos, es la única manera de decrementar el Stock.

Lo que hacemos, es que una vez realizado el pedido, obtenemos el **código de barras** de este pedido, y obtenemos el artículo, utilizando el código de barras de la tabla de productos, y luego decrementamos en uno el stock, para luego, mediante un **UPDATE**, cambiar los valores de la tabla.

---

