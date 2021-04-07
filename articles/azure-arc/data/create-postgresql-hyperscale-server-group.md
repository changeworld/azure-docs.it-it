---
title: Creare un gruppo di server PostgreSQL Hyperscale abilitato per Azure Arc
description: Creare un gruppo di server PostgreSQL Hyperscale abilitato per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 046f9d80c034e1ac1f2e7ffe144b4f389861b043
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687941"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Creare un gruppo di server PostgreSQL Hyperscale abilitato per Azure Arc

Questo documento descrive i passaggi per creare un gruppo di server con iperscalabilità PostgreSQL in Azure Arc.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Introduzione
Se si ha già familiarità con gli argomenti riportati di seguito, è possibile ignorare questo paragrafo.
È possibile leggere alcuni argomenti importanti prima di procedere con la creazione:
- [Panoramica dei servizi dati abilitati per Azure Arc](overview.md)
- [Modalità e requisiti di connettività](connectivity.md)
- [Concetti relativi alla configurazione dell'archiviazione e all'archiviazione Kubernetes](storage-configuration.md)
- [Modello di risorsa Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Se si preferisce provare gli elementi senza eseguire il provisioning di un ambiente completo, iniziare rapidamente con [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) in Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o in una VM di Azure.


## <a name="login-to-the-azure-arc-data-controller"></a>Accedere al controller dati di Azure Arc

Prima di poter creare un'istanza, è necessario prima accedere al controller dati di Azure Arc. Se è già stato effettuato l'accesso al controller dati, è possibile ignorare questo passaggio.

```console
azdata login
```

Verranno quindi richiesti il nome utente, la password e lo spazio dei nomi System.  

> Se è stato usato lo script per creare il controller dati, lo spazio dei nomi deve essere **Arc**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Passaggio preliminare e temporaneo per solo gli utenti OpenShift
Implementare questo passaggio prima di passare al passaggio successivo. Per distribuire il gruppo di server con iperscalabilità PostgreSQL in Red Hat OpenShift in un progetto diverso da quello predefinito, è necessario eseguire i comandi seguenti sul cluster per aggiornare i vincoli di sicurezza. Questo comando concede i privilegi necessari agli account del servizio che eseguiranno il gruppo di server di iperscala PostgreSQL. Il vincolo del contesto di sicurezza (SCC) Arc-data-SCC è quello aggiunto al momento della distribuzione del controller di dati di Azure Arc.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Nome-gruppo-Server è il nome del gruppo di server che verrà creato nel passaggio successivo.**

Per ulteriori informazioni su SCCs in OpenShift, consultare la documentazione di [OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). È ora possibile implementare il passaggio successivo.


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Creare un gruppo di server PostgreSQL Hyperscale abilitato per Azure Arc

Per creare un gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc sul controller dati Arc, si userà il comando `azdata arc postgres server create` al quale si passeranno diversi parametri.

Per informazioni dettagliate su tutti i parametri che è possibile impostare al momento della creazione, esaminare l'output del comando:
```console
azdata arc postgres server create --help
```

I parametri principali da considerare sono:
- **nome del gruppo di server** che si desidera distribuire. Indicare `--name` o `-n` seguito da un nome la cui lunghezza non deve superare i 11 caratteri.

- **versione del motore PostgreSQL** che si vuole distribuire: per impostazione predefinita è la versione 12. Per distribuire la versione 12, è possibile omettere questo parametro o passare uno dei parametri seguenti: `--engine-version 12` o `-ev 12` . Per distribuire la versione 11, indicare `--engine-version 11` o `-ev 11` .

- **il numero di nodi del** ruolo di lavoro che si desidera distribuire per la scalabilità orizzontale e potenzialmente prestazioni migliori. Prima di procedere, leggere i [concetti relativi a iperscalabilità Postgres](concepts-distributed-postgres-hyperscale.md). Per indicare il numero di nodi del ruolo di lavoro da distribuire, usare il parametro `--workers` o `-w` seguito da un numero intero maggiore o uguale a 2. Ad esempio, se si desidera distribuire un gruppo di server con 2 nodi di lavoro, indicare `--workers 2` o `-w 2` . Vengono creati tre Pod, uno per l'istanza o il nodo coordinatore e due per i nodi/istanze del ruolo di lavoro (uno per ogni ruolo di lavoro).

- **classi di archiviazione** che si desidera utilizzare per il gruppo di server. È importante impostare la classe di archiviazione nel momento in cui si distribuisce un gruppo di server, perché non può essere modificato dopo la distribuzione di. Se si modifica la classe di archiviazione dopo la distribuzione, è necessario estrarre i dati, eliminare il gruppo di server, creare un nuovo gruppo di server e importare i dati. È possibile specificare le classi di archiviazione da usare per i dati, i log e i backup. Per impostazione predefinita, se non si indicano classi di archiviazione, verranno utilizzate le classi di archiviazione del controller di dati.
    - per impostare la classe di archiviazione per i dati, indicare il parametro `--storage-class-data` o `-scd` seguito dal nome della classe di archiviazione.
    - per impostare la classe di archiviazione per i log, indicare il parametro `--storage-class-logs` o `-scl` seguito dal nome della classe di archiviazione.
    - per impostare la classe di archiviazione per i backup: in questa versione di anteprima di Azure Arc abilitata per l'iperscalabilità di PostgreSQL sono disponibili due modi per impostare le classi di archiviazione a seconda dei tipi di operazioni di backup/ripristino che si desidera eseguire. Stiamo lavorando per semplificare questa esperienza. Si indicherà una classe di archiviazione o un montaggio di volume Claim. Un montaggio di attestazioni volume è una coppia di un'attestazione di volume permanente esistente (nello stesso spazio dei nomi) e di un tipo di volume (e metadati facoltativi a seconda del tipo di volume) separati da due punti. Il volume permanente verrà montato in ogni pod per il gruppo di server PostgreSQL.
        - Se si desidera pianificare l'esecuzione di ripristini di database completi, impostare il parametro `--storage-class-backups` o `-scb` seguito dal nome della classe di archiviazione.
        - Se si prevede di eseguire ripristini completi del database e ripristini temporizzati, impostare il parametro `--volume-claim-mounts` o `-vcm` seguito dal nome di un'attestazione volume e di un tipo di volume.

Si noti che quando si esegue il comando create, verrà richiesto di immettere la password dell' `postgres` utente amministratore predefinito. Il nome dell'utente non può essere modificato in questa versione di anteprima. È possibile ignorare il prompt interattivo impostando la `AZDATA_PASSWORD` variabile di ambiente della sessione prima di eseguire il comando create.

### <a name="examples"></a>Esempio

**Per distribuire un gruppo di server di Postgres versione 12 denominata postgres01 con 2 nodi di lavoro che usano le stesse classi di archiviazione del controller dati, eseguire il comando seguente:**
```console
azdata arc postgres server create -n postgres01 --workers 2
```

**Per distribuire un gruppo di server di Postgres versione 12 denominata postgres01 con 2 nodi di lavoro che usano le stesse classi di archiviazione del controller dati per i dati e i log, ma la classe di archiviazione specifica per eseguire il ripristino completo e il ripristino temporizzato, seguire questa procedura:**

 Questo esempio presuppone che il gruppo di server sia ospitato in un cluster Azure Kubernetes Service (AKS). Questo esempio USA azurefile-Premium come nome della classe di archiviazione. È possibile modificare l'esempio seguente in modo che corrisponda all'ambiente in uso. Si noti che per questa configurazione **è necessario AccessModes ReadWriteMany** .  

Per prima cosa, creare un file YAML contenente la descrizione seguente del PVC di backup e denominarlo CreateBackupPVC. yml, ad esempio:
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

Successivamente, creare un PVC usando la definizione archiviata nel file YAML:

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

Successivamente, creare il gruppo di server:

```console
azdata arc postgres server create -n postgres01 --workers 2 -vcm backup-pvc:backup
```

> [!IMPORTANT]
> - Leggere le [limitazioni correnti correlate a backup/ripristino](limitations-postgresql-hyperscale.md#backup-and-restore).


> [!NOTE]  
> - Se il controller dati è stato distribuito usando le `AZDATA_USERNAME` `AZDATA_PASSWORD` variabili di ambiente della sessione e nella stessa sessione terminal, i valori per `AZDATA_PASSWORD` verranno usati anche per distribuire il gruppo di server di scalabilità di PostgreSQL. Se si preferisce usare un'altra password, (1) aggiornare il valore per `AZDATA_PASSWORD` o (2) eliminare la `AZDATA_PASSWORD` variabile di ambiente o (3) eliminare il valore in modo che venga richiesto di immettere una password in modo interattivo quando si crea un gruppo di server.
> - La creazione di un gruppo di server di iperscala PostgreSQL non registrerà immediatamente le risorse in Azure. Come parte del processo di caricamento di [inventario delle risorse](upload-metrics-and-logs-to-azure-monitor.md)  o [dei dati di utilizzo](view-billing-data-in-azure.md) in Azure, le risorse verranno create in Azure e sarà possibile visualizzare le risorse nella portale di Azure.



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Elencare i gruppi di server di iperscala PostgreSQL distribuiti nel controller di dati Arc

Per elencare i gruppi di server con iperscalabilità PostgreSQL distribuiti nel controller Arc data, eseguire il comando seguente:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Ottenere gli endpoint per la connessione ai gruppi di server di scalabilità di PostgreSQL abilitati per Azure Arc

Per visualizzare gli endpoint per un gruppo di server PostgreSQL, eseguire il comando seguente:

```console
azdata arc postgres endpoint list -n <server group name>
```
Ad esempio:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

È possibile usare l'endpoint dell'istanza di PostgreSQL per connettersi al gruppo di server con iperscalabilità PostgreSQL dallo strumento preferito:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [Pgcli](https://www.pgcli.com/) PSQL, pgAdmin e così via. Quando si esegue questa operazione, si esegue la connessione al nodo o all'istanza del coordinatore che si occupa di indirizzare la query ai nodi/istanze del ruolo di lavoro appropriati se sono state create tabelle distribuite. Per informazioni dettagliate, leggere i [concetti relativi all'iperscalabilità di PostgreSQL abilitata per Azure Arc](concepts-distributed-postgres-hyperscale.md).

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Nota speciale sulle distribuzioni delle macchine virtuali di Azure

Quando si usa una macchina virtuale di Azure, l'indirizzo IP dell'endpoint non visualizzerà l'indirizzo IP _pubblico_ . Per individuare l'indirizzo IP pubblico, usare il comando seguente:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Sarà quindi possibile combinare l'indirizzo IP pubblico con la porta per effettuare la connessione.

Potrebbe anche essere necessario esporre la porta del gruppo di server di iperscala PostgreSQL tramite il gateway di sicurezza di rete (NSG). Per consentire il traffico tramite (NSG), è necessario aggiungere una regola che è possibile eseguire usando il comando seguente:

Per impostare una regola, è necessario conoscerne il nome NSG. Per determinare il NSG, usare il comando seguente:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Una volta ottenuto il nome del NSG, è possibile aggiungere una regola del firewall usando il comando seguente. I valori di esempio qui creano una regola NSG per la porta 30655 e consentono la connessione da **qualsiasi** indirizzo IP di origine.  Non si tratta di una procedura consigliata per la sicurezza.  È possibile bloccare meglio gli elementi specificando un valore-Source-prefix-prefissi specifico per l'indirizzo IP del client o un intervallo di indirizzi IP che copre gli indirizzi IP del team o dell'organizzazione.

Sostituire il valore del parametro--Destination-Port-Ranges sotto con il numero di porta ottenuto dal comando ' azdata Arc Postgres server list ' precedente.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Stabilire la connessione con Azure Data Studio

Aprire Azure Data Studio e connettersi all'istanza di con l'indirizzo IP e il numero di porta dell'endpoint esterno e la password specificata al momento della creazione dell'istanza.  Se PostgreSQL non è disponibile nell'elenco a discesa *tipo di connessione* , è possibile installare l'estensione PostgreSQL cercando PostgreSQL nella scheda estensioni.

> [!NOTE]
> Per immettere il numero di porta, è necessario fare clic sul pulsante [Avanzate] nel pannello connessione.

Tenere presente che se si usa una macchina virtuale di Azure, sarà necessario l'indirizzo IP _pubblico_ accessibile tramite il comando seguente:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Connettersi con PSQL

Per accedere al gruppo di server con iperscalabilità PostgreSQL, passare l'endpoint esterno del gruppo di server di iperscala PostgreSQL recuperato in precedenza:

È ora possibile connettersi a PSQL:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Passaggi successivi

- Leggi i concetti e le guide alle procedure per la scalabilità di database di Azure per PostgreSQL per distribuire i dati tra più nodi di scalabilità PostgreSQL e per trarre vantaggio da prestazioni migliori, potenzialmente:
    * [Nodi e tabelle](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinare il tipo di applicazione](../../postgresql/concepts-hyperscale-app-type.md)
    * [Scegliere una colonna di distribuzione](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Condivisione delle tabelle](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuire e modificare tabelle](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Progettare un database multi-tenant](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Progettare un dashboard di analisi in tempo reale](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Nei documenti precedenti ignorare le sezioni accedere al **portale di Azure**& **creare un database di Azure per PostgreSQL-iperscalabilità (CITUS)**. Implementare i passaggi rimanenti nella distribuzione di Azure Arc. Queste sezioni sono specifiche dell'iperscalabilità di database di Azure per PostgreSQL (CITUS) offerta come servizio PaaS nel cloud di Azure, ma le altre parti dei documenti sono direttamente applicabili all'iperscalabilità di PostgreSQL abilitata per Azure Arc.

- [Scalabilità orizzontale del gruppo di server con iperscalabilità di Azure Arc abilitato per PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
- [Concetti relativi alla configurazione dell'archiviazione e all'archiviazione Kubernetes](storage-configuration.md)
- [Espansione di attestazioni di volume permanenti](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Modello di risorsa Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
