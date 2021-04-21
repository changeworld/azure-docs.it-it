---
title: Gestire le repliche in lettura - Interfaccia della riga di comando di Azure, API REST - Database di Azure per PostgreSQL - Server singolo
description: Informazioni su come gestire le repliche in lettura in Database di Azure per PostgreSQL - Server singolo dall'interfaccia della riga di comando di Azure e dall'API REST
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: d13db238674cae62f528c3d730bf892a72b8f6c2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764694"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Creare e gestire repliche in lettura dall'interfaccia della riga di comando di Azure, dall'API REST

Questo articolo illustra come creare e gestire repliche in lettura in Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure e l'API REST. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).

## <a name="azure-replication-support"></a>Supporto della replica di Azure
[Le repliche in lettura](concepts-read-replicas.md) [e la decodifica logica](concepts-logical.md) dipendono entrambe dal log Write ahead di Postgres (WAL) per informazioni. Queste due funzionalità necessitano di livelli diversi di registrazione da Postgres. La decodifica logica richiede un livello di registrazione superiore rispetto alle repliche in lettura.

Per configurare il livello di registrazione più giusto, usare il parametro supporto per la replica di Azure. Il supporto della replica di Azure include tre opzioni di impostazione:

* **Off:** inserisce le informazioni meno importanti in WAL. Questa impostazione non è disponibile nella maggior parte dei server di Database di Azure per PostgreSQL.  
* **Replica:** più dettagliata rispetto a **Off**. Questo è il livello minimo di registrazione necessario per [il funzionamento delle repliche in](concepts-read-replicas.md) lettura. Questa impostazione è l'impostazione predefinita nella maggior parte dei server.
* **Logica:** più dettagliata rispetto alla **replica.** Questo è il livello minimo di registrazione per il funzionamento della decodifica logica. Anche le repliche in lettura funzionano con questa impostazione.


> [!NOTE]
> Quando si distribuiscono repliche in lettura per carichi di lavoro primari persistenti a elevato utilizzo di scrittura, il ritardo della replica potrebbe continuare ad aumentare e potrebbe non essere mai in grado di raggiungere il database primario. Questo può anche aumentare l'utilizzo dello spazio di archiviazione nella replica primaria perché i file WAL non vengono eliminati fino a quando non vengono ricevuti nella replica.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile creare e gestire repliche in lettura usando l'interfaccia della riga di comando di Azure.

### <a name="prerequisites"></a>Prerequisiti

- [Installare l'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-azure-cli)
- Un [server di Database di Azure per PostgreSQL](quickstart-create-server-up-azure-cli.md) come server primario.


### <a name="prepare-the-primary-server"></a>Preparare il server primario

1. Controllare il valore del server `azure.replication_support` primario. Per il funzionamento delle repliche di lettura, deve essere almeno REPLICA.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Se `azure.replication_support` non è almeno REPLICA, impostarla. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. Riavviare il server per applicare la modifica.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Creare una replica in lettura

Il [comando az postgres server replica create](/cli/azure/postgres/server/replica#az_postgres_server_replica_create) richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Gruppo di risorse in cui verrà creato il server di replica.  |
| name | mydemoserver-replica | Nome del nuovo server di replica creato. |
| source-server | mydemoserver | Nome o ID risorsa del server primario esistente da cui eseguire la replica. Usare l'ID risorsa se si vuole che i gruppi di risorse della replica e del master siano diversi. |

Nell'esempio dell'interfaccia della riga di comando seguente la replica viene creata nella stessa area del master.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Per creare una replica di lettura tra aree, usare il `--location` parametro . L'esempio dell'interfaccia della riga di comando seguente crea la replica negli Stati Uniti occidentali.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo [Concetti relativi alle repliche in lettura](concepts-read-replicas.md). 

Se il parametro non è stato impostato su REPLICA in un server primario per utilizzo generico o ottimizzato per la memoria e il server è stato riavviato, viene `azure.replication_support` visualizzato un errore.  Completare questi due passaggi prima di creare una replica.

> [!IMPORTANT]
> Esaminare la [sezione considerazioni della panoramica della replica di lettura](concepts-read-replicas.md#considerations).
>
> Prima che un'impostazione del server primario venga aggiornata a un nuovo valore, aggiornare l'impostazione della replica su un valore uguale o maggiore. Questa azione consente alla replica di restare al passo con le modifiche apportate al master.

### <a name="list-replicas"></a>Elencare le repliche
È possibile visualizzare l'elenco delle repliche di un server primario usando [il comando az postgres server replica list.](/cli/azure/postgres/server/replica#az_postgres_server_replica_list)

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica
È possibile arrestare la replica tra un server primario e una replica in lettura usando [il comando az postgres server replica stop.](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop)

Dopo l'arresto della replica in un server primario e in una replica in lettura, non è possibile annullarla. La replica in lettura diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Eliminare un server primario o di replica
Per eliminare un server primario o di replica, usare il [comando az postgres server delete.](/cli/azure/postgres/server#az_postgres_server_delete)

Quando si elimina un server primario, la replica in tutte le repliche in lettura viene arrestata. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>API REST
È possibile creare e gestire repliche in lettura usando [l'API REST di Azure.](/rest/api/azure/)

### <a name="prepare-the-primary-server"></a>Preparare il server primario

1. Controllare il valore del server `azure.replication_support` primario. Per il funzionamento delle repliche in lettura, deve essere almeno REPLICA.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Se `azure.replication_support` non è almeno REPLICA, impostarla.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Riavviare il server](/rest/api/postgresql/servers/restart) per applicare la modifica.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Creare una replica in lettura
È possibile creare una replica in lettura usando [l'API di creazione:](/rest/api/postgresql/servers/create)

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo [Concetti relativi alle repliche in lettura](concepts-read-replicas.md). 

Se il parametro non è stato impostato su REPLICA in un server primario per utilizzo generico o ottimizzato per la memoria e il server è stato riavviato, viene `azure.replication_support` visualizzato un errore.  Completare questi due passaggi prima di creare una replica.

Una replica viene creata usando le stesse impostazioni di calcolo e archiviazione del master. Dopo aver creato una replica, è possibile modificare diverse impostazioni indipendentemente dal server primario: generazione di calcolo, vCore, archiviazione e periodo di conservazione del backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.


> [!IMPORTANT]
> Prima che un'impostazione del server primario venga aggiornata a un nuovo valore, aggiornare l'impostazione della replica su un valore uguale o maggiore. Questa azione consente alla replica di tenere il passo con le modifiche apportate al master.

### <a name="list-replicas"></a>Elencare le repliche
È possibile visualizzare l'elenco delle repliche di un server primario usando [l'API dell'elenco di repliche](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica
È possibile arrestare la replica tra un server primario e una replica di lettura usando [l'API di aggiornamento](/rest/api/postgresql/servers/update).

Dopo l'arresto della replica in un server primario e in una replica di lettura, non è possibile annullarla. La replica in lettura diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>Eliminare un server primario o di replica
Per eliminare un server primario o di replica, usare [l'API delete](/rest/api/postgresql/servers/delete):

Quando si elimina un server primario, la replica in tutte le repliche in lettura viene arrestata. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Passaggi successivi
* Vedere altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).
* Informazioni su come [creare e gestire le repliche in lettura nel portale di Azure](howto-read-replicas-portal.md).