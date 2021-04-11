---
title: Risoluzione dei problemi relativi a SQL Insights (anteprima)
description: Risoluzione dei problemi relativi a SQL Insights in monitoraggio di Azure
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 4d4a801d0cf0a2355334272053ff86dd846b6bbf
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030305"
---
# <a name="troubleshooting-sql-insights-preview"></a>Risoluzione dei problemi relativi a SQL Insights (anteprima)
Per risolvere i problemi di raccolta dei dati in SQL Insights, verificare lo stato del computer di monitoraggio nella scheda **Gestisci profilo** . Questo avrà uno degli Stati seguenti:

- Raccolta 
- Non raccolta 
- Raccolta con errori 
 
Fare clic sullo **stato** per eseguire il drill-down per visualizzare i log e altri dettagli, che possono aiutare a risolvere il problema. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Monitoraggio dello stato del computer":::

## <a name="not-collecting-state"></a>Non raccolta stato 
Lo stato del computer di monitoraggio *non viene raccolto* se non sono presenti dati in *InsightsMetrics* per SQL negli ultimi 10 minuti. 

SQL Insights usa la query seguente per recuperare queste informazioni:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Controllare se sono presenti log di Telegraf che consentono di identificare la causa principale del problema. Se sono presenti voci di log, è possibile fare clic su *non raccogliere* e controllare i log e le informazioni per la risoluzione dei problemi comuni. 


Se non sono presenti log, è necessario controllare i log nella macchina virtuale di monitoraggio per i servizi seguenti installati da due estensioni della macchina virtuale:

- Microsoft. Azure. monitor. AzureMonitorLinuxAgent 
  - Servizio: MDSD 
- Microsoft. Azure. monitor. workloads. workload. WLILinuxExtension 
  - Servizio: WLI 
  - Servizio: ms-Telegraf 
  - Servizio: TD-Agent-bit-WLI 
  - Log estensioni per verificare gli errori di installazione:/var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>log del servizio WLI 

Log del servizio: `/var/log/wli.log`

Per visualizzare i log recenti: `tail -n 100 -f /var/log/wli.log`
 

Se viene visualizzato il seguente log degli errori, viene indicato un problema con il servizio **MDSD** .

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Log del servizio Telegraf 

Log del servizio: `/var/log/ms-telegraf/telegraf.log`

Per visualizzare i log recenti: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log` per visualizzare i log degli errori e degli avvisi recenti: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 La configurazione usata da Telegraf viene generata dal servizio WLI e inserita in: `/etc/ms-telegraf/telegraf.d/wli`
 
Se viene generata una configurazione non valida, il servizio MS-Telegraf potrebbe non riuscire ad avviarsi, controllare se il servizio MS-Telegraf è in esecuzione con il comando: `service ms-telegraf status`

Per visualizzare i messaggi di errore dal servizio Telegraf, eseguirlo manualmente con il comando seguente: 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>log del servizio MDSD 

Controllare le [limitazioni correnti](../agents/azure-monitor-agent-overview.md#current-limitations) per l'agente di monitoraggio di Azure. 


Log del servizio:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

Per visualizzare gli errori recenti: `tail -n 100 -f /var/log/mdsd.err`

 Se è necessario contattare il supporto tecnico, raccogliere le seguenti informazioni: 

- Log in `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- Accedi `/var/log/waagent.log` 
- Log in `/var/log/mdsd*`
- File in `/etc/mdsd.d/`
- File `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>Configurazione della macchina virtuale di monitoraggio non valida

Una delle cause dello stato di *non raccolta* è la presenza di una configurazione di macchina virtuale di monitoraggio non valida.  Di seguito è riportata la configurazione predefinita:

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Questa configurazione specifica i token di sostituzione da usare nella configurazione del profilo nella macchina virtuale di monitoraggio. Consente inoltre di fare riferimento a segreti da Azure Key Vault, in modo che non si disponga dei valori Secret in qualsiasi configurazione, che è fortemente consigliata.

#### <a name="secrets"></a>Segreti
I segreti sono token i cui valori vengono recuperati in fase di esecuzione da un Azure Key Vault. Un segreto è definito da una coppia di un Key Vault di riferimento e di un nome di segreto. In questo modo, monitoraggio di Azure può ottenere il valore dinamico del segreto e usarlo in riferimenti alla configurazione downstream.

È possibile definire tutti i segreti necessari nella configurazione, inclusi i segreti archiviati in insiemi di credenziali delle chiavi distinti.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

Le autorizzazioni per accedere al Key Vault vengono fornite a una identità del servizio gestita nella macchina virtuale di monitoraggio. Monitoraggio di Azure prevede che il Key Vault fornisca almeno i segreti per ottenere l'autorizzazione per la macchina virtuale. È possibile abilitarlo dalla portale di Azure, da PowerShell, dall'interfaccia della riga di comando o da Gestione risorse modello.

#### <a name="parameters"></a>Parametri
I parametri sono token a cui è possibile fare riferimento nella configurazione del profilo tramite modelli JSON. I parametri hanno un nome e un valore. I valori possono essere qualsiasi tipo JSON, inclusi oggetti e matrici. Si fa riferimento a un parametro nella configurazione del profilo utilizzandone il nome in questa convenzione `.Parameters.<name>` .

I parametri possono fare riferimento ai segreti in Key Vault usando la stessa convenzione. Ad esempio, `sqlAzureConnections` fa riferimento al segreto `telegrafPassword` usando la convenzione `$telegrafPassword` .

In fase di esecuzione, tutti i parametri e i segreti verranno risolti e Uniti alla configurazione del profilo per costruire la configurazione effettiva da usare nel computer.

> [!NOTE]
> I nomi dei parametri di `sqlAzureConnections` , `sqlVmConnections` e `sqlManagedInstanceConnections` sono tutti necessari nella configurazione anche se non si dispone di stringhe di connessione che verranno fornite per alcune di esse.


## <a name="collecting-with-errors-state"></a>Raccolta con stato errori
Il computer di monitoraggio sarà in stato di *raccolta con errori* se è presente almeno un log *InsightsMetrics* , ma sono presenti anche errori nella tabella delle *operazioni* .

SQL Insights usa le query seguenti per recuperare queste informazioni:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```
WorkloadDiagnosticLogs
| summarize Errors = countif(Status == 'Error')
```

> [!NOTE]
> Se non vengono visualizzati dati nel tipo di dati ' WorkloadDiagnosticLogs ', potrebbe essere necessario aggiornare il profilo di monitoraggio per archiviare questi dati.  In SQL Insights UX selezionare ' Gestisci profilo ', quindi selezionare ' modifica profilo ', quindi selezionare ' Aggiorna profilo di monitoraggio '.


Per i casi comuni, sono disponibili informazioni sulla risoluzione dei problemi nella visualizzazione log: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Risoluzione dei problemi di visualizzazione log.":::



## <a name="next-steps"></a>Passaggi successivi

- Ottenere informazioni dettagliate sull' [Abilitazione di SQL Insights](sql-insights-enable.md).
