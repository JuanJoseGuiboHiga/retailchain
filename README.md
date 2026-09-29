### 1. ¿Cuántas filas devuelve cada consulta y por qué son distintas? ¿Qué filas se eliminaron con UNION?

* **`UNION` devuelve 11 filas:** Entrega un catálogo de productos únicos consolidado.
* **`UNION ALL` devuelve 14 filas:** Consolida la totalidad de los registros de inventario de ambas sucursales ($7 + 7 = 14$).

**Explicación de la diferencia:**  
Son distintas porque `UNION` aplica un filtrado automático de duplicados, mientras que `UNION ALL` conserva todos los registros sin evaluar redundancias. La diferencia de 3 filas ($14 - 11 = 3$) corresponde a los 3 productos presentes simultáneamente en ambas sucursales (por ejemplo: si el producto con ID `101` llamado `Teclado Mecánico` figura tanto en la sucursal Norte como en la Sur, `UNION ALL` muestra las 2 entradas y `UNION` conserva solo 1 fila).

---

### 2. ¿Por qué UNION ALL es más eficiente que UNION? ¿Qué operación adicional realiza UNION internamente?

`UNION ALL` es notablemente más rápido y consume menos recursos porque se limita a concatenar o apilar verticalmente los conjuntos de resultados directamente en memoria.

En cambio, `UNION` realiza dos operaciones adicionales costosas:
1. **Ordenamiento o Hash (`Sort / Hash Operation`):** Reordena o genera una tabla hash con todos los registros para poder comparar fila por fila.
2. **Deduplicación (`Unique / Distinct`):** Examina todas las columnas del resultado para detectar y descartar duplicados antes de entregar la salida final.

---

### 3. ¿En qué casos de negocio usarías cada uno? (Ejemplos reales)

#### Casos para `UNION`:
* **Directorio de contactos unificado:** Consolidar clientes de un CRM antiguo y uno nuevo para generar una lista única de envío de correos, garantizando que nadie reciba un correo duplicado.
* **Maestro de categorías o marcas:** Unificar las categorías de productos vendidas en distintas plataformas (e-commerce y tienda física) para armar el menú de navegación del sitio.

#### Casos para `UNION ALL`:
* **Historial de transacciones financieras:** Consolidar movimientos de cuentas corrientes y cajas de ahorro para auditoría contable o reportería de volumen, donde cada transacción individual debe conservarse.
* **Logs de eventos de aplicaciones:** Apilar registros diarios de accesos o clicks en la web provenientes de distintos servidores para métricas de tráfico total.

---

### 4. ¿Qué pasa si las columnas de ambas consultas no coinciden en número o tipo? ¿Qué error genera SQL?

SQL requiere estricta compatibilidad posicional entre las consultas que se combinan:

* **Distinto número de columnas:** La consulta falla inmediatamente al compilar.  
  * *Error típico:* `ERROR: each UNION query must have the same number of columns` (PostgreSQL) o `The used SELECT statements have a different number of columns` (MySQL).
* **Tipos de datos incompatibles:** Si la columna 1 del primer `SELECT` es un texto (`VARCHAR`) y la del segundo es una fecha o entero no convertible automáticamente, el motor aborta la ejecución.  
  * *Error típico:* `ERROR: UNION types text and integer cannot be matched` o `Conversion failed when converting...`.
