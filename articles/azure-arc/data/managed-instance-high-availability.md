---
title: Azure Arc abilitato Istanza gestita disponibilità elevata
titleSuffix: Deploy Azure Arc enabled Managed Instance with high availability
description: Informazioni su come distribuire Azure Arc abilitato Istanza gestita con disponibilità elevata.
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: conceptual
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 92f5c900238fc5d40e22870e2f00f8adeb5d335f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032195"
---
# <a name="azure-arc-enabled-managed-instance-high-availability"></a>Azure Arc abilitato Istanza gestita disponibilità elevata

Azure Arc Enabled Istanza gestita viene distribuito in Kubernetes come applicazione in contenitori e USA costrutti Kubernetes, ad esempio set con stato e archiviazione persistente, per fornire il monitoraggio dell'integrità integrato, il rilevamento degli errori e i meccanismi di failover per mantenere l'integrità del servizio. Per una maggiore affidabilità, è anche possibile configurare Azure Arc Enabled Istanza gestita per la distribuzione con repliche aggiuntive in una configurazione a disponibilità elevata. Il monitoraggio, il rilevamento degli errori e il failover automatico sono gestiti dal controller dati di Arc Data Services. Questo servizio opera senza l'intervento dell'utente, dall'impostazione del gruppo di disponibilità, configurando gli endpoint di mirroring dei database per l'aggiunta dei database al gruppo di disponibilità o per il coordinamento del failover e dell'aggiornamento. In questo documento vengono esaminati entrambi i tipi di disponibilità elevata.

## <a name="built-in-high-availability"></a>Disponibilità elevata predefinita 

La disponibilità elevata incorporata viene fornita da Kubernetes quando l'archiviazione permanente remota viene configurata e condivisa con i nodi utilizzati dalla distribuzione di Arc Data Service. In questa configurazione Kubernetes svolge il ruolo di agente di orchestrazione del cluster. Quando l'istanza gestita in un contenitore o nel nodo sottostante ha esito negativo, l'agente di orchestrazione avvia un'altra istanza del contenitore e si connette alla stessa risorsa di archiviazione permanente. Questo tipo è abilitato per impostazione predefinita quando si distribuisce Azure Arc abilitato Istanza gestita.

### <a name="verify-built-in-high-availability"></a>Verificare la disponibilità elevata incorporata

In questa sezione viene verificata la disponibilità elevata incorporata fornita da Kubernetes. Quando si seguono i passaggi per testare questa funzionalità, eliminare il pod di un'istanza gestita esistente e verificare che Kubernetes venga recuperata da questa azione. 

### <a name="prerequisites"></a>Prerequisiti

- Il cluster Kubernetes deve avere [Shared Storage Remote](storage-configuration.md#factors-to-consider-when-choosing-your-storage-configuration) 
- Azure Arc abilitato Istanza gestita distribuito con una replica (impostazione predefinita)

1. Visualizzare i pod. 

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

2. Eliminare il Pod dell'istanza gestita.

   ```console
   kubectl delete pod <name of managed instance>-0 -n <namespace of data controller>
   ```

   Ad esempio:

   ```output
   user@pc:/# kubectl delete pod sql1-0 -n arc
   pod "sql1-0" deleted
   ```

3. Visualizzare i pod per verificare che l'istanza gestita venga recuperata.

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

   Ad esempio:

   ```output
   user@pc:/# kubectl get pods -n arc
   NAME                 READY   STATUS    RESTARTS   AGE
   sql1-0               2/3     Running   0          22s
   ```

Dopo il ripristino di tutti i contenitori all'interno del Pod, è possibile connettersi all'istanza gestita.

## <a name="deploy-with-always-on-availability-groups"></a>Distribuisci con Gruppi di disponibilità Always On

Per una maggiore affidabilità, è possibile configurare Azure Arc Enabled Istanza gestita per la distribuzione con repliche aggiuntive in una configurazione a disponibilità elevata. 

Funzionalità abilitate dai gruppi di disponibilità:

- Quando vengono distribuiti con più repliche, viene creato un singolo gruppo di disponibilità denominato `containedag` . Per impostazione predefinita, `containedag` è costituito da tre repliche, inclusa quella primaria. Tutte le operazioni CRUD per il gruppo di disponibilità vengono gestite internamente, inclusa la creazione del gruppo di disponibilità o l'aggiunta di repliche al gruppo di disponibilità creato. Non è possibile creare gruppi di disponibilità aggiuntivi in Azure Arc abilitato Istanza gestita.

- Tutti i database vengono aggiunti automaticamente al gruppo di disponibilità, inclusi tutti i database utente e di sistema come `master` e `msdb`. Questa funzionalità offre una vista a singolo sistema di tutte le repliche del gruppo di disponibilità. Si notino i `containedag_master` `containedag_msdb` database e se ci si connette direttamente all'istanza. I database `containedag_*` rappresentano i database `master` e `msdb` all'interno del gruppo di disponibilità.

- Viene effettuato il provisioning automatico di un endpoint esterno per la connessione ai database all'interno del gruppo di disponibilità. Questo endpoint `<managed_instance_name>-svc-external` ha il ruolo di listener del gruppo di disponibilità.

### <a name="deploy"></a>Distribuisci

Per distribuire un'istanza gestita con i gruppi di disponibilità, eseguire il comando seguente.

```console
azdata arc sql mi create -n <name of instance> --replicas 3
```

### <a name="check-status"></a>Controlla stato
Una volta distribuita l'istanza, eseguire i comandi seguenti per verificare lo stato dell'istanza:

```console
azdata arc sql mi list
azdata arc sql mi show -n <name of instance>
```

Output di esempio:

```output
user@pc:/# azdata arc sql mi list
ExternalEndpoint    Name    Replicas    State
------------------  ------  ----------  -------
20.131.31.58,1433   sql2    3/3         Ready

user@pc:/#  azdata arc sql mi show -n sql2
{
...
  "status": {
    "AGStatus": "Healthy",
    "externalEndpoint": "20.131.31.58,1433",
    "logSearchDashboard": "link to logs dashboard",
    "metricsDashboard": "link to metrics dashboard",
    "readyReplicas": "3/3",
    "state": "Ready"
  }
}
```

Si noti il numero aggiuntivo di `Replicas` e il `AGstatus` campo che indica lo stato del gruppo di disponibilità. Se tutte le repliche sono attivate e sincronizzate, questo valore è `healthy` . 

### <a name="restore-a-database"></a>Ripristinare un database 
Per ripristinare un database in un gruppo di disponibilità sono necessari passaggi aggiuntivi. Nei passaggi seguenti viene illustrato come ripristinare un database in un'istanza gestita e aggiungerlo a un gruppo di disponibilità. 

1. Esporre l'endpoint esterno dell'istanza primaria creando un nuovo servizio Kubernetes.

    Determinare il pod che ospita la replica primaria connettendosi all'istanza gestita ed eseguire:

    ```sql
    SELECT @@SERVERNAME
    ```
    Creare il servizio kubernetes nell'istanza primaria eseguendo il comando seguente se il cluster kubernetes usa i servizi Deport. Sostituire `podName` con il nome del server restituito nel passaggio precedente, `serviceName` con il nome preferito per il servizio Kubernetes creato.

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    Per un servizio LoadBalancer, eseguire lo stesso comando, ad eccezione del fatto che il tipo di servizio creato è `LoadBalancer` . Ad esempio: 

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    Di seguito è riportato un esempio di questo comando eseguito sul servizio Azure Kubernetes, in cui il pod che ospita il database primario è `sql2-0` :

    ```bash
    kubectl -n arc-cluster expose pod sql2-0 --port=1533  --name=sql2-0-p --type=LoadBalancer
    ```

    Ottenere l'indirizzo IP del servizio Kubernetes creato:

    ```bash
    kubectl get services -n <namespaceName>
    ```
2. Ripristinare il database all'endpoint dell'istanza primaria.

    Aggiungere il file di backup del database nel contenitore di istanze primario.

    ```console
    kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>
    ```

    Esempio

    ```console
    kubectl cp /home/WideWorldImporters-Full.bak sql2-1:var/opt/mssql/data/WideWorldImporters-Full.bak -c arc-sqlmi -n arc
    ```

    Ripristinare il file di backup del database eseguendo il comando seguente.

    ```sql 
    RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
    WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
    ,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
    ,RECOVERY, REPLACE, STATS = 5;  
    GO
    ```
    
    Esempio

    ```sql
    RESTORE Database WideWorldImporters
    FROM DISK = '/var/opt/mssql/data/WideWorldImporters-Full.BAK'
    WITH
    MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf',
    MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_UserData.ndf',
    MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf',
    MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1',
    RECOVERY, REPLACE, STATS = 5;  
    GO
    ```

3. Aggiungere il database al gruppo di disponibilità.

    Per aggiungere il database al gruppo di disponibilità, è necessario eseguirlo in modalità di recupero con registrazione completa e deve essere eseguito un backup del log. Eseguire le istruzioni TSQL riportate di seguito per aggiungere il database ripristinato al gruppo di disponibilità.

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    L'esempio seguente aggiunge un database denominato `WideWorldImporters` che è stato ripristinato nell'istanza:

    ```sql
    ALTER DATABASE WideWorldImporters SET RECOVERY FULL;
    BACKUP DATABASE WideWorldImporters TO DISK='/var/opt/mssql/data/WideWorldImporters.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE WideWorldImporters
    ```

> [!IMPORTANT]
> Come procedura consigliata, eseguire la pulizia eliminando il servizio Kubernetes creato in precedenza tramite questo comando:
>
>```bash
>kubectl delete svc sql2-0-p -n arc
>```

### <a name="limitations"></a>Limitazioni

Azure Arc abilitato Istanza gestita i gruppi di disponibilità hanno le stesse [limitazioni dei gruppi di disponibilità del cluster di Big Data. Per ulteriori informazioni, fare clic qui.](/sql/big-data-cluster/deployment-high-availability#known-limitations)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su [caratteristiche e funzionalità di Istanza gestita di SQL con abilitazione di Azure Arc](managed-instance-features.md)
