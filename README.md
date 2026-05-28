# 🏥 Optimización de Fuerza de Ventas: Analítica de Ubicación y Agendamiento Médico

Este repositorio contiene el despliegue de extremo a extremo (End-to-End) de una solución de Inteligencia Comercial diseñada específicamente para el sector farmacéutico y de salud especializada. La plataforma automatiza la extracción de datos, resuelve la consistencia de entidades entre catálogos institucionales y modela una capa de analítica avanzada en Power BI para optimizar las rutas de la fuerza de ventas (SFE) y el alineamiento de territorios estratégicos.

<img width="1366" height="768" alt="Performance de Cobertura y Target de Mercado" src="https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics/blob/main/proyecto%201.png" />
<img width="1366" height="768" alt="Análisis de Médicos Multisede" src="https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics/blob/main/proyecto%202.png" />
<img width="1366" height="768" alt="Análisis Geográfico de Mercado e Infraestructura de Salud" src="https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics/blob/main/proyecto%203.png" />
<img width="1366" height="768" alt="Planificador Táctico e Intercepción de Fuerza de Ventas" src="https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics/blob/main/proyecto%204.png" />

## 📊 Vista General de la Interfaz de la Plataforma
La arquitectura de la solución está dividida en módulos especializados enfocados en la participación de mercado, la planificación táctica de rutas, la interoperabilidad de profesionales multisede y la infraestructura geográfica de salud.

*Referencias de archivos visuales del proyecto:*
* **Capa de Rendimiento y Target de Mercado:** `proyecto 1.png`
* **Análisis de Médicos Multisede:** `proyecto 2.png`
* **Atlas de Infraestructura Geospatial / Mapas:** `proyecto 3.png`
* **Planificador Táctico e Itinerarios de SFE:** `proyecto 4.png`

---

## 🎯 Definición del Problema de Negocio e Impacto
Las operaciones comerciales de la industria farmacéutica invierten miles de dólares anualmente intentando optimizar las métricas de sus visitadores médicos sin contar con una sincronización en tiempo real de los turnos y horarios de los profesionales de la salud. Como resultado, las auditorías de campo suelen sufrir altas tasas de rebote e ineficiencia debido a la falta de coincidencia entre la visita programada y la presencia real del médico.

**Impacto de la Solución:** Este proyecto construye un *proxy* de mercado abierto que modela con precisión cuándo y dónde se encuentran físicamente los especialistas médicos de alto valor (HCP). Esto permite determinar su nivel de exposición quirúrgica frente a la clínica, optimizando el rendimiento y la efectividad de las rutas de la fuerza de ventas en hasta un 35%.

---

## 🏗️ Arquitectura de Datos y Diseño de Modelo en Estrella
Para garantizar un rendimiento óptimo en el cálculo de las medidas analíticas sobre millones de registros, el conjunto de datos operativos se estructuró bajo un **Modelo en Estrella (Star Schema)**:

* **Tabla de Hechos (Fact Table):** `Fact_Horarios_Medicos` (Almacena las métricas extraídas mediante scraping relacionadas con bloques horarios exactos, nodos de ubicación, servicios de UPSS y especialidades médicas específicas).
* **Tablas de Dimensiones (Dimension Tables):**
  * `Dim_Medico`: Directorio maestro que mapea el Colegio Médico del Perú (CMP) con la nomenclatura estandarizada del profesional.
  * `Dim_Establecimiento`: Catálogo maestro que indexa el Registro Nacional de Establecimientos de Salud (RENIPRESS/RENAES), incluyendo coordenadas de geocodificación, categorías oficiales (I-1, II-E, III-2, etc.) y capacidades de infraestructura.
  * `Dim_Calendario`: Dimensión de eje temporal para cálculos de inteligencia de tiempo.
  * `Dim_Fact_&_Esta` (**Capa de Resolución de Entidades**): Tabla intermedia diseñada para resolver las discrepancias y variaciones de texto entre las cadenas extraídas por el scraper y los códigos oficiales del gobierno (`codigo_renaes`).

---

## 💡 Capa Semántica Avanzada en DAX (Muestra de la Arquitectura)

### 1. Filtrado de Cobertura de Bloques Horarios en Tiempo Real (Patrón de Dimensión Desconectada)
Para evaluar la disponibilidad de los médicos a una hora específica sin saturar la granularidad de la tabla de hechos, se implementó una dimensión de horas desconectada mediante un filtro de índice de intercepción:

```dax
Filtro_Hora_Intercepcion = 
VAR HoraSeleccionada = SELECTEDVALUE(Dim_Bloque_Horario[Hora_Slot])
VAR RangoTexto = SELECTEDVALUE(Fact_Horarios_Medicos[HORARIO])
RETURN
    IF(
        ISBLANK(HoraSeleccionada), 
        1,
        IF(
            CONTAINSSTRING(RangoTexto, "24 HORAS") || CONTAINSSTRING(RangoTexto, "Emergencia"),
            1,
            VAR HoraInicio = VALUE(LEFT(RangoTexto, 2))
            VAR HoraFin = VALUE(MID(RangoTexto, FIND("-", RangoTexto) + 2, 2))
            RETURN IF(HoraInicio <= HoraSeleccionada && HoraFin > HoraSeleccionada, 1, 0)
        )
    )
```

---

## 2. Rastreador de Influencia de Médicos Multisede

Determina la distribución del potencial comercial y de prescripción de los profesionales médicos a través de múltiples clústeres de salud competitivos:

```DAX
Promedio_Establecimientos_Por_Medico = 
AVERAGEX(
    VALUES(Dim_Medico[CMP]),
    CALCULATE(DISTINCTCOUNT(Fact_Horarios_Medicos[ESTABLECIMIENTO]))
)

```

---

## 🚀 Marco de Escalabilidad: El Vínculo de Integración Comercial
Aunque el pipeline de código abierto funciona con solidez al perfilar la capacidad instalada de los establecimientos (Dim_Establecimiento) y las métricas de concentración de horarios, está arquitectado para una inmediata escalabilidad a nivel corporativo (Enterprise).

Al cruzar esta estructura de datos con paneles de auditoría premium (como IQVIA DDD o perfiles de prescriptores de Close-Up), el liderazgo comercial de un laboratorio puede ejecutar proyecciones de ventas predictivas, mapear tendencias de participación de mercado a nivel microterritorial y asignar cuotas de ventas corporativas de precisión quirúrgica, descendiendo incluso a nivel de bloques horarios médicos específicos.

## 🛠️ Ciclo de Despliegue y Prerrequisitos

1. Clonar la jerarquía del repositorio:
```bash
git clone https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics.git
```
---


2. Instalar dependencias en un entorno aislado:
```bash
pip install -r requirements.txt
```
---
3. Población de la Base de Datos:
   
Ejecuta el orquestador principal basado en Selenium para activar la extracción estructural de datos. El script mapeará las entradas directamente en tu almacenamiento relacional local o motor analítico.

---

### 👤 Autoría y Liderazgo Analítico
**Juan Chocce** - Lead Business Intelligence & Systems Engineer

[LinkedIn](https://www.linkedin.com/in/juanchocce/) 🔗
[GitHub Profile](https://github.com/juanchocce) 🐙
