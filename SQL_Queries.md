# Ejercicios - EMPRESA JARDINERÍA

![Modelo Entidad-Relación](MODELO%20ENTIDAD-RELACI%C3%93N.png)

## <span style="color:aqua">Consultas sobre una tabla</span>

1. Devuelve un listado con el código de oficina y la ciudad donde hay oficinas.
    ```sql
    SELECT codigo_oficina, ciudad
    FROM oficina
    ```

2. Devuelve un listado con la ciudad y el teléfono de las oficinas de España.
    ```sql
    SELECT ciudad, telefono
    FROM oficina
    WHERE pais = 'España'
    ```

3. Devuelve un listado con el nombre, apellidos y email de los empleados cuyo jefe tiene un código de jefe igual a 7.
    ```sql
    SELECT nombre, apellido1, apellido2, email
    FROM empleado
    WHERE codigo_jefe = 7
    ```

4. Devuelve el nombre del puesto, nombre, apellidos y email del jefe de la empresa.
    ```sql
    SELECT puesto, nombre, apellido1, apellido2, email
    FROM empleado
    WHERE codigo_jefe IS NULL
    ```

5. Devuelve un listado con el nombre, apellidos y puesto de aquellos empleados que no sean representantes de ventas.
    ```sql
    SELECT nombre, apellido1, apellido2, puesto
    FROM empleado
    WHERE puesto != 'Representante Ventas'
    ```

6. Devuelve un listado con el nombre de los todos los clientes españoles.
    ```sql
    SELECT nombre_cliente
    FROM cliente
    WHERE pais = 'Spain'
    ```

7. Devuelve un listado con los distintos estados por los que puede pasar un pedido.
    ```sql
    SELECT DISTINCT estado
    FROM pedido
    ```
    
8. Devuelve un listado con el código de cliente de aquellos clientes que realizaron algún pago en 2008. Tenga en cuenta que deberá eliminar aquellos códigos de cliente que aparezcan repetidos. Resuelva la consulta:
   * Utilizando la función YEAR de MySQL.
   * Utilizando la función DATE_FORMAT de MySQL.
   * Sin utilizar ninguna de las funciones anteriores.
    ```sql
    /*
    Utilizando la función YEAR
    */
    SELECT DISTINCT p.codigo_cliente
    FROM pago p
    WHERE YEAR(p.fecha_pago) = 2008

    /*
    Utilizando la función DATE_FORMAT
    */
    SELECT DISTINCT p.codigo_cliente
    FROM pago p
    WHERE DATE_FORMAT(p.fecha_pago, '%Y') = 2008

    /*
    Sin utilizar ninguna de las funciones
    */
    SELECT DISTINCT p.codigo_cliente
    FROM pago p
    WHERE p.fecha_pago BETWEEN '2008-01-01' AND '2008-12-31'
    ```

9. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos que no han sido entregados a tiempo.
    ```sql
    SELECT p.codigo_pedido, p.codigo_cliente, p.fecha_esperada, p.fecha_entrega
    FROM pedido p
    WHERE p.fecha_entrega > p.fecha_esperada
    ```

10. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos cuya fecha de entrega ha sido al menos dos días antes de la fecha esperada.
    * Utilizando la función ADDDATE de MySQL.
    * Utilizando la función DATEDIFF de MySQL.
    * ¿Sería posible resolver esta consulta utilizando el operador de suma + o resta -?
  
    ```sql
    /*
    Función ADDDATE()
    */
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega
    FROM pedido
    WHERE adddate(fecha_entrega, 2) <= fecha_esperada
    /*
    Función DATEDIFF()
    */
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega
    FROM pedido
    WHERE datediff(fecha_esperada, fecha_entrega) >= 2
    /*
    Operador de suma (+)
    */
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega
    FROM pedido
    WHERE fecha_entrega + 2 <= fecha_esperada
    ```

11. Devuelve un listado de todos los pedidos que fueron rechazados en 2009.
    ```sql
    SELECT *
    FROM pedido
    WHERE estado = 'Rechazado' AND YEAR(fecha_pedido) = 2009
    ```

12. Devuelve un listado de todos los pedidos que han sido entregados en el mes de enero de cualquier año.
    ```sql
    SELECT *
    FROM pedido
    WHERE MONTH(fecha_entrega) = 01
    ```

13. Devuelve un listado con todos los pagos que se realizaron en el año 2008 mediante Paypal. Ordene el resultado de mayor a menor.
    ```sql
    SELECT *
    FROM pago
    WHERE YEAR(fecha_pago) = 2008 AND forma_pago = 'Paypal'
    ```

14. Devuelve un listado con todas las formas de pago que aparecen en la tabla pago. Tenga en cuenta que no deben aparecer formas de pago repetidas.
    ```sql
    SELECT DISTINCT forma_pago
    FROM pago
    ```

15. Devuelve un listado con todos los productos que pertenecen a la gama Ornamentales y que tienen más de 100 unidades en stock. El listado deberá estar ordenado por su precio de venta, mostrando en primer lugar los de mayor precio.
    ```sql
    SELECT *
    FROM producto
    WHERE gama = 'Ornamentales' AND cantidad_en_stock > 100
    ORDER BY precio_venta DESC
    ```

16. Devuelve un listado con todos los clientes que sean de la ciudad de Madrid y cuyo representante de ventas tenga el código de empleado 11 o 30.
    ```sql
    SELECT *
    FROM cliente
    WHERE ciudad = 'Madrid' AND codigo_empleado_rep_ventas IN (11, 30)
    ```

## <span style="color:aqua">Consultas multitabla (Composición interna)<span>

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.
    ```sql
    SELECT c.nombre_cliente, e.nombre, e.apellido1, e.apellido2
    FROM cliente c
    JOIN empleado e ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    ```
    
2. Muestra el nombre de los clientes que hayan realizado pagos junto con el nombre de sus representantes de ventas.
    ```sql
    SELECT c.nombre_cliente, e.nombre, e.apellido1, e.apellido2
    FROM cliente c
    JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    ```

3. Muestra el nombre de los clientes que no hayan realizado pagos junto con el nombre de sus representantes de ventas.
    ```sql
    SELECT c.nombre_cliente, e.nombre, e.apellido1, e.apellido2
    FROM cliente c
    LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    WHERE p.codigo_cliente IS NULL
    ```

4. Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.
    ```sql
    SELECT c.nombre_cliente, e.nombre, e.apellido1, e.apellido2, o.ciudad
    FROM cliente c
    JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
    ```

5. Devuelve el nombre de los clientes que no hayan hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.
    ```sql
    SELECT c.nombre_cliente, e.nombre, o.ciudad
    FROM cliente c
    LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
    WHERE p.codigo_cliente IS NULL
    ```

6. Lista la dirección de las oficinas que tengan clientes en Fuenlabrada.
    ```sql
    SELECT DISTINCT o.linea_direccion1, o.linea_direccion2, o.ciudad, o.pais
    FROM oficina o
    JOIN empleado e ON o.codigo_oficina = e.codigo_oficina
    JOIN cliente c ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    WHERE c.ciudad = 'Fuenlabrada'

    ```

7. Devuelve el nombre de los clientes y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.
    ```sql
    SELECT c.nombre_cliente, e.nombre AS nombre_representante, o.ciudad AS ciudad_oficina
    FROM cliente c
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    JOIN oficina o ON e.codigo_oficina = o.codigo_oficina

    ```

8. Devuelve un listado con el nombre de los empleados junto con el nombre de sus jefes.
    ```sql
    SELECT e.nombre AS nombre_empleado, j.nombre AS nombre_jefe
    FROM empleado e
    LEFT JOIN empleado j ON e.codigo_jefe = j.codigo_empleado

    ```

9. Devuelve un listado que muestre el nombre de cada empleados, el nombre de su jefe y el nombre del jefe de sus jefe.
    ```sql
    SELECT e.nombre AS nombre_empleado, 
       j.nombre AS nombre_jefe, 
       jj.nombre AS nombre_jefe_del_jefe
    FROM empleado e
    LEFT JOIN empleado j ON e.codigo_jefe = j.codigo_empleado
    LEFT JOIN empleado jj ON j.codigo_jefe = jj.codigo_empleado

    ```

10. Devuelve el nombre de los clientes a los que no se les ha entregado a tiempo un pedido.
    ```sql
    SELECT DISTINCT c.nombre_cliente, c.codigo_cliente
    FROM cliente c 
    JOIN pedido p on p.codigo_cliente = c.codigo_cliente
    WHERE p.fecha_entrega > p.fecha_esperada
    ```

11. Devuelve un listado de las diferentes gamas de producto que ha comprado cada cliente.
    ```sql
    SELECT c.nombre_cliente, 
       GROUP_CONCAT(DISTINCT p.gama ORDER BY p.gama SEPARATOR ', ') AS gamas_compradas
    FROM cliente c
    JOIN pedido pe ON pe.codigo_cliente = c.codigo_cliente
    JOIN detalle_pedido dp ON dp.codigo_pedido = pe.codigo_pedido
    JOIN producto p ON p.codigo_producto = dp.codigo_producto
    GROUP BY c.nombre_cliente

    ```

## <span style="color:aqua">Consultas multitabla (Composición externa)<span>

1. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.
    ```sql
    SELECT c.*
    FROM cliente c
    LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    WHERE p.codigo_cliente IS NULL
    ```

2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pedido.
    ```sql
    SELECT c.*
    FROM cliente c
    LEFT JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
    WHERE p.codigo_cliente IS NULL
    ```

3. Devuelve un listado que muestre los clientes que no han realizado ningún pago y los que no han realizado ningún pedido.
    ```sql
    SELECT c.*, 
       CASE 
           WHEN p.codigo_cliente IS NULL AND pe.codigo_cliente IS NULL THEN 'Sin pedidos ni pagos'
           WHEN p.codigo_cliente IS NULL THEN 'Sin pagos'
           WHEN pe.codigo_cliente IS NULL THEN 'Sin pedidos'
       END AS estado
    FROM cliente c
    LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    LEFT JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
    WHERE p.codigo_cliente IS NULL OR pe.codigo_cliente IS NULL
    ORDER BY c.nombre_cliente;

    ```

4. Devuelve un listado que muestre solamente los empleados que no tienen una oficina asociada.
    ```sql
    SELECT e.*
    FROM empleado e
    LEFT JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
    WHERE e.codigo_oficina IS NULL
    ```

5. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado.
    ```sql
    SELECT e.*
    FROM empleado e LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    WHERE c.codigo_empleado_rep_ventas IS NULL
    ```

6. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado junto con los datos de la oficina donde trabajan.
    ```sql
    SELECT e.*, o.*
    FROM empleado e
    LEFT JOIN cliente c on c.codigo_empleado_rep_ventas = e.codigo_empleado
    JOIN oficina o on o.codigo_oficina = e.codigo_oficina
    WHERE c.codigo_empleado_rep_ventas IS NULL
    ORDER BY e.codigo_empleado
    ```

7. Devuelve un listado que muestre los empleados que no tienen una oficina asociada y los que no tienen un cliente asociado.
    ```sql
    SELECT e.codigo_empleado, e.nombre, e.apellido1, e.apellido2
    FROM empleado e
    LEFT JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
    LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    WHERE o.codigo_oficina IS NULL OR c.codigo_cliente IS NULL

    ```

8. Devuelve un listado de los productos que nunca han aparecido en un pedido.
    ```sql
    SELECT p.*
    FROM producto p
    LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
    WHERE dp.codigo_pedido IS NULL
    ```

9. Devuelve un listado de los productos que nunca han aparecido en un pedido. El resultado debe mostrar el nombre, la descripción y las dimensiones.
    ```sql
    SELECT p.nombre, p.descripcion, p.dimensiones
    FROM producto p
    LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
    WHERE dp.codigo_pedido IS NULL

    ```

10. Devuelve las oficinas donde no trabajan ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama Frutales.
    ```sql
    SELECT o.*
    FROM oficina o
    WHERE o.codigo_oficina NOT IN (
    SELECT DISTINCT e.codigo_oficina
    FROM empleado e
    JOIN cliente c ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    JOIN pedido p ON p.codigo_cliente = c.codigo_cliente
    JOIN detalle_pedido dp ON dp.codigo_pedido = p.codigo_pedido
    JOIN producto pr ON pr.codigo_producto = dp.codigo_producto
    WHERE pr.gama = 'Frutales'
    )

    ```

11. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.
    ```sql
    SELECT c.*
    FROM cliente c
    JOIN pedido p on p.codigo_cliente = c.codigo_cliente
    LEFT JOIN pago pa on pa.codigo_cliente = p.codigo_cliente
    WHERE pa.codigo_cliente is null
    ```

12. Devuelve un listado con los datos de los empleados que no tienen clientes asociados y el nombre de su jefe asociado.
    ```sql
    SELECT e.codigo_empleado, e.nombre, e.apellido1, e.apellido2, e.puesto, j.nombre AS nombre_jefe, j.apellido1 AS apellido_jefe
    FROM empleado e
    LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    LEFT JOIN empleado j ON e.codigo_jefe = j.codigo_empleado
    WHERE c.codigo_cliente IS NULL;

    ```

## <span style="color:aqua">Consultas resúmen<span>

1. ¿Cuántos empleados hay en la compañía?
    ```sql
    SELECT COUNT(codigo_empleado) as 'Número de empleados'
    FROM empleado
    ```

2. ¿Cuántos clientes tiene cada país?
    ```sql
    SELECT pais, COUNT(pais) 
    FROM cliente
    GROUP BY pais
    ```

3. ¿Cuál fue el pago medio en 2009?
    ```sql
    SELECT AVG(p.total) AS 'Pago medio en 2009'
    FROM pago p
    WHERE YEAR(p.fecha_pago) = 2009
    ```

4. ¿Cuántos pedidos hay en cada estado? Ordena el resultado de forma descendente por el número de pedidos.
    ```sql
    SELECT estado, count(codigo_pedido) as 'Número'
    FROM pedido
    GROUP BY estado
    ORDER BY Número DESC
    ```

5. Calcula el precio de venta del producto más caro y más barato en una misma consulta.
    ```sql
    SELECT MIN(precio_venta) AS 'Producto más barato', MAX(precio_venta) AS 'Producto más caro'
    FROM producto
    ```

6. Calcula el número de clientes que tiene la empresa.
    ```sql
    SELECT COUNT(nombre_cliente)
    FROM cliente
    ```

7. ¿Cuántos clientes existen con domicilio en la ciudad de Madrid?
    ```sql
    SELECT COUNT(codigo_cliente) AS 'Clientes en Madrid'
    FROM cliente
    WHERE ciudad = 'Madrid'
    ```

8. ¿Calcula cuántos clientes tiene cada una de las ciudades que empiezan por M?
    ```sql
    SELECT COUNT(codigo_cliente)
    FROM cliente
    WHERE ciudad LIKE 'M%'
    ```

9. Devuelve el nombre de los representantes de ventas y el número de clientes al que atiende cada uno.
    ```sql
    SELECT e.nombre AS nombre_representante, COUNT(c.codigo_cliente) AS numero_clientes
    FROM empleado e
    JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    GROUP BY e.codigo_empleado
    #SOLO SE TIENE EN CUENTA LOS REPRESENTANTES CON CLIENTES

    ```

10. Calcula el número de clientes que no tiene asignado representante de ventas.
    ```sql
    SELECT COUNT(codigo_cliente) AS clientes_sin_representante
    FROM cliente
    WHERE codigo_empleado_rep_ventas IS NULL

    ```

11. Calcula la fecha del primer y último pago realizado por cada uno de los clientes. El listado deberá mostrar el nombre y los apellidos de cada cliente.
    ```sql
    SELECT c.nombre_cliente, c.nombre_contacto, c.apellido_contacto, min(p.fecha_pago), max(p.fecha_pago)
    FROM pago p
    JOIN cliente c on c.codigo_cliente = p.codigo_cliente
    GROUP BY c.nombre_cliente, c.nombre_contacto, c.apellido_contacto
    ```

12. Calcula el número de productos diferentes que hay en cada uno de los pedidos.
    ```sql
    SELECT codigo_pedido, count(DISTINCT codigo_producto) as 'Nº Productos Distintos'
    FROM detalle_pedido
    GROUP BY codigo_pedido
    ```

13. Calcula la suma de la cantidad total de todos los productos que aparecen en cada uno de los pedidos.
    ```sql
    SELECT codigo_pedido, SUM(cantidad) as 'Cantidad total productos'
    FROM detalle_pedido
    GROUP BY codigo_pedido
    ```

14. Devuelve un listado de los 20 productos más vendidos y el número total de unidades que se han vendido de cada uno. El listado deberá estar ordenado por el número total de unidades vendidas.
    ```sql
    SELECT p.codigo_producto, SUM(dp.cantidad) as Cantidad_Total_Vendida
    FROM producto p 
    JOIN detalle_pedido dp on dp.codigo_producto = p.codigo_producto
    GROUP BY p.codigo_producto
    ORDER BY Cantidad_Total_Vendida DESC
    LIMIT 20
    ```

15. La facturación que ha tenido la empresa en toda la historia, indicando la base imponible, el IVA y el total facturado. La base imponible se calcula sumando el coste del producto por el número de unidades vendidas de la tabla detalle_pedido. El IVA es el 21 % de la base imponible, y el total la suma de los dos campos anteriores.
    ```sql
    SELECT 
    SUM(dp.cantidad * dp.precio_unidad) AS `Base Imponible`,
    SUM(dp.cantidad * dp.precio_unidad) * 0.21 AS `IVA`,
    SUM(dp.cantidad * dp.precio_unidad) * 1.21 AS `Total Facturado`
    FROM detalle_pedido dp

    ```

16. La misma información que en la pregunta anterior, pero agrupada por código de producto.
    ```sql
    SELECT 
    dp.codigo_producto,
    SUM(dp.cantidad * dp.precio_unidad) AS `Base Imponible`,
    SUM(dp.cantidad * dp.precio_unidad) * 0.21 AS `IVA`,
    SUM(dp.cantidad * dp.precio_unidad) * 1.21 AS `Total Facturado`
    FROM detalle_pedido dp
    GROUP BY dp.codigo_producto;

    ```

17. La misma información que en la pregunta anterior, pero agrupada por código de producto filtrada por los códigos que empiecen por OR.
    ```sql
    SELECT 
    dp.codigo_producto,
    SUM(dp.cantidad * dp.precio_unidad) AS `Base Imponible`,
    SUM(dp.cantidad * dp.precio_unidad) * 0.21 AS `IVA`,
    SUM(dp.cantidad * dp.precio_unidad) * 1.21 AS `Total Facturado`
    FROM detalle_pedido dp
    WHERE dp.codigo_producto LIKE 'OR%'
    GROUP BY dp.codigo_producto;


    ```

18. Lista las ventas totales de los productos que hayan facturado más de 3000 euros. Se mostrará el nombre, unidades vendidas, total facturado y total facturado con impuestos (21% IVA).
    ```sql
    SELECT 
    p.nombre AS Nombre_Producto,
    SUM(dp.cantidad) AS Unidades_Vendidas,
    SUM(dp.cantidad * dp.precio_unidad) AS Total_Facturado,
    SUM(dp.cantidad * dp.precio_unidad) * 1.21 AS Total_Con_IVA
    FROM detalle_pedido dp
    JOIN producto p ON dp.codigo_producto = p.codigo_producto
    GROUP BY p.codigo_producto, p.nombre
    HAVING Total_Facturado > 3000
    ORDER BY Total_Facturado DESC

    ```

19. Muestre la suma total de todos los pagos que se realizaron para cada uno de los años que aparecen en la tabla pagos.
    ```sql
    SELECT YEAR(p.fecha_pago) AS 'Año', SUM(p.total) AS 'Total'
    FROM pago
    GROUP BY Año
    ORDER BY Año ASC
    ```

## <span style="color:aqua">Subconsultas<span>

### <span style="color:palegreen">Con operadores básicos de comparación</span>

1. Devuelve el nombre del cliente con mayor límite de crédito.
    ```sql
    SELECT c.nombre_cliente
    FROM cliente c
    WHERE c.limite_credito = (SELECT MAX(c.limite_credito)           
                              FROM cliente c)
    ```

2. Devuelve el nombre del producto que tenga el precio de venta más caro.
    ```sql
    SELECT p.nombre
    FROM producto p
    WHERE p.precio_venta = (SELECT MAX(p.precio_venta) 
                            FROM producto p)
    ```

3. Devuelve el nombre del producto del que se han vendido más unidades. (Tenga en cuenta que tendrá que calcular cuál es el número total de unidades que se han vendido de cada producto a partir de los datos de la tabla detalle_pedido)
    ```sql
    SELECT p.nombre
    FROM producto p
    JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
    WHERE dp.cantidad = (SELECT MAX(dp.cantidad)
                         FROM detalle_pedido dp)
    ```

4. Los clientes cuyo límite de crédito sea mayor que los pagos que haya realizado. (Sin utilizar INNER JOIN).
    ```sql
    SELECT c.Nombre_cliente, c.Limite_credito, p.Total
    FROM cliente c, pago p 
    WHERE p.total = (SELECT MAX(p2.total)
                     FROM pago p2
                     WHERE c.limite_credito > p2.total)
    ```

5. Devuelve el producto que más unidades tiene en stock.
    ```sql
    SELECT p.nombre
    FROM producto p
    WHERE p.cantidad_en_stock = (SELECT MAX(p.cantidad_en_stock) 
                                 FROM producto p)
    ```

6. Devuelve el producto que menos unidades tiene en stock.
    ```sql
    SELECT p.nombre
    FROM producto p
    WHERE p.cantidad_en_stock = (SELECT MIN(p.cantidad_en_stock) 
                                 FROM producto p)
    ```

7. Devuelve el nombre, los apellidos y el email de los empleados que están a cargo de Alberto Soria.
    ```sql
    SELECT CONCAT(e.nombre, ' ', e.apellido1, ' ', e.apellido2) AS 'Empleados a cargo de Alberto Soria'
    FROM empleado e
    WHERE e.codigo_jefe = (SELECT e.codigo_empleado 
                           FROM empleado e
                           WHERE e.nombre = 'Alberto' AND e.apellido1 = 'Soria')
    ```

### <span style="color:palegreen">Subconsultas con ALL y ANY</span>

1. Devuelve el nombre del cliente con mayor límite de crédito.
    ```sql
    SELECT c.nombre_cliente
    FROM cliente c
    WHERE c.limite_credito >= ALL(SELECT MAX(c.limite_credito) 
                                  FROM cliente c)
    ```

2. Devuelve el nombre del producto que tenga el precio de venta más caro.
    ```sql
    SELECT p.nombre
    FROM producto p
    WHERE p.precio_venta >= ALL(SELECT MAX(p.precio_venta)
                                FROM producto p)
    ```

3. Devuelve el producto que menos unidades tiene en stock.
    ```sql
    SELECT p.nombre
    FROM producto p
    WHERE p.cantidad_en_stock <= ALL(SELECT MIN(p.cantidad_en_stock)
                                     FROM producto p)
    ```

### <span style="color:palegreen">Subconsultas con IN y NOT IN</span>

1. Devuelve el nombre, apellido1 y cargo de los empleados que no representen a ningún cliente.
    ```sql
    SELECT e.nombre, e.apellido1, e.apellido2
    FROM empleado e
    WHERE e.codigo_empleado NOT IN (SELECT c.codigo_empleado_rep_ventas
                                    FROM cliente c)
    ```

2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.
    ```sql
    SELECT c.nombre_cliente
    FROM cliente c
    WHERE c.codigo_cliente NOT IN (SELECT p.codigo_cliente 
                                   FROM pago p)
    ```

3. Devuelve un listado que muestre solamente los clientes que sí han realizado algún pago.
    ```sql
    SELECT c.nombre_cliente
    FROM cliente c 
    WHERE c.codigo_cliente IN (SELECT p.codigo_cliente 
                               FROM pago p)
    ```

4. Devuelve un listado de los productos que nunca han aparecido en un pedido.
    ```sql
    SELECT p.nombre
    FROM producto p
    WHERE p.codigo_producto NOT IN (SELECT dp.codigo_producto 
                                    FROM detalle_pedido dp)
    ```

5. Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos empleados que no sean representante de ventas de ningún cliente.
    ```sql
    SELECT e.nombre, e.apellido1, e.apellido2, e.puesto, o.telefono
    FROM empleado e
    JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
    WHERE e.codigo_empleado NOT IN (SELECT c.codigo_empleado_rep_ventas 
                                    FROM cliente c)
    ```

6. Devuelve las oficinas donde no trabajan ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama Frutales.
    ```sql
    SELECT o.*
    FROM oficina o
    WHERE o.codigo_oficina NOT IN (
        SELECT e.codigo_oficina
        FROM empleado e
        JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
        JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
        JOIN detalle_pedido dp ON p.codigo_pedido = dp.codigo_pedido
        JOIN producto pr ON dp.codigo_producto = pr.codigo_producto
        WHERE pr.gama = 'Frutales')
    ```

7. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.
    ```sql
    SELECT c.nombre_cliente
    FROM cliente c 
    WHERE c.codigo_cliente IN (SELECT p.codigo_cliente 
                               FROM pedido p)  AND 
          c.codigo_cliente NOT IN (SELECT p.codigo_cliente
                                   FROM pago p)
    ```

### <span style="color:palegreen">Subconsultas con EXISTS y NOT EXISTS</span>

1. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.
    ```sql
    SELECT *
    FROM cliente c
    WHERE NOT EXISTS (SELECT *
                      FROM pago p
                      WHERE c.codigo_cliente = p.codigo_cliente)
    ```

2. Devuelve un listado que muestre solamente los clientes que sí han realizado algún pago.
    ```sql
    SELECT 
    FROM cliente c
    WHERE EXISTS (SELECT *
                  FROM pago p
                  WHERE c.codigo_cliente = p.codigo_cliente)
    ```

3. Devuelve un listado de los productos que nunca han aparecido en un pedido.
    ```sql
    SELECT * 
    FROM cliente c
    WHERE NOT EXISTS (SELECT * 
                      FROM pedido p
                      WHERE p.codigo_cliente = c.codigo_cliente)
    ```

4. Devuelve un listado de los productos que han aparecido en un pedido alguna vez.
    ```sql
    SELECT * 
    FROM cliente c
    WHERE EXISTS (SELECT * 
                  FROM pedido p
                  WHERE p.codigo_cliente = c.codigo_cliente)
    ```

## <span style="color:aqua">Consultas variadas<span>

1. Devuelve el listado de clientes indicando el nombre del cliente y cuántos pedidos ha realizado. Tenga en cuenta que pueden existir clientes que no han realizado ningún pedido.
    ```sql
    SELECT c.nombre_cliente, count(p.codigo_pedido) as Cantidad_de_pedidos
    FROM cliente c 
    LEFT JOIN pedido p USING(codigo_cliente)
    GROUP BY c.nombre_cliente
    ORDER BY Cantidad_de_pedidos DESC
    ```
    
2. Devuelve un listado con los nombres de los clientes y el total pagado por cada uno de ellos. Tenga en cuenta que pueden existir clientes que no han realizado ningún pago.
    ```sql
    SELECT c.nombre_cliente, IFNULL(SUM(p.total), 0) AS Total_pagado
    FROM cliente c
    LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    GROUP BY c.nombre_cliente
    ORDER BY Total_pagado DESC
    ```

3. Devuelve el nombre de los clientes que hayan hecho pedidos en 2008 ordenados alfabéticamente de menor a mayor.
    ```sql
    SELECT DISTINCT c.nombre_cliente
    FROM cliente c
    JOIN pedido p USING(codigo_cliente)
    WHERE YEAR(p.fecha_pedido) = 2008
    ORDER BY c.nombre_cliente ASC
    ```

4. Devuelve el nombre del cliente, el nombre y primer apellido de su representante de ventas y el número de teléfono de la oficina del representante de ventas, de aquellos clientes que no hayan realizado ningún pago.
    ```sql
    SELECT DISTINCT c.nombre_cliente, e.nombre, e.apellido1, o.telefono
    FROM cliente c
    JOIN empleado e on e.codigo_empleado = c.codigo_empleado_rep_ventas
    JOIN oficina o USING(codigo_oficina)
    WHERE c.codigo_cliente NOT IN (SELECT codigo_cliente FROM pago)
    ```

5. Devuelve el listado de clientes donde aparezca el nombre del cliente, el nombre y primer apellido de su representante de ventas y la ciudad donde está su oficina.
    ```sql
    SELECT c.nombre_cliente, e.nombre, e.apellido1, o.ciudad
    FROM cliente c
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    JOIN oficina o ON o.codigo_oficina = e.codigo_oficina
    ```

6. Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos empleados que no sean representante de ventas de ningún cliente.
    ```sql
    SELECT e.nombre, e.apellido1, e.apellido2, e.puesto, o.telefono
    FROM empleado e
    JOIN oficina o USING(codigo_oficina)
    WHERE e.codigo_empleado NOT IN (SELECT DISTINCT c.codigo_empleado_rep_ventas 
                                    FROM cliente c)
    ```

7. Devuelve un listado indicando todas las ciudades donde hay oficinas y el número de empleados que tiene.
    ```sql
    SELECT o.ciudad, COUNT(e.codigo_oficina) AS Numero_empleados
    FROM oficina o
    JOIN empleado e USING(codigo_oficina)
    GROUP BY o.ciudad
    ORDER BY Numero_empleados DESC
    ```
