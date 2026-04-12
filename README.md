# 6. Configuración del Entorno y Guía del Proyecto

Este apartado documenta los elementos técnicos fundamentales del proyecto para que cualquier desarrollador pueda comprender, configurar y ejecutar el sistema desde cero. Cubre desde la descripción general de la solución hasta la organización interna del repositorio, pasando por los requisitos de entorno y la arquitectura seleccionada.

---

## 6.1 Descripción del proyecto

Sistema multiplataforma basado en inteligencia artificial para la generación de rutinas de musculación personalizadas. La plataforma analiza evidencia científica deportiva (metaanálisis y estudios en PDF) mediante una arquitectura Data Lakehouse y un motor RAG (Retrieval-Augmented Generation), produciendo planes de entrenamiento respaldados científicamente y adaptados al perfil de cada usuario. Disponible en versión web (React) y móvil Android (Kotlin).

---

## 6.2 Arquitectura seleccionada

Se utiliza una arquitectura Data Lakehouse con patrón Medallón de tres capas:

- **Bronce:** almacenamiento crudo de PDFs científicos y telemetría de usuarios en Supabase Storage.
- **Plata:** datos limpios y normalizados en formato Apache Parquet con gestión transaccional mediante Delta Lake.
- **Oro:** embeddings vectoriales almacenados en PostgreSQL + pgvector (Supabase), listos para consumo por el motor RAG.

El motor de inferencia está construido en Python con LangChain. La exposición de datos se realiza mediante API Gateway (PostgREST) entregando respuestas JSON a los clientes web y móvil.

---

## 6.3 Requisitos y configuración del entorno técnico

| Herramienta | Versión mínima | Uso |
|---|---|---|
| Git | 2.40+ | Control de versiones |
| Docker | 24+ | Contenedores de servicios locales |
| Python | 3.11+ | Motor RAG, scripts de ingesta y procesamiento |
| Node.js | 20+ | Frontend web (React) |
| Android Studio | Hedgehog+ | Desarrollo móvil Kotlin |
| Supabase CLI | 1.150+ | Gestión de base de datos y storage local |
| PostgreSQL | 15+ | Base de datos relacional (incluido en Supabase) |
| pgvector | 0.5+ | Extensión vectorial para búsqueda RAG |

Dependencias Python principales: `langchain`, `sentence-transformers`, `polars`, `duckdb`, `pypdf`, `supabase-py`.

Dependencias Node principales: `react`, `react-router-dom`, `axios`.

### Instrucciones de instalación

1. Clonar el repositorio:
```bash
git clone https://github.com/snozz1001/gimnasio-ia.git
cd gimnasio-ia
```

2. Configurar variables de entorno:
```bash
cp .env.example .env
```

3. Levantar Supabase local con Docker:
```bash
supabase start
```

4. Aplicar migraciones de base de datos:
```bash
supabase db push
```

5. Instalar dependencias Python y ejecutar el pipeline de ingesta:
```bash
cd backend-python
pip install -r requirements.txt
python ingesta/run_pipeline.py
```

6. Instalar dependencias y levantar el frontend web:
```bash
cd frontend-web
npm install
npm run dev
```

7. Abrir el proyecto móvil en Android Studio desde la carpeta `mobile-android/` y ejecutar en emulador o dispositivo físico.

---

## 6.4 Estructura del repositorio

```
gimnasio-ia/
│
├── backend-python/
│   ├── ingesta/          # Scripts de carga y limpieza de PDFs (capas Bronce y Plata)
│   ├── embeddings/       # Generación de vectores con sentence-transformers
│   ├── rag/              # Motor de inferencia LangChain
│   └── requirements.txt
│
├── frontend-web/
│   ├── src/
│   │   ├── components/   # Componentes React reutilizables
│   │   ├── pages/        # Vistas principales (rutinas, perfil, progreso)
│   │   └── services/     # Llamadas al API Gateway
│   └── package.json
│
├── mobile-android/
│   └── app/src/main/
│       ├── ui/           # Pantallas Jetpack Compose
│       ├── viewmodel/    # ViewModels (MVVM)
│       └── data/         # Repositorios y llamadas a Supabase
│
├── supabase/
│   ├── migrations/       # SQL de creación de tablas y configuración RLS
│   └── seed/             # Datos iniciales de ejercicios y estudios base
│
├── docs/
│   ├── DFD.png
│   ├── diccionario_datos.md
│   └── arquitectura.md
│
├── .env.example
└── README.md
```
