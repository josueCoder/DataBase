## **Base de Datos**
### **Exclusive  Left Join**
![](./img/exclusiveJoin.png)

> `Traer todos los elementos que existan en A `,donde nos aseguremos que  no existan en en la tabla  B.

> Ejem: Tenemos 2 categorias que se solapan : Equipos de computo y electronica , `necesito solo los productos de electronica `que no tengan que ver con computo.

### **Exclusive Right Join**
![](./img/exclusiveRightJoin.png)

### **Full Outer Join**
> Traer todos los elementos de ambas tablas.

>Trae todo contra todo y multiplicalo: `Elemento N°1 de la tabla A con el elemento numero 1 de B, elemento 1 de A con el elemento 2 B, elemento 1 A con elemnto n de B`.

### **Exclusive Full Outer Join**
![](./img/exclusiveFullOuterJoin.png)
> - Trae lo que existe en A y en B pero `No lo que tienen en comun`.
> Ejem: Deseo traer  por un lado productos  de electronica y por el otro solo de  computacion.No me intersa lo que tienen en comun(Lo que es de electronica y computacion al mismo tiempo).

### **LIKE**
![](./img/like.png)
> 1. Trae todos los elementos que `empiezen con Is%`(% indica que despues de esa cadena ,puede contener lo que sea)
> 2. Trae a todos los usuarios que  `contengan "Is_ael"`(- comodin que indica que ese espacio puede variar ejem: **`Is`m`ael`**, **`Is`r`ael`**)
> 3. traer todas las cadenas que no tengan este patron `Is_ael`.

### ORDER BY 
>    - `las cadenas` las ordena de manera alfabetica

### **Indices**
> Cuando hay campos muy complejos , en donde  se requiere ordenar por fecha,precio y ademas nombre. Volviendose un indice complicado.

> Para evitar ello se utilizan los `Indices`(se idexan,mapeando cada dato de la columna).

> Ejem:  Si se tienen palabras con `Aa` ocupara el lugar 1, `Ab` ocupa el lugar 2 , `B` el lugar 3. Que pasa si llega una palabra `Ac` , habra que `indexar nuevamente` quedando  en el siguiente orden : `Aa`,`Ab`,`Ac`,`B`,

> - Exelentes para busquedas y ordenamientos.

> - Es recomendable usarlo  en tablas donde se  realizan muchas busquedas , ucarlas en el Join o en el Order By .

> - Cuidar para alta transaccionabilidad.(`No es bueno tener indices en tablas donde se realizan muchas inserciones o actualizaciones`).

### **Agregacion | Group By**

> Reducir los datos en grupos.`Agrupar por criterios especificos.`

> Util : estadisticas , graficos,etc.

```
/*Quiero saber por marca X cosa**/

SELECT*
FROM tabla_diaria
GROUP BY marca;

```
¿Cual es el precio promedio por marca?

- se utilizara el `SELECT AVG(precio)` .
- Utilizas `GROUP BY marca` .Lo que hace :
     - lista las marcas  existentes y se agrupan (Asus, HP, Lenovo)
     - Suma los precios y saca el promedio  de los precios de cada uno de los grupos y te los muestra: Asus: 40, HP: 20 ,Lennovo:50
- Con el `Gruop By` indicas con que `criterio se armaran los grupos`.
- Con el `select` indicas que es `lo quieres saber de los grupos formados`(suma,promedio,etc).

### **Limitantes**
> - **LIMIT**
```
SELECT*
FROM tabla_diaria
LIMIT 1500;
```
>    - **`OFFSET`**: Complemento de LIMIT, `indica  el salto apartir de cual se iniciara la cuenta`.
```
/**Inicas que la cuenta iniciar despues de los  primeros 1500 registro(que efectue un salto)**/
SELECT*
FROM tabla_diaria
OFFET 1500
LIMIT 1500
```
### **WINDOWS FUNCTION**

**`Row_NUMBER`**: Se utiliza para asignar un numero secuencial unico a las filas dentro de una particion de un conjunto de resultados.

**Ejemplo sin partición**
Si solo se necesita numerar todas las filas de una tabla, se puede omitir la clausula PARTITION BY.



```
SELECT 
    ROW_NUMBER OVER() AS row_id,columna1, columna2
FROM platzi.alumnos
```
**Ejemplo sin partición**
Si se necesita numerar las filas `dentro de un grupo de valores` de una columna especifica, se usa la clausula `PARTITION BY`
```
SELECT 
    columna1,
    columna2,
    ROW_NUMBER() OVER (PARTITION BY columna1 ORDER BY columna2) AS RowNum
FROM 
    tu_tabla;

```
**Ejemplo completo**
```
SELECT 
    Departamento,
    NombreEmpleado,
    Salario,
    ROW_NUMBER() OVER (PARTITION BY Departamento ORDER BY Salario DESC) AS RowNum
FROM 
    Empleados;
```
**`PARTITION BY Departamento`** Agrupa a los empleados por departamento.

**`ORDEN BY Salario DESC`** Ordena a los empleados dentro de cada departamento por salario en orden desendente.

**`ROW_NUMBER()`** Asigna un numero de fila  a cada empleado dentro de su departamento. 

### **EN MIS TIEMPOS**

> Es inportante manejar las fechas adecuadamente utilizando tipos datos ya establecidos como `DATE` O `DATETIME`.


***Uso en SELECT***
##### **EXTRAER PARTES DE UNA FECHA**
- Option 1: `EXTRACT`
```
SELECT EXTRACT(YEAR FROM fecha_incorporacion) AS anio_incorporacion
FROM platzi.alumnos
```
- Option 2: `DATE_PART`
```
SELECT DATE_PART('YEAR',fecha_incorporacion) AS anio_incorporacion,
       DATE_PART('MONTH',fecha_incorporacion) AS mes_incorporacion,
	   DATE_PART('DAY',fecha_incorporacion) AS dia_incorporacion
FROM PLATZI.alumnos
```
***Uso en WHERE***
```
SELECT *
FROM PLATZI.alumnos
WHERE (EXTRACT(YEAR FROM fecha_incorporacion))=2018


SELECT*
FROM PLATZI.alumnos
WHERE (DATE_PART('YEAR',fecha_incorporacion))=2019


SELECT*
FROM (
	SELECT *, DATE_PART('YEAR',fecha_incorporacion) AS anio_incorporacion
	FROM PLATZI.alumnos
) AS alumnos_con_anio
WHERE anio_incorporacion =2020
```

### **SELECTORES DE RANGO**
Los tipos de rango que vienen en PostgreSQL son:

- `int4range`: Que trae un rango de enteros.

- `int8range:` Es un rango de enteros grandes.

- `numrange:` Es un rango numérico.
```
SELECT*
FROM platzi.alumnos
WHERE tutor_id in(1,2,3,4)


SELECT*
FROM PLATZI.alumnos
WHERE tutor_id between 1 and 10

---EVALUA SI EL VALOR ESTA DENTRO DEL RANGO
SELECT int4range(1,20) @>3;


--&& EVALUA LA INTERSECCION
SELECT numrange(11.1,19.2) && numrange(20.0,30.0)


--UPPER/ LOWER RETORNA EL VALOR + ALTO DENTRO DE UN RANGO
SELECT UPPER(int8range(15,25)) 

--LOS LIMITES SUPERIORES E INFERIORES DE LA INTERSECCION
SELECT INT4RANGE(10,20)* INT4RANGE(15,25)

--EVALUA SI ES VACIO UN RANGO
SELECT ISEMPTY(INT4RANGE(1,5))

-- APLICACIÓN
SELECT*
FROM PLATZI.alumnos
WHERE int4range(10,20) @>tutor_id 

```

















