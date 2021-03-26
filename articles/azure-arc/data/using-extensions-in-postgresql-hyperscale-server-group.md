---
title: Usare le estensioni di PostgreSQL
description: Usare le estensioni di PostgreSQL
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e247e372237572586e5a4647d24d9ed6067ea823
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104949788"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Usare le estensioni di PostgreSQL nel gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc

PostgreSQL è al meglio quando viene usato con le estensioni. Infatti, un elemento chiave della nostra funzionalità di iperscalabilità è l' `citus` estensione fornita da Microsoft che viene installata per impostazione predefinita, che consente a Postgres di partizionare in modo trasparente i dati tra più nodi.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="supported-extensions"></a>Estensioni supportate
Le estensioni standard [`contrib`](https://www.postgresql.org/docs/12/contrib.html) e le estensioni seguenti sono già distribuite nei contenitori del gruppo di server di iperscalabilità di PostgreSQL abilitato per Azure Arc:
- [`citus`](https://github.com/citusdata/citus), v: 9,4. L'estensione CITUS per i [dati CITUS](https://www.citusdata.com/) viene caricata per impostazione predefinita in quanto porta la funzionalità di iperscalabilità nel motore PostgreSQL. Non è possibile eliminare l'estensione CITUS dal gruppo di server di iperscalabilità di Azure Arc PostgreSQL.
- [`pg_cron`](https://github.com/citusdata/pg_cron), v: 1,2
- [`pgaudit`](https://www.pgaudit.org/), v: 1,4
- plpgsql, v: 1,0
- [`postgis`](https://postgis.net), v: 3.0.2
- [`plv8`](https://plv8.github.io/), v: 2.3.14

Gli aggiornamenti apportati a questo elenco verranno pubblicati nel corso del tempo.

> [!IMPORTANT]
> Sebbene sia possibile portare al gruppo di server un'estensione diversa da quelle elencate in precedenza, in questa versione di anteprima non verrà salvata in modo permanente nel sistema. Ciò significa che non sarà disponibile dopo il riavvio del sistema ed è necessario riportarlo.

In questa guida verrà usato uno scenario per usare due di queste estensioni:
- [`PostGIS`](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)

## <a name="which-extensions-need-to-be-added-to-the-shared_preload_libraries-and-created"></a>Quali estensioni devono essere aggiunte alla shared_preload_libraries e create?

|Estensioni   |È necessario aggiungere a shared_preload_libraries  |Richiede la creazione di |
|-------------|--------------------------------------------------|---------------------- |
|`pg_cron`      |No       |Sì        |
|`pg_audit`     |Sì       |Sì        |
|`plpgsql`      |Sì       |Sì        |
|`postgis`      |No       |Sì        |
|`plv8`      |No       |Sì        |

## <a name="add-extensions-to-the-shared_preload_libraries"></a>Aggiungere estensioni al shared_preload_libraries
Per informazioni dettagliate [su questi shared_preload_libraries leggere la documentazione di](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SHARED-PRELOAD-LIBRARIES)PostgreSQL:
- Questo passaggio non è necessario per le estensioni che fanno parte di `contrib`
- Questo passaggio non è necessario per le estensioni che non sono necessarie per il pre-caricamento da shared_preload_libraries. Per queste estensioni è possibile saltare il paragrafo successivo [creare estensioni](#create-extensions).

### <a name="add-an-extension-at-the-creation-time-of-a-server-group"></a>Aggiungere un'estensione all'ora di creazione di un gruppo di server
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
### <a name="add-an-extension-to-an-instance-that-already-exists"></a>Aggiungere un'estensione a un'istanza già esistente
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```




## <a name="show-the-list-of-extensions-added-to-shared_preload_libraries"></a>Mostra l'elenco di estensioni aggiunte a shared_preload_libraries
Eseguire uno dei comandi seguenti.

### <a name="with-an-azdata-cli-command"></a>Con un comando dell'interfaccia della riga di comando azdata
```console
azdata arc postgres server show -n <server group name>
```
Scorrere l'output e notare le sezioni engine\extensions nelle specifiche del gruppo di server. Ad esempio:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
### <a name="with-kubectl"></a>Con kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Scorrere l'output e notare le sezioni engine\extensions nelle specifiche del gruppo di server. Ad esempio:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


## <a name="create-extensions"></a>Creare estensioni
Connettersi al gruppo di server con lo strumento client desiderato ed eseguire la query standard PostgreSQL:
```console
CREATE EXTENSION <extension name>;
```

## <a name="show-the-list-of-extensions-created"></a>Mostra l'elenco di estensioni create
Connettersi al gruppo di server con lo strumento client desiderato ed eseguire la query standard PostgreSQL:
```console
select * from pg_extension;
```

## <a name="drop-an-extension"></a>Elimina un'estensione
Connettersi al gruppo di server con lo strumento client desiderato ed eseguire la query standard PostgreSQL:
```console
drop extension <extension name>;
```

## <a name="the-postgis-extension"></a>`PostGIS`Estensione
Non è necessario aggiungere l' `PostGIS` estensione a `shared_preload_libraries` .
Ottenere [i dati di esempio](http://duspviz.mit.edu/tutorials/intro-postgis/) dal dipartimento di studi urbani del mit & Planning. Eseguire `apt-get install unzip` per installare unzip in base alle esigenze.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

Connettersi al database e creare l' `PostGIS` estensione:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Se si desidera utilizzare una delle estensioni nel `postgis` pacchetto (ad esempio,,, `postgis_raster` .. `postgis_topology` `postgis_sfcgal` `fuzzystrmatch` .) è necessario innanzitutto creare l'estensione PostGIS e quindi creare l'altra estensione. Ad esempio: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

E creare lo schema:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

A questo punto è possibile combinare `PostGIS` con la funzionalità di scalabilità orizzontale, rendendo distribuita la tabella coffee_shops:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Caricare alcuni dati:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

E riempire il `geom` campo con la latitudine e la longitudine codificate correttamente nel `PostGIS` `geometry` tipo di dati:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

A questo punto è possibile elencare le caffetterie più vicine al MIT (77 Massachusetts Ave a 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


## <a name="the-pg_cron-extension"></a>`pg_cron`Estensione

A questo punto, è `pg_cron` possibile abilitare il gruppo di server PostgreSQL aggiungendolo alla shared_preload_libraries:

```console
azdata postgres server update -n pg2 -ns arc --extensions pg_cron
```

Il gruppo di server viene riavviato per completare l'installazione delle estensioni. Potrebbero essere necessari da 2 a 3 minuti.

È ora possibile connettersi di nuovo e creare l' `pg_cron` estensione:

```sql
CREATE EXTENSION pg_cron;
```

A scopo di test, consente di creare una tabella `the_best_coffee_shop` che accetta un nome casuale dalla `coffee_shops` tabella precedente e inserisce il contenuto della tabella:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

È possibile utilizzare `cron.schedule` più istruzioni SQL, per ottenere un nome di tabella casuale (si noti l'utilizzo di una tabella temporanea per archiviare i risultati di una query distribuita) e archiviarlo in `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

E ora, una volta al minuto, otterremo un nome diverso:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Per informazioni dettagliate sulla sintassi, vedere il [file leggimi pg_cron](https://github.com/citusdata/pg_cron) .


## <a name="next-steps"></a>Passaggi successivi
- Leggi la documentazione su [`plv8`](https://plv8.github.io/)
- Leggi la documentazione su [`PostGIS`](https://postgis.net/)
- Leggi la documentazione su [`pg_cron`](https://github.com/citusdata/pg_cron)