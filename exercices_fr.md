# Introduction à la gestion des bases de données avec des logiciels libres

## Accéder à DuckDB

**Sur Windows et Mac**:

- Trouvez le fichier executable (duckdb.exe) sur votre ordinateur et cliquez dessus. 

Cette approche va utiliser une base de données temporaire qui sera effacée quand vous quitterez DuckDB. 

Pour créer une base de données persistante. Trouvez le terminal (Powershell) dans votre liste d'applications. Allez vers le dossier dans lequel vous avez extrait duckdb.exe.

```
cd c://Users/MyUser/Downloads/duckdb/
```

Ensuite
```
duckdb.exe mydb.duckdb
```

**Sur Linux** Tapez simplement:

    duckdb mydb.duckdb

dans un terminal.


# Discussion de groupe

[Cliquez ici pour télécharger le jeu de données (feuille de calcul en
format LibreOffice
Calc)](http://qcbs.ca/wp-content/uploads/2012/10/Crustacean_plankton_Canada.ods)

  
  
  
# Exercices DuckDB


## Commandes de base



Voir la liste des tables:

``` DuckDB
SHOW TABLES;
```

Voir les colonnes dans une table

``` DuckDB
DESCRIBE nom_de_la_table;
```


Pour quitter DuckDB, faites CTRL-D sur votre clavier. 



## Exercice 1 - déclarations CREATE et INSERT

Formats de colonnes les plus communs

| fonction   | description                                   | exemple                               |
|------------|-----------------------------------------------|---------------------------------------|
| int        | integer                                       | 5                                     |
| decimal(,) | decimal(longueur totale, nombre de décimales) | 122.52                                |
| varchar()  | character(longueur maximale)                  | 'hirondelle bicolore'                 |
| text       | texte d'une longueur non-définie              | 'Il était une fois une hirondelle...' |
| timestamp  | date et heure                                 | '2012-10-21 10:03:21'                 |

[Liste complète des type de données
DuckDB.](https://duckdb.org/docs/current/sql/data_types/overview)


Créer la séquence pour la clé primaire 
``` sql
CREATE SEQUENCE oiseaux_id_seq START 1;
```

Créer la table

``` sql
CREATE TABLE obs_oiseaux (obs_id INT PRIMARY KEY DEFAULT nextval('oiseaux_id_seq'),
obs_time timestamp,espece text,nombre integer,lat real,"long" real);
```

Note: nous utilisons les doubles guillements "" pour marquer
l'utilisation d'un mot réservé, d'un mot avec des caractères spéciaux ou
des espaces dans le nom d'une table ou d'une colonne.

Insérer une nouvelle ligne dans la table

``` sql
INSERT INTO obs_oiseaux (obs_time,espece,nombre,lat,"long") VALUES ('2012-06-19 12:31:16','Sturnus vulgaris',40,45.3522,-73.7930);
```

Notez que nous n'avons pas entré le champ obs_id puisqu'il est entré
automatiquement avec le type de caractère SERIAL, tel que spécifié lors
de la création de la table.

Pour visualiser la table

``` sql
SELECT * FROM obs_oiseaux;
```

**Question 1** Reproduisez la table suivante dans DuckDB, nommez
la 'acteur' et visualisez là dans DuckDB.

| prenom | nom_de_famille | date_naissance | sexe | grandeur | marque_distinctive                                                                                                                     |
|--------|----------------|----------------|------|----------|----------------------------------------------------------------------------------------------------------------------------------------|
| Bruce  | Willis         | 1955-03-19     | M    | 1.81     | Joue fréquemment des hommes qui souffrent d'une tragédie, ont perdu quelque chose, ou qui ont une crise de confiance ou de conscience. |
| Emma   | Watson         | 1990-04-15     | F    | 1.65     | Joue souvent des personnages littéraires.                                                                                              |

**Note**: quand on entre du texte, il faut doubler les ' dans le texte
('') pour éviter que DuckDB ne les utilise comme marqueur de fin de
l'entrée texte.

## Exercice 2 - Importer des données dans DuckDB

Téléchargez les fichiers suivants sur votre ordinateur (utilisez le
bouton de droite pour cliquer sur le lien... sauvergarder sous).
[Lakes.csv](./data/lakes.csv), [species_acro.csv](./data/species_acro.csv),
[lakes_species.csv](./data/lakes_species.csv)

Sous Windows: copiez ces trois fichiers vers un dossier nommé 'bdlibre'
dans le répertoire C: (i.e. C:/bdlibre). Sous Mac ou Linux: copiez ces
trois fichiers vers un dossier facilement accessible (e.g.
/home/votrenome/bdlibre). Modifiez les commandes plus bas en remplaçant
"C:/bdlibre" avec le chemin vers ce dossier.

Créer une table contenant l'information environnementale de chaque
lac. - **Lakes**.

``` sql
CREATE SEQUENCE lakes_id_seq START 1;
CREATE TABLE lakes (lake_id INT PRIMARY KEY DEFAULT nextval('lakes_id_seq'),numero INT, lake_name text, 
province text, latitude DEC(10,5),longitude DEC(10,5), number_of_species INT, ecoprov VARCHAR(50), 
ecozone VARCHAR(50), gss DEC(10,2),gse DEC(10,2), gsl DEC(10,2), gdd10 DEC(10,2), egdd DEC(10,2), 
mean_ele DEC(10,2), pe_ann_p DEC(10,2), totp_ann DEC(10,2), srann_me DEC(10,2), shann_me DEC(10,2), 
tmax_ann DEC(10,2), tmean_an DEC(10,2), vpann_me DEC(10,2));
```

Charger le fichier CSV dans cette table.

``` sql
COPY lakes FROM './data/lakes.csv' WITH csv HEADER DELIMITER AS ',';
```

Créez la table contentant les présences de espèces pour chaque lac et
charger le fichier CSV dans cette table.- **lakes_species**

``` sql
CREATE TABLE lakes_species (lake_id INT NOT NULL, species_id INT NOT NULL);
COPY lakes_species FROM './data/lakes_species.csv' WITH csv HEADER DELIMITER AS ',';
```

Pour la troisième table, nous pourrions utiliser cette commande:

### Commande

``` sql
CREATE TABLE species_acro (species_id INT NOT NULL, full_name varchar(100), short_name varchar(25));
COPY species_acro FROM './data/species_acro.csv' WITH csv HEADER DELIMITER AS ',';
```

Par contre, nous allons utiliser DBeaver pour se familiariser avec
l'interface.

## Importation de données dans DBeaver

#### Importation d'une table

Pour la troisième table, nous allons utiliser l'interface de DBeaver. Cliquez avec le bouton de gauche de la souris sur Tables dans Schemas>public dans le menu de gauche, et sélectionnez Import Data. Cliquez sur Next et choisissez le fichier CSV species_acro.csv sur votre ordinateur. Dans Tables mapping, assurez-vous que le nom de la table, le nom des colonnes et le format des colonnes est adéquat. Après avoir cliqué sur Next à nouveau, cliquez sur Proceed. 


## RÉFÉRENCES

**Fonctions mathématiques et d'aggrégation (GROUP BY)**

| Fonction | Description |
|----------|-------------|
| AVG() | La moyenne |
| COUNT(DISTINCT COLUMN) | Le nombre d'entrées distinctes dans une colonne |
| COUNT() | Le nombre de lignes |
| GROUP_CONCAT() | Concaténation de multiples entrées textuelles |
| MAX() | Valeur maximale |
| MIN() | Valeur minimale |
| STDDEV_POP() | Déviation standard de la population |
| STDDEV_SAMP() | Déviation standard de l'échantillon |
| SUM() | Somme |
| VAR_POP() | Variance de la population |
| VAR_SAMP() | Variance de l'échantillon |

[Liste complète](https://duckdb.org/docs/current/sql/functions/aggregates)

**Sélection conditionnelle pour utilisation dans une clause 'WHERE'**

|            |                                                              |
|:-----------|--------------------------------------------------------------|
| =          | Égal                                                         |
| \>         | Plus grand que                                               |
| \<         | Moins que                                                    |
| \>=        | Plus grand que ou égal                                       |
| \< =       | Plus petit que ou égal                                       |
| \<\> or != | Non-égalité                                                  |
| LIKE       | Requête d'équivalence d'entrées textuelles (string matching) |

**Ordre des opérations dans une requête 'SELECT'**:

    SELECT columns FROM tables WHERE conditions JOIN GROUP BY columns HAVING condition ORDER BY columns;


## Exercice 3 - Importer des donnees dans SQLite avec R

Installez d'abord le paquet `RSQLite` :

```r
install.packages('RSQLite')
```

Pour l'utiliser dans R, chargez le paquet `DBI` :

```r
library(DBI)
```

Ensuite, creez le fichier qui contiendra la base de donnees :

```r
mydb <- dbConnect(RSQLite::SQLite(), "QCBS_Workshop.sqlite")
```

Definissez le dossier de travail vers l'emplacement ou vous avez telecharge les fichiers :

```r
setwd('C:/User/MyName/Workshop/')
```

Vous pouvez maintenant charger les fichiers CSV dans R comme data frames :

```r
lakes <- read.csv('lakes.csv')
lakes_species <- read.csv('lakes_species.csv')
species_acro <- read.csv('species_acro.csv')
```

Puis charger ces data frames dans la base de donnees :

```r
dbWriteTable(mydb, "lakes", lakes)
dbWriteTable(mydb, "lakes_species", lakes_species)
dbWriteTable(mydb, "species_acro", species_acro)
dbListTables(mydb)
```

Vous pourrez ensuite executer la plupart des requetes ci-dessous avec `dbGetQuery`. Par exemple :

```r
lake_prairies <- dbGetQuery(mydb, "SELECT * FROM lakes WHERE ecozone='Prairies'")
```

## Exercice 4 - commande SELECT

Sélectionnez toutes les lignes de la table Lakes

``` sql
SELECT * FROM lakes;
```

Si vous voulez voir toutes les colonnes
``` sql
.mode box
```

Si vous voulez voir toutes les valeurs, par ligne
``` sql
.mode line
```

Retourner au mode par défaut 
``` sql
.mode duckdb
```

Sélectionner toutes les lignes, mais n'afficher que les noms des lacs et
la province

``` sql
SELECT lake_name, province FROM lakes;
```

Voir tous les noms de provinces différents

``` sql
SELECT DISTINCT province from lakes;
```

Voir tous le noms de provinces, utiliser une alias (renommer cette
colonne dans les résultats) et trier par ordre descendant de nom.

``` sql
SELECT DISTINCT province as "province name" FROM lakes ORDER BY province DESC;
```

Voir l'année de naissance de chaque acteur

``` sql
SELECT prenom, nom_de_famille, EXTRACT(year from date_naissance) FROM acteur;
```

Voir les initiales de chaque acteur

``` sql
SELECT prenom, nom_de_famille, concat(substr(prenom,1,1),' ',substr(nom_de_famille,1,1)) as initiales FROM acteur;
```

Sélectionner tous les lacs du Québec où la température annuelle moyenne
est en bas de -5 C.

``` sql
SELECT lake_name, tmean_an FROM lakes WHERE province='QUEBEC' AND tmean_an<-5;
```

Ou, pour compter le nombre de lacs

``` sql
SELECT count(*) FROM lakes WHERE province='QUEBEC' AND tmean_an<-5;
```

**Question 1** - Combien de lacs en Colombie-Britannique reçoivent
plus de 3000 mm de précipitation (colonne totp_ann)?  
<details>
<summary>Réponse</summary>
12
</details> 

``` sql
SELECT count(*) FROM lakes WHERE totp_ann>3000 AND province='BRITISH COLUMBIA';
```

**Question 2** - Quel est l'altitude moyenne (colonne mean_ele) de
tous les lacs dans l'écozone 'Montane Cordillera' (utilisez la fonction
avg())?  
<details>
<summary>Réponse</summary>
1364.36
</details> 


``` sql
SELECT avg(mean_ele) FROM lakes WHERE ecozone='Montane Cordillera';
```

Note: Le symbole % est utilisé pour remplacer le début ou la fin d'une
entrée textuelle.  
Sélectionnez tous les noms de lacs qui contiennent le mot 'Small'

``` sql
SELECT lake_name FROM lakes WHERE lake_name like '%Small%';
```

Sélectionnez tous les noms de lacs qui contiennent le mot 'Small' et qui
ne sont pas situés en Ontario

``` sql
SELECT lake_name, province 
FROM lakes
WHERE lake_name like '%Small%' AND province!='ONTARIO';
autre possibilité:
SELECT lake_name, province 
FROM lakes 
WHERE lake_name like '%Small%' AND province NOT LIKE 'ONTARIO';
```

**Question 3** - Quelle est la latitude maximale de tous les lacs
dans un Ecozone dont le nom commence par 'Taiga'

<details>
<summary>Réponse</summary>
68.8
</details> 

``` sql
SELECT max(latitude) FROM lakes WHERE ecozone LIKE 'Taiga%';
```

**Question 4** - Combien d'espèces de Daphnia sont-elles listées
dans la table species_acro?


<details>
<summary>Réponse</summary>
17 (21 ont le mot 'Daphnia' quelque part dans leur nom)

``` sql
SELECT count(*) FROM species_acro WHERE full_name like 'Daphnia%';
```
</details> 


## Exercice 5 - REGROUPEMENT

Calculez la température annuelle moyenne de tous les lacs dans chaque
province

``` sql
SELECT province, avg(tmean_an) as mean_an_t
FROM  lakes
GROUP BY province;
```

Même table, mais triée par ordre de températures croissantes

``` sql
SELECT province, avg(tmean_an) as mean_an_t
FROM  lakes
GROUP BY province ORDER BY avg(tmean_an);
```

**Question 5** - Quelle province a le lac ayant la température
maximale (tmax_ann) la plus élevée?

<details> 
<summary>Réponse</summary>
BRITISH COLUMBIA 14.60
</details> 

``` sql
SELECT province, max(tmax_ann) as max_temp FROM lakes GROUP BY province ORDER BY max_temp DESC;
```

Commande 'UPDATE'

Changer toutes les noms de province qui contiennent 'NWT', pour qu'ils
aient le même nom ('NWT'). - **Faites attention, cette opération modifie
la table!**

``` sql
UPDATE lakes
SET province='NWT'
WHERE province like '%NWT%';
```

## Exercice 6 - Travailler avec plusieurs tables

Compter le nombre d'espèces dans chaque lac. Afficher le nom du lac et
le nombre de chaque espèce.

``` sql
SELECT lakes.lake_name, COUNT(DISTINCT species_id) as num_species FROM lakes, lakes_species 
WHERE lakes.lake_id=lakes_species.lake_id 
GROUP BY lake_name, lakes.lake_id
ORDER BY num_species DESC;
```

Même requête, mais on limite l'affichage aux 20 premières entrées.

``` sql
SELECT lake_name, COUNT(DISTINCT species_id) as num_species FROM lakes, lakes_species 
WHERE lakes.lake_id=lakes_species.lake_id 
GROUP BY lakes.lake_name, lakes.lake_id
ORDER BY num_species DESC LIMIT 20;
```

Compter le nombre d'espèces dans chaque Ecozone et Ecoprovince.

``` sql
SELECT ecozone, ecoprov, COUNT(species_id) as num_species FROM lakes, lakes_species 
WHERE lakes.lake_id=lakes_species.lake_id 
GROUP BY ecozone, ecoprov
ORDER BY ecozone, COUNT(species_id);
```

**Question 6** Générer une table qui liste le nom complet de chaque
espèce de Daphnia et le nombre de lacs où on retrouve cette espèce.

<details> 
<summary>Réponse</summary>

``` sql
SELECT full_name, count(DISTINCT b.lake_id) as cnt FROM species_acro a, lakes_species b WHERE full_name like 'Daphnia%' AND a.species_id=b.species_id GROUP BY full_name;
```
</details> 



| full_name                               | cnt |
|-----------------------------------------|-----|
| Daphnia pulex Leydig, 1860              | 234 |
| Daphnia similis Claus, 1876             | 17  |
| Daphnia ambigua Scourfield, 1947        | 59  |
| Daphnia catawba Coker, 1926             | 28  |
| Daphnia dubia Herick, 1883              | 71  |
| Daphnia galeata Sars, 1864              | 17  |
| Daphnia longiremis G. O. Sars, 1862     | 426 |
| Daphnia longispina hyalina ceresiana    | 3   |
| Daphnia longispina hyalina microcephela | 27  |
| Daphnia magna Straus, 1820              | 18  |
| Daphnia mendotae Birge, 1918            | 433 |
| Daphnia middendorffiana Fischer, 1851   | 117 |
| Daphnia parvula Fordyce, 1901           | 28  |
| Daphnia pulicaria Forbes, 1893          | 110 |
| Daphnia retrocurva Forbes, 1882         | 324 |
| Daphnia rosea G.O. Sars, 1862           | 52  |
| Daphnia thorata Forbes, 1893            | 14  |

## Exercice 7 - Opérations de jointures (JOIN)

|                                |                                                                                                                                              |
|--------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|
| JOIN (INNNER JOIN, CROSS JOIN) | Garder toutes les combinaisons possibles des lignes des tables A et B.                                                                       |
| LEFT JOIN                      | Garder toutes les entrées de la table A, et joindre avec les entrées de B qui se trouvent également dans A (via un identificateur conjoint). |
| RIGHT JOIN                     | Garder toutes les entrées de la table B, et joindre avce les entrées de A qui se trouvent également dans B (via un identificateur conjoint). |
| OUTER JOIN                     | Garder toutes les entrées des tables A et B                                                                                                  |

Créer une liste de toutes les espèces présentes dans les lacs du Québec,
avec les noms complets des espèces et le nombre de lacs dans lesquelles
elles sont présentes.

``` sql
SELECT full_name, count(full_name) 
FROM species_acro,lakes_species,lakes 
WHERE species_acro.species_id=lakes_species.species_id AND  lakes.lake_id=lakes_species.lake_id AND province='QUEBEC' 
GROUP BY full_name;
```

De façon équivalente...

``` sql
SELECT full_name, count(full_name) 
FROM lakes
LEFT JOIN lakes_species ON (lakes.lake_id=lakes_species.lake_id)
LEFT JOIN species_acro ON (species_acro.species_id=lakes_species.species_id)
WHERE province='QUEBEC' 
GROUP BY full_name;
```

Pour toutes les combinaisons possibles de lacs se trouvant dans
l'Ecoprovince Baffin Uplands, calculer la différence entre leurs
températures annuelles moyennes.

``` sql
SELECT concat(a.lake_name,'-',b.lake_name) as lakes, a.tmean_an-b.tmean_an as temp_diff 
FROM lakes a, lakes b 
WHERE a.ecoprov='Baffin Uplands' AND b.ecoprov='Baffin Uplands';
```

## Exercice 8 - Sous-requêtes (Subqueries)

Créez une table contenant le fréquence de chaque espèce dans les lacs au
sud et au nord du 50e parallèle (latitude).

``` sql
SELECT a.species_id, count_50south, count_50north
FROM 
 (SELECT species_id, count(d.species_id) as count_50south FROM lakes c, lakes_species d WHERE c.lake_id=d.lake_id AND latitude<=50 GROUP BY species_id) a,
 (SELECT species_id, count(f.species_id) as count_50north FROM lakes e, lakes_species f WHERE e.lake_id=f.lake_id AND latitude>50 GROUP BY species_id) b 
WHERE a.species_id=b.species_id;
```

**Question 7** En utilisant une sous-requête, trouvez l'élévation
moyenne (colonne MEAN_ELE) des lacs dans lesquels au moins une espèce de
Daphnia est présente.  

<details> 
<summary>Réponse</summary>
**Réponse:** 474.627172  
``` sql
SELECT avg(mean_ele) FROM (SELECT DISTINCT a.lake_id, mean_ele
FROM lakes a,species_acro b,lakes_species c WHERE b.full_name like
'Daphnia%' AND a.lake_id=c.lake_id AND c.species_id=b.species_id ORDER
BY a.lake_id) d;
```
</details> 

## Exercice 9 - Travailler avec des fichiers locaux ou à distance


On va créer une VIEW Pour se connecter au fichier d'observation dans l'Atlas de Biodiversité Québec

``` sql
INSTALL spatial;
LOAD spatial;
INSTALL httpfs;
LOAD httpfs;
```


``` sql
CREATE OR REPLACE VIEW atlas AS SELECT * FROM read_parquet('https://object-arbutus.cloud.computecanada.ca/bq-io/atlas/parquet/atlas_public_2026-05-04.parquet');
```

```sql
DESCRIBE atlas;
``

Extraire le nombre d'observations dans Atlas par royaume. 
```sql
SELECT kingdom, count(*) cnt FROM atlas GROUP BY kingdom ORDER BY cnt DESC;
```

Extraire toutes les observations d'harfang des neigs
```sql
SELECT * FROM atlas WHERE valid_scientific_name='Bubo scandiacus';
```


# Lier R avec DuckDB

Dans R: lors de la première utilisation seulement :

```r
install.packages('duckdb')
```

```r
library(duckdb)
con <- dbConnect(duckdb::duckdb(), dbdir = "mydb.duckdb")
lakes <- dbGetQuery(con,'SELECT * FROM lakes');
```

```r
lakesqc <- dbGetQuery(con,"SELECT * FROM lakes WHERE province='QUEBEC'")
hist(lakes$tmean_an)
```

## Utiliser le package dplyr/dbdplyr

```r
lakes <- tbl(con, "lakes") # Define lakes table
lakes_qc<-filter(lakes, province  == 'QUEBEC') # Select lakes in Quebec
prov_tmean<-summarise(group_by(lakes, province), mean(tmean_an)) # Mean annual temperature per province
prov_tmean=collect(prov_tmean) # Transfer result to standard R data frame
lakes_qc2<-tbl(con, sql("SELECT * FROM lakes WHERE province='QUEBEC'")) #Perform any SQL statement
```

## Charger une table comme un objet spatial sf

```r
install.packages('duckspatial')
library(duckspatial)
library(sf)
```

```r
bubo <- ddbs_read_table(conn, "atlas", clauses = "WHERE valid_scientific_name='Bubo scandiacus'")
```

bubo est un objet spatial sf. Vous pouvez donc le visualiser sur une carte. 
```r 
plot(bubo['dataset_name'])
```

# Utilisation de l'interface LibreOffice Base

## Créer des tables et des formulaires

On vous donne un jeu de données dans le format suivant

| Parcelle 1         |
|--------------------|
| Espèce             |
| Acer rubrum        |
| Acer saccharum     |
| Fagus grandifolia  |
| Fagus grandifolia  |
| Fraxinus americana |
| Parcelle 2         |
| Quercus rubra      |
| Fraxinus americana |
| Fraxinus americana |
| Carpinus caroliana |

On vous dit que des milliers d'autres arbres devront être entrés.
Comment pensez-vous construire une base de données pour entreposer cette
information?

Créez la table suivante avec la commande 'CREATE TABLE' dans
DuckDB. Spécifiez le champs espece_id comme la clé primaire avec le
type de fichier SERIAL PRIMARY KEY. Vous pouvez ensuite remplir la table
avec les valeurs dans Libreoffice Base. Nom de la table: especes_arbres

| espece_id | nom_espece         |
|-----------|--------------------|
| 1         | Quercus rubra      |
| 2         | Acer saccharum     |
| 3         | Acer rubrum        |
| 4         | Fagus grandifolia  |
| 5         | Fraxinus americana |
| 6         | Carpinus caroliana |

Créez cette table dans DuckDB et créez un formulaire pour la
remplir avec le mode ébauche de formulaire, avec des menus déroulants
pour sélectionner les espèces et des boutons radios pour la santé des
arbres. arbre_id est la clé primaire. Nom de la table: parcelles_arbres

| parcelle_id | arbre_id | espece_id | dhp  | etat   | commentaires            |
|-------------|----------|-----------|------|--------|-------------------------|
| 1           | 1        | 3         | 12.4 | vivant |                         |
| 1           | 2        | 2         | 25.3 | vivant |                         |
| 1           | 3        | 4         | 14.1 | mort   |                         |
| 1           | 4        | 4         | 66.0 | vivant |                         |
| 1           | 5        | 4         | 30.1 | vivant | écorce endommagée       |
| 2           | 6        | 1         | 40.1 | vivant |                         |
| 2           | 7        | 5         | 64.2 | vivant | gros trou dans le tronc |
| 2           | 8        | 5         | 53.1 | mort   | déraciné                |
| 2           | 9        | 6         | 10.3 | vivant |                         |

