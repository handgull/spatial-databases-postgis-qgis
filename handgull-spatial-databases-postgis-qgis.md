## Spatial databases with PostGIS & QGIS cookbook by hangull
Table of Contents
---
---
- [Introduction](#Introduction)
  - [What can a spatial database do for you?](#What-can-a-spatial-database-do-for-you)
- [postgis in action](#postgis-in-action)
  - [Loading spatial data into PostGIS](#Loading-spatial-data-into-PostGIS)
# Introduction
Un "enterprise level" spatial database può essere complicato da installare e gestire.
- **QGIS**<br>
  Open source desktop GIS, usato per mandare query al DB
- **Enterprise GIS**<br>
  Un sistema GIS integrato accessibile tramite l'organizzazione.
- **Spatial database**<br>
  Può essere confuso con l'enterprise gis, ma non sono la stessa cosa:<br>
  Locazione centralizzata per conservare i dati spaziali, necessaria per rendere i dati accessibili tramite l'organizzazione. (uno spatial DB ha speciali features per trattare i dati spaziali e le analisi).<br>
  Spesso la business logic e il controllo degli accessi sono gestiti da DB<br><br>
  **Detail**<br>
  - è un estensione di un esistente RDBMS per gestire i dati spaziali (e.g.):
    - SpatiaLite - File-based (SQLite)
    - PostGIS (PostgreSQL)
    - SQL Server Spatial (SQL Server)
      - non ha le funzioni per trasformare i dati in mezzo a delle coordinate
    - Oracle Spatial (Oracle)
    - ArcSDE (Molti db)
  - Spatial functions (buffer, intersect, ...)
  - SQL/MM Spatial - extends SFS (Standard per l'archiviazione di file multi mediali per le operazioni spaziali)
  - Output dei dati in JSON format (non tutti supportano questa feature, postgis sì) 
  - Raster - tiles in BLOB fields (in postgis ad esempio i raster sono splittati in tiles sotto forma di BLOB nel DB).
  - Spatial Indexes (aiuto per cercare più velocemente ciò che mi serve)
    - Test bounding box relationships first.
    - More complex relationships if the BB test passes.
  - Molti spatial DB si appoggiano su librerie esistenti, per standardizzare le operazioni spaziali
    - GEOS
      - Vector geometry operations
    - PROJ.4
      - Coordinate transformation between CRS's 
    - GDAL
      - Reading and writing raster data
    - OGR
      - Reading and writing vector data
- **PostGIS**<br>
  Una specifica implementazione di database spaziale, costruito sopra a PostgreSQL
- **CLient/server**<br>
  Architettura nella quale un server (non necessariamente solo una macchina) fornisce ad n client le risorse, quando richieste.
## What can a spatial database do for you?
- Speed
  - Altamente ottimizato per conservare erecuperare dati
  - Usa risorse server per le query e l'analisi
- Security
  - Robustezza verso gli "errori interni"
    - Validazione dei dati
    - User authentication
  - Robustezza verso gli attacchi esterni
  - Gestione degli errori software ed hardware
    - Replication (se il server muore ho altri backup in giro)
    - Transactions
- Multi-user editing
- Scalability
  - Replication (se io ho 100 client che richiedono le risorse la risposta potrebbe essere lenta, serve quindi la possibilità di aggiungere un replicate server, per aumentare speed e capability)
- Common data store
  - Integrate non-spatial data
# postgis in action
Aggiungere l'estensione ad un db esistente
```sql
CREATE EXTENSION postgis
```
> Ora da pgAdmin vedremo che sono state aggiunte delle funzioni ed una tabella 'spatial_ref_sys'
## Loading spatial data into PostGIS