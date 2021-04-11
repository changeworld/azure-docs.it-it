---
title: Gestire l'accesso ai dati di Sentinel di Azure per risorsa | Microsoft Docs
description: Questo articolo illustra come gestire l'accesso ai dati di Sentinel di Azure dalle risorse a cui un utente può accedere. La gestione dell'accesso per risorsa consente di fornire l'accesso solo a specifici dati, senza l'intera esperienza di Azure Sentinel. Questo metodo è noto anche come controllo degli accessi in base al contesto delle risorse.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: fc1246d079760fd86513840aebbffa34d192f8ed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044176"
---
# <a name="manage-access-to-azure-sentinel-data-by-resource"></a>Gestire l'accesso ai dati di Sentinel di Azure per risorsa

In genere, gli utenti che hanno accesso a un'area di lavoro di Azure Sentinel possono accedere a tutti i dati dell'area di lavoro, incluso il contenuto di sicurezza. Gli amministratori possono usare i [ruoli di Azure](roles.md) per configurare l'accesso a funzionalità specifiche in Sentinel di Azure, a seconda dei requisiti di accesso del team.

Tuttavia, è possibile che alcuni utenti debbano accedere solo a specifici dati nell'area di lavoro di Azure Sentinel, ma non devono avere accesso all'intero ambiente Sentinel di Azure. Ad esempio, è possibile fornire un team di operazioni non di sicurezza (non SOC) con accesso ai dati degli eventi di Windows per i server di loro proprietà.

In questi casi, è consigliabile configurare il controllo degli accessi in base al ruolo (RBAC) in base alle risorse consentite agli utenti, invece di fornire loro l'accesso all'area di lavoro di Azure Sentinel o a specifiche funzionalità di Sentinel di Azure. Questo metodo è noto anche come impostazione del controllo degli accessi in base al **contesto delle risorse**.

Quando gli utenti hanno accesso ai dati di Sentinel di Azure tramite le risorse a cui possono accedere anziché l'area di lavoro di Azure Sentinel, possono visualizzare i log e le cartelle di lavoro usando i metodi seguenti:

- **Tramite la risorsa stessa**, ad esempio una macchina virtuale di Azure. Utilizzare questo metodo per visualizzare i log e le cartelle di lavoro solo per una risorsa specifica.

- **Tramite monitoraggio di Azure**. Utilizzare questo metodo quando si desidera creare query che si estendono su più risorse e/o gruppi di risorse. Quando si passa a log e cartelle di lavoro in monitoraggio di Azure, definire l'ambito per uno o più gruppi di risorse o risorse specifiche.

Abilita il controllo degli accessi in base al contesto delle risorse in monitoraggio di Azure Per altre informazioni, vedere [gestire l'accesso ai dati di log e alle aree di lavoro in monitoraggio di Azure](../azure-monitor/logs/manage-access.md).

> [!NOTE]
> Se i dati non sono una risorsa di Azure, ad esempio i dati syslog, CEF o AAD oppure i dati raccolti da un agente di raccolta personalizzato, è necessario configurare manualmente l'ID risorsa usato per identificare i dati e abilitare l'accesso.
>
> Per altre informazioni, vedere [configurare in modo esplicito](#explicitly-configure-resource-context-rbac)il controllo degli accessi in base al contesto.
>
## <a name="scenarios-for-resource-context-rbac"></a>Scenari di controllo degli accessi in base al contesto delle risorse

La tabella seguente illustra gli scenari in cui il controllo degli accessi in base al contesto delle risorse è particolarmente utile. Si notino le differenze nei requisiti di accesso tra i team SOC e i team non SOC.

| Tipo di requisito |Team SOC  |Team non SOC  |
|---------|---------|---------|
|**Autorizzazioni**     | Intera area di lavoro        |   Solo risorse specifiche      |
|**Accesso ai dati**     |  Tutti i dati nell'area di lavoro       | Solo i dati per le risorse a cui il team è autorizzato ad accedere        |
|**Esperienza**     |  Esperienza completa di Sentinel di Azure, possibilmente limitata dalle [autorizzazioni funzionali](roles.md) assegnate all'utente       |  Solo query di log e cartelle di lavoro       |
|     |         |         |

Se il team ha requisiti di accesso simili a quelli descritti nella tabella precedente, il controllo degli accessi in base al contesto delle risorse può essere una soluzione ideale per la propria organizzazione.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>Metodi alternativi per implementare il controllo delle risorse RBAC

A seconda delle autorizzazioni richieste nell'organizzazione, l'uso del controllo degli accessi in base al contesto delle risorse potrebbe non offrire una soluzione completa.

Nell'elenco seguente vengono descritti gli scenari in cui altre soluzioni per l'accesso ai dati possono soddisfare meglio i requisiti:

|Scenario  |Soluzione  |
|---------|---------|
|**Una filiale ha un team SoC che richiede un'esperienza completa di Sentinel per Azure**.     |  In questo caso, utilizzare un'architettura a più aree di lavoro per separare le autorizzazioni per i dati. <br><br>Per altre informazioni, vedere: <br>- [Estendi Azure Sentinel tra aree di lavoro e tenant](extend-sentinel-across-workspaces-tenants.md)<br>    - [Lavorare con gli eventi imprevisti in molte aree di lavoro contemporaneamente](multiple-workspace-view.md)          |
|**Si vuole fornire l'accesso a un tipo specifico di evento**.     |  Fornire ad esempio un amministratore di Windows con accesso agli eventi di sicurezza di Windows in tutti i sistemi. <br><br>In questi casi, utilizzare il controllo degli accessi in base al ruolo di [tabella](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) per definire le autorizzazioni per ogni tabella.       |
| **Limitare l'accesso a un livello più granulare, non basato sulla risorsa, oppure solo a un subset dei campi in un evento**   |   Ad esempio, potrebbe essere necessario limitare l'accesso ai log di Office 365 in base alla filiale di un utente. <br><br>In questo caso, fornire l'accesso ai dati tramite l'integrazione incorporata con [Power BI Dashboard e report](../azure-monitor/visualize/powerbi.md).      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>Configura esplicitamente il controllo degli accessi in base al contesto

Usare la procedura seguente se si vuole configurare il controllo degli accessi in base al contesto delle risorse, ma i dati non sono una risorsa di Azure.

Ad esempio, i dati nell'area di lavoro di Azure Sentinel che non sono risorse di Azure includono i dati syslog, CEF o AAD oppure i dati raccolti da un agente di raccolta personalizzato.

**Per configurare esplicitamente** il controllo degli accessi in base al contesto delle risorse:

1. Assicurarsi di aver abilitato il controllo degli accessi in base al [contesto delle risorse](../azure-monitor/logs/manage-access.md) in monitoraggio di Azure. 

1. [Creare un gruppo di risorse](../azure-resource-manager/management/manage-resource-groups-portal.md) per ogni team di utenti che deve accedere alle risorse senza l'intero ambiente Sentinel di Azure.

    Assegnare le [autorizzazioni di lettura log](../azure-monitor/logs/manage-access.md#resource-permissions) per ogni membro del team.

1. Assegnare le risorse ai gruppi del team di risorse creati e contrassegnare gli eventi con gli ID di risorsa pertinenti.

    Quando le risorse di Azure inviano dati ad Azure Sentinel, i record del log vengono automaticamente contrassegnati con l'ID risorsa dell'origine dati.

    > [!TIP]
    > Si consiglia di raggruppare le risorse a cui si concede l'accesso in base a un gruppo di risorse specifico creato per lo scopo.
    >
    > Se non è possibile, assicurarsi che il team disponga di autorizzazioni di lettura log direttamente per le risorse a cui si vuole accedere.
    >

    Per ulteriori informazioni sugli ID di risorsa, vedere:

    - [ID risorsa con l'invio del log](#resource-ids-with-log-forwarding)
    - [ID risorsa con raccolta logstash](#resource-ids-with-logstash-collection)
    - [ID di risorsa con la raccolta di API Log Analytics](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>ID risorsa con l'invio del log

Quando gli eventi vengono raccolti utilizzando il [formato CEF (Common Event Format)](connect-common-event-format.md) o [syslog](connect-syslog.md), l'invio del log viene utilizzato per raccogliere eventi da più sistemi di origine.

Ad esempio, quando una macchina virtuale di inoltro CEF o syslog è in ascolto delle origini che inviano eventi syslog e le inoltra ad Azure Sentinel, l'ID risorsa della macchina virtuale di inoltro del log viene assegnato a tutti gli eventi inoltrati.

Se si dispone di più team, assicurarsi di disporre di VM di invio di log separate che elaborano gli eventi per ogni team separato.

La separazione delle macchine virtuali, ad esempio, garantisce che gli eventi syslog che appartengono al team A vengano raccolti usando la macchina virtuale dell'agente di raccolta A.

> [!TIP]
> - Quando si usa una macchina virtuale locale o un'altra macchina virtuale cloud, ad esempio AWS, come server d'invio, assicurarsi che disponga di un ID risorsa implementando [Azure Arc](../azure-arc/servers/overview.md).
> - Per ridimensionare l'ambiente di macchina virtuale per l'invio del log, provare a creare un [set di scalabilità di macchine virtuali](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) per raccogliere i log CEF e sylog.


### <a name="resource-ids-with-logstash-collection"></a>ID risorsa con raccolta logstash

Se si raccolgono i dati usando il plug-in di [output logstash](connect-logstash.md)di Azure Sentinel, usare il campo **azure_resource_id** per configurare l'agente di raccolta personalizzato in modo da includere l'ID risorsa nell'output.

Se si usa il controllo degli accessi in base al contesto delle risorse e si vuole che gli eventi raccolti dall'API siano disponibili per utenti specifici, usare l'ID risorsa del gruppo di risorse [creato per gli utenti](#explicitly-configure-resource-context-rbac).

Il codice seguente, ad esempio, Mostra un file di configurazione logstash di esempio:

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> Potrebbe essere necessario aggiungere più `output` sezioni per distinguere i tag applicati a eventi diversi.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>ID di risorsa con la raccolta di API Log Analytics

Quando si esegue la raccolta usando l'API dell'agente di raccolta [dati log Analytics](../azure-monitor/logs/data-collector-api.md), è possibile assegnare agli eventi con un ID risorsa usando l'intestazione della richiesta HTTP [*x-ms-AzureResourceId*](../azure-monitor/logs/data-collector-api.md#request-headers) .

Se si usa il controllo degli accessi in base al contesto delle risorse e si vuole che gli eventi raccolti dall'API siano disponibili per utenti specifici, usare l'ID risorsa del gruppo di risorse [creato per gli utenti](#explicitly-configure-resource-context-rbac).


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [autorizzazioni in Sentinel di Azure](roles.md).