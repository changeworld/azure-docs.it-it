---
title: Archiviare e visualizzare i dati di diagnostica in Archiviazione di Azure
description: Informazioni su come raccogliere i dati di diagnostica di Azure in un account di Archiviazione di Azure per visualizzarli con uno dei diversi strumenti disponibili.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: application-insights
ms.openlocfilehash: b58249dac5b98bb86c35fae9ac574b6100fa9fd5
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383100"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Archiviare e visualizzare i dati di diagnostica nell'account di archiviazione Azure

> [!IMPORTANT]
> [Servizi cloud di Azure (supporto esteso)](../cloud-services-extended-support/overview.md) è un nuovo modello di distribuzione basato su Azure Resource Manager per il prodotto servizi cloud di Azure.Con questa modifica, i servizi cloud di Azure in esecuzione nel modello di distribuzione basato su Service Manager di Azure sono stati rinominati come servizi cloud (versione classica) e tutte le nuove distribuzioni devono usare i [servizi cloud (supporto esteso)](../cloud-services-extended-support/overview.md).

I dati di diagnostica non vengono archiviati in modo permanente, a meno che non vengano trasferiti nell'Emulatore di archiviazione di Microsoft Azure o nell'archiviazione di Azure. Una volta trasferiti nella risorsa di archiviazione, sono disponibili diversi strumenti per visualizzarli.

## <a name="specify-a-storage-account"></a>Specificare un account di archiviazione
Specificare l'account di archiviazione da usare nel file ServiceConfiguration.cscfg. Le informazioni account vengono definite come stringa di connessione in un'impostazione di configurazione. L'esempio seguente mostra la stringa di connessione predefinita creata per un nuovo progetto di servizio cloud in Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

È possibile modificare questa stringa di connessione per poter specificare le informazioni per un account di archiviazione di Azure.

A seconda del tipo di dati di diagnostica da raccogliere, Diagnostica di Azure usa il servizio BLOB o il servizio tabelle. La tabella seguente mostra le origini dati persistenti e il relativo formato.

| Origine dati | Formato di archiviazione |
| --- | --- |
| Log di Azure |Tabella |
| Log di IIS 7.0 |BLOB |
| Log dell'infrastruttura Diagnostica di Azure |Tabella |
| Log di analisi delle richieste non riuscite |BLOB |
| Registri eventi di Windows |Tabella |
| Contatori delle prestazioni |Tabella |
| Dump di arresto anomalo |BLOB |
| Log degli errori personalizzati |BLOB |

## <a name="transfer-diagnostic-data"></a>Trasferire i dati di diagnostica
Per SDK 2.5 e versioni successive, la richiesta di trasferimento dei dati di diagnostica può verificarsi nel file di configurazione. È possibile trasferire i dati di diagnostica a intervalli pianificati, come specificato nella configurazione.

Per SDK 2.4 e versioni precedenti, è possibile richiedere di trasferire i dati di diagnostica nel file di configurazione oltre che a livello di programmazione. L'approccio programmatico consente anche di eseguire trasferimenti su richiesta.

> [!IMPORTANT]
> Quando si trasferiscono i dati di diagnostica in un account di archiviazione di Azure, si devono sostenere i costi per le risorse di archiviazione usate dai dati di diagnostica.
> 
> 

## <a name="store-diagnostic-data"></a>Archiviare i dati di diagnostica
I dati dei log vengono archiviati nell'archivio BLOB o tabelle con i nomi seguenti:

**Tabelle**

* **WadLogsTable** : log scritti nel codice con il listener di traccia.
* **WADDiagnosticInfrastructureLogsTable** : monitor di diagnostica e modifiche di configurazione.
* **WADDirectoriesTable** : directory monitorate dal monitor di diagnostica.  Sono inclusi i log di IIS, i log delle richieste non riuscite di IIS e le directory personalizzate.  La posizione del file di log dei BLOB è specificata nel campo Container e il nome del BLOB si trova nel campo RelativePath.  Il campo AbsolutePath indica la posizione e il nome del file esistente nella macchina virtuale di Azure.
* **WADPerformanceCountersTable** : contatori delle prestazioni.
* **WADWindowsEventLogsTable**: log eventi di Windows.

**BLOB**

* **wad-control-container** : (solo per SDK 2.4 e versioni precedenti) contiene i file di configurazione XML che controllano Diagnostica di Azure.
* **wad-iis-failedreqlogfiles**: contiene le informazioni dei log delle richieste non riuscite di IIS.
* **wad-iis-logfiles**: contiene le informazioni sui log di IIS.
* **"custom"** : contenitore personalizzato basato sulle directory di configurazione monitorate dal monitor di diagnostica.  Il nome di questo contenitore BLOB verrà specificato in WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Strumenti per visualizzare i dati di diagnostica
Sono disponibili diversi strumenti per visualizzare i dati una volta trasferiti nell'account di archiviazione. Ad esempio:

* Esplora server in Visual Studio: se sono stati installati gli Strumenti di Azure per Microsoft Visual Studio, è possibile usare il nodo Archiviazione di Azure in Esplora server per visualizzare i dati di tabelle e BLOB di sola lettura dagli account di archiviazione di Azure. È possibile visualizzare i dati dall'account dell'emulatore di archiviazione locale e anche dagli account di archiviazione creati per Azure. Per altre informazioni, vedere [Esplorazione e gestione delle risorse di archiviazione con Esplora server](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, OSX e Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) include Azure Diagnostics Manager che consente di visualizzare, scaricare e gestire i dati di diagnostica raccolti dalle applicazioni in esecuzione in Azure.

## <a name="next-steps"></a>Passaggi successivi
[Tracciare il flusso in un'applicazione di Servizi cloud con Diagnostica di Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


