# Celestial Bodies Database
 
Database relazionale **PostgreSQL** sull'universo, realizzato come progetto del percorso *Relational Database* di [freeCodeCamp](https://www.freecodecamp.org/).
 
Il repository contiene un unico file, `universe.sql`: un dump generato con `pg_dump` che ricrea l'intero database (schema, dati e vincoli).
 
## Schema
 
Il database `universe` contiene 5 tabelle:
 
| Tabella | Contenuto | Relazione |
|---|---|---|
| `galaxy` | 6 galassie (Via Lattea, Andromeda, Triangulum, Nubi di Magellano, Sombrero) | — |
| `star` | 6 stelle | `galaxy_id` → `galaxy` |
| `planet` | 12 pianeti | `star_id` → `star` |
| `moon` | 20 lune | `planet_id` → `planet` |
| `constellation` | 3 costellazioni (Orione, Orsa Maggiore, Cassiopea) | tabella indipendente |
 
```
galaxy 1───N star 1───N planet 1───N moon
 
constellation  (indipendente)
```
 
### Colonne principali
 
- **galaxy**: `name`, `galaxy_types`, `description`, `age_in_millions_of_years`, `distance_from_earth`, `has_life`
- **star**: `name`, `galaxy_id`, `age_in_millions_of_years`, `distance_from_earth`, `is_spherical`, `description`
- **planet**: `name`, `star_id`, `planet_types`, `has_life`, `age_in_millions_of_years`, `distance_from_earth`, `description`
- **moon**: `name`, `planet_id`, `diameter_km`, `is_spherical`, `has_life`, `description`
- **constellation**: `name`, `abbreviation`, `description`
Ogni tabella ha una chiave primaria con sequenza autoincrementale e un vincolo `UNIQUE` sul campo `name`.
 
## Utilizzo
 
Requisiti: PostgreSQL (il dump è stato creato con la versione 12).
 
```bash
psql -U postgres -f universe.sql
```
 
Per connettersi al database:
 
```bash
psql -U postgres -d universe
```
 
> ⚠️ Il file inizia con `DROP DATABASE universe;`. Se il database non esiste ancora, `psql` mostra un errore e continua comunque. Se esiste già, **viene cancellato e ricreato**.
>
> Il dump assegna inoltre la proprietà degli oggetti all'utente `freecodecamp`: fuori dall'ambiente del corso crea quel ruolo oppure rimuovi le righe `OWNER TO`.
 
## Query di esempio
 
```sql
-- Lune di Giove
SELECT m.name, m.diameter_km
FROM moon m
JOIN planet p USING (planet_id)
WHERE p.name = 'Jupiter'
ORDER BY m.diameter_km DESC;
 
-- Numero di pianeti per galassia
SELECT g.name, COUNT(p.planet_id) AS pianeti
FROM galaxy g
JOIN star s USING (galaxy_id)
JOIN planet p USING (star_id)
GROUP BY g.name;
```
 
## Note sui dati
 
I dati del Sistema Solare (pianeti e lune) sono reali. Le righe del tipo "Andromeda Star A" o "LMC Planet A" sono **segnaposto di esempio**, create per popolare le tabelle. L'unità di misura di `distance_from_earth` non è uniforme: per i pianeti del Sistema Solare i valori sembrano espressi in unità astronomiche, per galassie e oggetti extragalattici in anni luce.
 
## Licenza
 
Progetto didattico, nessuna licenza specificata.
