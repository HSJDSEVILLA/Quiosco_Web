# Lista de Espera — Dashboard MySQL + Oracle  
Visualizador web para pacientes con estado **PROGRAMADO**

## Descripción
Este script PHP genera una **lista de espera en tiempo real**, combinando información procedente de dos sistemas:

- **MySQL (NemoQ)** → datos operativos del turno/ticket.
- **Oracle (OGS/Clínico)** → estado clínico del acto asociado.

El sistema cruza ambas fuentes y **muestra únicamente los pacientes cuyo estado en Oracle es _PROGRAMADO_**, ordenados por mayor tiempo de espera.

Es una herramienta útil para supervisión asistencial y monitorización de agendas.

---

## Características principales

###  Extracción desde MySQL  
- Consulta sobre la tabla `booked_today` con unión a `ticket`.
- Filtrado por:
  - `printedfrom` dentro del rango `172.31.148.%` (kioscos)
  - Centros `4` y `6`
  - Registros impresos (`printed = 1`)
- Cálculo adicional:
  - Descripción del estado NemoQ  
  - Origen de impresión (Kiosco / Mostrador / Integración)
  - Tiempo de espera en minutos (`TIMESTAMPDIFF`)

###  Cruce con Oracle  
Por cada fila MySQL:
- Se procesa `icu` → `CCA_SID`.
- Consulta en `com_clinical_acts`, `arc_histories`, `sch_consultations`.
- Obtiene:
  - NHC del paciente
  - Estado clínico (`PROGRAMADO`, `ADMITIDO`, etc.)
- **Solo se conservan las filas cuyo estado Oracle es `PROGRAMADO`**.

## Flujo del proceso
1. Conectar a MySQL y ejecutar consulta principal.
2. Recorrer resultados.
3. Calcular `cca_sid` desde `icu`.
4. Ejecutar consulta Oracle .
5. Filtrar solo `PROGRAMADO`.
6. Construir array final.
7. Renderizado de tabla ordenada por `minutos_espera DESC`.

---



