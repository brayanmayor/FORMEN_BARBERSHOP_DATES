# 💈 FORMEN BARBERSHOP DATES  
#### ✅ Proyecto de ETL con MinIO, PostgreSQL, Docker, Python y Drive escritorio

---

## 🌍 CONTEXTO DE LA EMPRESA

- **Ubicación:** Centro comercial Cañaveralejo  
- **Ciudad:** Cali  
- **Nombre Barbería:** Formen Barber Shop 💇‍♂️  
- **Años de Antigüedad:** 2 años  
- **Empleados actuales:** 2

---

## 📂 Estructura del Proyecto

```plaintext
ETL_FormenBarbershop/
├── data/              # Archivos fuente y transformados
├── scripts/           # Código Python por etapa
│   ├── extract.py
│   ├── transform.py
│   └── load.py
├── config/            # Parámetros de conexión y rutas
├── logs/              # Registro de ejecución
└── README.md          # Documentación del proyecto
```

---

## 🚀 Requisitos Previos

Antes de ejecutar el proyecto, asegúrate de tener instaladas las siguientes herramientas:

- 🐳 **Docker y Docker Compose** – Para levantar contenedores de servicios como PostgreSQL y MinIO  
- 📦 **MinIO** – Almacenamiento tipo S3 para el Data Lake  
- 🛢️ **PostgreSQL** – Base de datos relacional para la carga final  
- 💾 **Google Drive (modo escritorio)** – Para acceder a los archivos CSV sincronizados localmente  
- 📊 **Power BI** – Para visualización de los datos cargados

---

## 🐳 Levantar el Contenedor de Base de Datos

En la raíz del proyecto, ejecutar:

```bash
docker-compose up -d
```

🌐 Esto iniciará un contenedor con PostgreSQL en el puerto `5432`.

🔐 **Credenciales de conexión:**

- Host: `localhost`  
- Port: `5432`  
- Database: `bd`  
- User: `arq`  
- Password: `password`

---

## 🗄️ Proceso de Insert

En la unidad compartida del Drive se encuentran los archivos en formato CSV.  
Por medio de Python se aplicó la conexión con MinIO para insertar los datos en un Data Lake.  
El nombre del folder es **Raw**, donde diariamente se depositará el dataset.

---

## 🔥 Proceso de ETL

### 1. Extracción (`extract.py`)
- Fuente: Drive local (CSV o Excel)
- Herramienta: `pandas`
- Salida: `data/raw_data.csv`

```python
import pandas as pd
ruta = r"G:\Unidades compartidas\ManBarberShop\29092025\Reporte_Colaboradores.xlsx"
df = pd.read_excel(ruta)
df.to_csv("data/raw_data.csv", index=False)
```

---

### 2. Transformación (`transform.py`)
- Limpieza de filas vacías y totales
- Normalización de nombres de columnas
- Conversión de tipos

```python
df = pd.read_csv("data/raw_data.csv")
df = df[~df["Producto"].str.upper().isin(["TOTAL", ""])]
df.columns = df.columns.str.strip().str.lower().str.replace(" ", "_")
df.to_csv("data/clean_data.csv", index=False)
```

---

### 3. Carga (`load.py`)
- Destino: PostgreSQL
- Herramienta: `sqlalchemy` + `psycopg2`

```python
from sqlalchemy import create_engine
df = pd.read_csv("data/clean_data.csv")
engine = create_engine("postgresql+psycopg2://arq:password@localhost:5432/bd")
df.to_sql("stg1_principal", engine, if_exists="replace", index=False)
```

---

## 📈 Visualización

- Power BI conectado a PostgreSQL  
- Dashboards con métricas de ventas, servicios y colaboradores

---

## 🛡️ Validaciones

- Verificación de columnas requeridas (`producto`, `cantidad`, `valor`, `ganancia`)  
- Control de errores por campos faltantes  
- Registro de errores en `logs/error.log`

---

## 👤 Autor

**Brayan**  
Ingeniero Industrial | Especialización en Big Data  
Apasionado por la estadística aplicada, visualización efectiva y automatización de procesos analíticos.

---
