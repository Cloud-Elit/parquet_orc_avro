<a name="br1"></a> 

Formats de données

• HDFS supporte différents formats de données :

ü Formats lisibles par l’homme : json, csv, texte,

images, etc.

ü Formats optimisés pour HDFS : Avro, Parquet,

ORC, etc.

• Les formats Avro, Parquet et ORC partagent

certaines similitudes mais chacun d'eux apporte

ses avantages et ses inconvénients.

•

• Les traits communs :

– Compression de données ;

– Répartition sur plusieurs disques ;

– Schéma de données.

Mohamed TALHA

Développement Big Data

1



<a name="br2"></a> 

Format Parquet (1/3)

• Apache Parquet est un format de fichier open-source pour le

stockage distribué des données volumineuses dans un

environnement Big Data. Les fichiers Parquet sont compatibles

avec de nombreux Frameworks comme Hadoop, Spark et Hive.

**C’est la format par défaut pour Apache Spark**.

• Apache Parquet est un format de stockage de données **orienté**

**colonne**. Les données sont stockées sous forme de **colonnes** :

chaque colonne de données est stockée dans un bloc de fichier

séparé (**chunk**).

Mohamed TALHA

Développement Big Data

2



<a name="br3"></a> 

Format Parquet (2/3)

• Cette structure permet une  lecture  sélective  efficace des

données en ne lisant que les colonnes nécessaires pour une

requête donnée. Parquet est optimisé pour le paradigme WORM

(**write once read many**).

• Comme les valeurs d'une même colonne ont tendance à être

similaires (même type), le stockage des données sous forme de

colonnes (dans des fichiers séparées) permet d’appliquer

une **compression efficace des données** en fonction du type

des données de la colonne. Apache Parquet prend en charge

plusieurs algorithmes de compression comme **Gzip**, **Snappy** et

**LZO**.

• Apache Parquet gère également **le schéma de données** sous

forme de métadonnées stockées à la fin du fichier. Les schémas

de données peuvent être **étendus ou modifiés** avec le

temps sans avoir à réécrire l'ensemble des données.

Mohamed TALHA

Développement Big Data

3



<a name="br4"></a> 

Format Parquet (3/3)

• Un fichier parquet commence par un

Parquet File

numéro "magique" à 4 octets qui vaut

"PAR1", suivi d’un ensemble de

partitions, appelées "groupes de

lignes" (ou **Row Group**), suivies d’un

"**Footer**" contenant des métadonnées

sur le fichier telles que la version de

parquet, le schéma des données, le

type de compression, etc., et se

termine par le numéro "magique" à 4

octets (PAR1).

File

**Header**

metadata

...

Row

Group 0

Row

Group n

PAR1

PAR1

**Footer**

...

Column

Chunk 0

Column

Chunk m

...

Page 0

Page p

• Au sein de chaque "**Row Group**", les

colonnes sont divisées en plusieurs

blocs (**chunk**) et stockées sous forme

de **pages** à l'intérieur des blocs.

Encoded

Data

Page

metadata

• Chaque **page** contient les valeurs de

données codées et des métadonnées

concernant la page.

Mohamed TALHA

Développement Big Data

4



<a name="br5"></a> 

Format ORC (1/2)

• Apache ORC (Optimized Row Columnar) est un format de fichier

open-source pour le stockage distribué des données

volumineuses dans un environnement Big Data. Les fichiers

ORC sont compatibles avec de nombreux Frameworks comme

Hadoop, Spark et Hive.

• Tout comme Parquet, les fichiers ORC sont orientés colonne,

prend en charge plusieurs algorithmes de compression (comme

**Zlib** et **Snappy**) et embarquent les schémas de données.

• Vu les grandes similarité entre Parquet et ORC, le choix entre

l’un ou l’autre pour un projet est difficile, mais :

– Pour **Hive**, il faut privilégier **ORC** pare ce qu’il supporte les transactions

ACID.

– Pour **Spark**, il faut privilégier **Parquet** pour sa capacité de compression de

données (jusqu’à 75% avec Snappy) ce qui réduit les E/S sur disque et

rend Spark SQL plus rapide.

Mohamed TALHA

Développement Big Data

5



<a name="br6"></a> 

Format ORC (2/2)

• Un fichier ORC commence par le texte

"ORC", suivi d’un ensemble de blocs,

appelés "**Stripes**" qui contiennent les

données, suivis d’un "**Footer**".

ORC File

File

metadata

• Un **Stripe** est un bloc de 250MB par défaut.

**Header**

...

Il est composé de 3 éléments :

File Footer

Stripe 0

Stripe n

ORC

– **Index Data** : contiennent les valeurs min et max

pour chaque colonne et les positions des lignes

dans chaque colonne.

Postscript

**Footer**

– **Row Data** : contient les données compressées

Index

Data

Row

Data

Stripe

Footer

de chaque colonne.

– **Stripe Footer** : contient des statistiques clés

pour chaque colonne d'un stripe (nombre de

colonnes, min, max, somme, etc.) ce qui permet

de sauter facilement le stripe si nécessaire.

• Le **Footer** est composé de 3 sections :

Column

1

Column

1

– **File Metadata** : contient diverses informations

statistiques liées aux colonnes.

...

...

– **File Footer** : contient des informations

concernant la liste des stripes dans le fichier, le

nombre de lignes par stripe et le type de

données pour chaque colonne.

Column

m

Column

m

– **Postscript** : contient les informations sur le

fichier telles la version du fichier et les

paramètres de compression, etc.

Mohamed TALHA

Développement Big Data

6



<a name="br7"></a> 

Format AVRO (1/2)

• Avro est un format de fichier **orienté ligne**. Contrairement à Parquet et

ORC, Avro a été conçu pour prendre en charge l'évolution des schémas

de données et assurer le transfert de données dans Hadoop.

• La sérialisation (souvent confondue avec marshalling) est le processus

permettant d’encoder les données dans un format binaire afin de les

transmettre sur le réseau, les écrire dans un fichier ou les sauvegarder

dans une base de données. Le processus inverse, consistant à

décoder les données binaire pour les rendre lisible ou exploitable dans

un langage de programmation, s’appelle la désérialisation (ou

unmarshalling).

• Apache Avro est un format de **sérialisation** de données, compatible

avec différents langages de programmation (Java, C, C++, Python, etc.)

qui est facile à lire et à écrire. Avro embarque le schéma de données

dispensant l'utilisateur d'écrire le code de sérialisation/désérialisation.

Le fichier Avro sérialisé inclut déjà des métadonnées sur le schéma, ce

qui permet de prendre en charge les éventuelles évolutions du schéma

lors de la désérialisation.

Mohamed TALHA

Développement Big Data

7



<a name="br8"></a> 

Format AVRO (2/2)

• Un fichier Avro est composé d’un "**header**"

Avro File

suivi d’un ensemble de blocs de données.

Le header commence par le mot "**Obj**"

suivi de la version d’Avro. La section "**File**

**metadata**" du header contient les

metadata du fichier (au format JSON)

comme le schéma des données (comme le

montre l'exemple ci-contre), le codec, la

compression, etc. La section "**Sync**

**Marker**" du header permet de vérifier que

le fichier de données n’est pas corrompu,

comme un checksum par exemple.

...

**Header**

Bloc 0

Bloc n

meFtaildeata

Sync

Marker

Obj1

*{*

*"type": "record",*

*"namespace": "fr.mtalha",*

*"name": "Informations",*

*"fields": [*

• Les blocs de données contiennent les

données en soit, le plus souvent au format

binaire mais elles peuvent aussi être au

format JSON (si l’on souhaite faire du

débogage par exemple).

*{ "name": "Nom", "type":*

*"string" },*

*{ "name": "Prenom", "type":*

*"string" },*

*{ "name": "Age", "type": "int" }*

*{ "name": "Sexe", "type":*

*"string" }*

Mohamed TALHA

Développement Big Data

*]*

