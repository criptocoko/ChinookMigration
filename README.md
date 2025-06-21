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

1. **Búsqueda en playlists**: Tracks en playlists específicas
2. **Relaciones empleado-cliente**: Clientes atendidos por empleado
3. **Análisis de ventas**: Clientes ordenados por gasto total
4. **Ventas por género**: Tracks más vendidos por género musical
5. **Análisis geográfico**: Ventas por país
6. **Estadísticas de álbum**: Duración promedio de tracks
7. **Jerarquías organizacionales**: Reportes empleado-manager
8. **Historial de compras**: Detalle completo de facturas por cliente

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
