# Migración de Base de Datos Chinook: PostgreSQL a MongoDB

Este proyecto implementa la migración de la base de datos relacional Chinook desde PostgreSQL hacia MongoDB, como parte del curso de Bases de Datos No Relacionales.

## 📋 Descripción del Proyecto

La base de datos Chinook es un dataset clásico que simula una tienda de música digital. Este proyecto demuestra cómo migrar de un modelo relacional tradicional a un modelo de documentos NoSQL, adaptando las consultas y optimizando la estructura para MongoDB.

### Base de Datos Original
- **Fuente**: [Chinook Database](https://github.com/lerocha/chinook-database)
- **Tipo**: Base de datos relacional (PostgreSQL)
- **Dominio**: Tienda de música digital con artistas, álbumes, tracks, clientes, empleados, facturas y playlists

## 🎯 Objetivos

1. **Migración de esquema**: Transformar el modelo relacional en un modelo de documentos apropiado para MongoDB
2. **Desnormalización estratégica**: Embeber documentos relacionados para optimizar consultas frecuentes
3. **Comparación de rendimiento**: Demostrar las diferencias entre consultas SQL y agregaciones MongoDB
4. **Indexación**: Implementar índices optimizados para las consultas más comunes

## 🏗️ Arquitectura de la Migración

### Modelo Relacional Original
- **11 tablas** normalizadas con relaciones FK
- Estructura típica de OLTP con alta normalización

### Modelo de Documentos (MongoDB)
- **5 colecciones** principales con documentos embebidos
- Desnormalización controlada para optimizar consultas

#### Colecciones Principales:

1. **albums**: Álbumes con tracks embebidos
2. **tracks**: Tracks individuales con estadísticas de ventas
3. **playlists**: Playlists con tracks referenciados
4. **customers**: Clientes con facturas embebidas
5. **employees**: Empleados con clientes asignados

## 📊 Transformaciones Realizadas

### Desnormalización Estratégica

| Tabla Original | Colección MongoDB | Estrategia |
|----------------|-------------------|------------|
| Album + Track | `albums` | Tracks embebidos en álbumes |
| Customer + Invoice + InvoiceLine | `customers` | Facturas embebidas con líneas |
| Employee + Customer | `employees` | Clientes asignados embebidos |
| Playlist + PlaylistTrack | `playlists` | Tracks de playlist embebidos |
| Track + sales stats | `tracks` | Estadísticas calculadas embebidas |

### Campos Calculados Agregados
- **total_spent**: Gasto total por cliente
- **sales.total_quantity**: Cantidad total vendida por track
- **sales.total_revenue**: Ingresos totales por track

## 🔍 Consultas Implementadas

El proyecto incluye comparaciones directas entre SQL y MongoDB para:

1. **Búsqueda de playlists por track**: 
   - SQL: JOIN entre playlist y playlist_track para encontrar playlists que contienen un track específico
   - MongoDB: Búsqueda directa en array embebido `tracks.track_id`

2. **Clientes atendidos por empleado**: 
   - SQL: JOIN entre customer y employee por support_rep_id
   - MongoDB: Acceso directo a array embebido `customers` en colección employees

3. **Ranking de clientes por gasto total**: 
   - SQL: GROUP BY con SUM de invoice_line (unit_price * quantity) ordenado DESC
   - MongoDB: Ordenamiento directo por campo precalculado `total_spent`

4. **Tracks más vendidos por género**: 
   - SQL: JOIN de track, invoice_line, GROUP BY género con SUM de ventas
   - MongoDB: Filtro por `genre_id` y ordenamiento por `sales.total_revenue`

5. **Tracks más vendidos por país**: 
   - SQL: JOIN complejo de track, invoice_line, invoice, customer por billing_country
   - MongoDB: Pipeline de agregación con $unwind de invoices y agrupación por track_id

6. **Duración promedio de tracks en álbum**: 
   - SQL: AVG(milliseconds) filtrado por album_id
   - MongoDB: Pipeline de agregación con $unwind de tracks y $avg de milliseconds

7. **Relaciones empleado-cliente**: 
   - SQL: JOIN entre customer y employee para mostrar representante de ventas
   - MongoDB: Pipeline con $unwind de customers y concatenación de nombres

8. **Jerarquía organizacional (empleado-manager)**: 
   - SQL: Self-JOIN de employee para mostrar relaciones reports_to
   - MongoDB: Campo `reports_to` con nombre del manager precalculado

9. **Historial detallado de compras por cliente**: 
   - SQL: JOIN múltiple de invoice, customer, invoice_line, track para detalle completo
   - MongoDB: Pipeline complejo con $unwind de invoices/invoice_lines y $lookup de tracks

## 🚀 Configuración e Instalación

### Prerequisitos
```bash
pip install psycopg2-binary pymongo[srv] pandas
```

### Variables de Entorno
El proyecto utiliza las siguientes conexiones:
- **PostgreSQL**: Render Cloud Database
- **MongoDB**: MongoDB Atlas

### Ejecución
1. Ejecutar las celdas de instalación de dependencias
2. Conectar a ambas bases de datos
3. Ejecutar el script de migración
4. Probar las consultas comparativas

## 📈 Índices Implementados

### Álbumes
- `album_id` (único)
- `artist_id`
- `tracks.track_id`

### Tracks
- `track_id` (único)
- `genre + sales.total_quantity` (compuesto)
- `artist_id`

### Clientes
- `customer_id` (único)
- `total_spent` (descendente)
- `support_rep.employee_id`
- `invoices.invoice_date`
- `invoices.billing_country`

### Empleados
- `employee_id` (único)
- `customers.customer_id`

### Playlists
- `playlist_id` (único)
- `tracks.track_id`

## 🔧 Estructura del Proyecto

```
ChinookMigration/
├── LabFinal.ipynb          # Notebook principal con migración y consultas
├── README.md               # Documentación del proyecto
├── requirements.txt        # Dependencias Python
```

## 📝 Resultados y Conclusiones

### Ventajas del Modelo MongoDB
- **Consultas más simples** para datos relacionados frecuentemente accedidos
- **Mejor rendimiento** en lecturas de documentos completos
- **Flexibilidad de esquema** para futuras extensiones
- **Agregaciones potentes** para análisis de datos

### Consideraciones
- **Duplicación controlada** de datos para optimizar lecturas
- **Tamaño de documentos** balanceado para evitar límites de MongoDB
- **Consistencia eventual** en algunos escenarios de actualización

## 👥 Autores

- **Estudiante**: Rodrigo González Green y Fernando Daniel Mangold Bentancour
- **Curso**: Bases de Datos No Relacionales
- **Institución**: FING
- **Año**: 2025

## 📚 Referencias

- [Chinook Database Original](https://github.com/lerocha/chinook-database)
- [MongoDB Data Modeling Guide](https://docs.mongodb.com/manual/core/data-modeling-introduction/)
- [PostgreSQL to MongoDB Migration Best Practices](https://docs.mongodb.com/guides/server/migration/)

## 📄 Licencia

Este proyecto es desarrollado con fines académicos bajo la misma licencia que el proyecto original Chinook Database.
