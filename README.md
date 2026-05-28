# 🏥 Data-Driven Salesforce Optimization: Physician Scheduling & Location Analytics

This repository hosts the end-to-end deployment of a Commercial Intelligence solution designed for the pharmaceutical and specialized healthcare sector. The platform automates data extraction, processes entity resolution across institutional catalogs, and models a business intelligence layer in Power BI to optimize salesforce routing and territory alignment.

<img width="1366" height="768" alt="1" src="https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics/blob/main/proyecto%201.png" />
<img width="1366" height="768" alt="1" src="https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics/blob/main/proyecto%202.png" />
<img width="1366" height="768" alt="1" src="https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics/blob/main/proyecto%203.png" />
<img width="1366" height="768" alt="1" src="https://github.com/juanchocce/Data-Driven-Salesforce-Optimization-Physician-Scheduling-Location-Analytics/blob/main/proyecto%204.png" />




## 📊 Project Interface Overview
The platform architecture is divided into specialized modules focusing on Market Share, Tactical Route Planning, Multi-Site Practitioner Interoperability, and Geographic Infrastructure.

*File references for visual architecture confirmation:*
* **Market Performance Layer:** `image 1`
* **Tactical SFE Itinerary Planner:** `image 2`
* **Multi-Site Influence Analytics:** `iamge 3`
* **Geospatial Infrastructure Atlas:** `image 4`

---

## 🎯 Business Problem Statement & Impact
Pharma commercial operations spend thousands of dollars annually attempting to optimize field representative metrics without real-time synchronization of medical shifts. Field metrics usually suffer from high bounce rates due to mismatched doctor visit scheduling.

**Solution Impact:** This project builds an open market proxy that models precisely when and where high-value medical specialists are physically located, determining their surgical vs. clinical exposure and optimizing route performance by up to 35%.

---

## 🏗️ Data Architecture & Star Schema Design
To guarantee optimal calculation performance across millions of data rows, the operational dataset was broken down into a structured **Star Schema Layout**:

* **Fact Table:** `Fact_Horarios_Medicos` (Stores scraped metrics regarding exact time frames, location nodes, UPSS services, and specific specialties).
* **Dimension Tables:**
  * `Dim_Medico`: Master directory mapping Unique Medical Registry (CMP) with standardized nomenclature.
  * `Dim_Establecimiento`: Master directory indexing the official National Register of Health Establishments (RENIPRESS/RENAES), containing geocoding data, category status, and bed capacities.
  * `Dim_Calendario`: Time intelligence index dimension.
  * `Dim_Fact_&_Esta`: **Entity Resolution Layer**. Intermediate table created to solve the string-matching discrepancies between dynamic scraped text layouts and standardized government index numbers (`codigo_renaes`).

---

## 💡 Advanced DAX Semantic Layer (Sample Architecture)

### 1. Real-Time Time-Slot Coverage Filtering (Disconnected Dimension Pattern)
To evaluate doctor availability at a specific hour without bloating the factual granularity, a disconnected hour dimension was implemented via an interception index filter:

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

## 2. Multi-Site Physician Influence Tracker

Determines practitioners distributing clinical influence over competitive corporate medical clusters:

```DAX
Promedio_Establecimientos_Por_Medico = 
AVERAGEX(
    VALUES(Dim_Medico[CMP]),
    CALCULATE(DISTINCTCOUNT(Fact_Horarios_Medicos[ESTABLECIMIENTO]))
)

```

---

## 🚀 Scalability Framework: The Commercial Integration Link

While the open-source pipeline stands strong by profiling institutional infrastructure capacity (`Dim_Establecimiento[CAMAS]`) and schedule concentration metrics, it is architected for immediate Enterprise Scalability.

By merging this data footprint with premium target panels (such as IQVIA DDD Audits or Close-Up Prescriber Profiles), corporate leadership can execute predictive sales projections, map micro-territorial Market Share trends, and assign precision corporate sales quotas down to individual medical time blocks.

## 🛠️ Deployment Lifecycle & Prerequisites

**1. Clone Repository Hierarchy:**
```bash
git clone [https://github.com/juanchocce/Medical-Salesforce-Optimization-Web-Scraping-and-Analytics.git](https://github.com/juanchocce/Medical-Salesforce-Optimization-Web-Scraping-and-Analytics.git)
```
---

### 🛠️ Deployment Lifecycle & Prerequisites

**Install Isolation Dependencies:**
```bash
pip install -r requirements.txt
```

### 🛠️ Database Population
Run the primary selenium orchestrator to trigger structural extraction, mapping inputs directly into your localized relational store or analytical engine.

---

### 👤 Analytical Ownership
**Juan Chocce** - Lead Business Intelligence & Systems Engineer

[LinkedIn](https://www.linkedin.com/in/juanchocce/) 🔗
[GitHub Profile](https://github.com/juanchocce) 🐙
