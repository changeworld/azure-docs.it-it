---
title: Connetti i dati di Microsoft 365 Defender ad Azure Sentinel | Microsoft Docs
description: Informazioni su come inserire eventi imprevisti, avvisi e dati di eventi non elaborati da Microsoft 365 Defender ad Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: 6500805a4dc7e26f5e1bc601df9ea78279ae17e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709343"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Connettere i dati da Microsoft 365 Defender ad Azure Sentinel

> [!IMPORTANT]
>
> **Microsoft 365 Defender** era noto in precedenza come **Microsoft Threat Protection** o **MTP**.
>
> **Microsoft Defender for endpoint** era noto in precedenza come **Microsoft Defender Advanced Threat Protection** o **MDATP**.
>
> È possibile visualizzare i nomi precedenti ancora in uso per un certo periodo di tempo.

## <a name="background"></a>Sfondo

Il connettore [Microsoft 365 Defender (M365D)](/microsoft-365/security/mtp/microsoft-threat-protection) di Azure Sentinel consente di trasmettere tutti gli avvisi e gli eventi imprevisti M365D in Sentinel di Azure e di mantenere sincronizzati gli eventi imprevisti tra entrambi i portali. Gli eventi imprevisti di M365D includono tutti gli avvisi, le entità e altre informazioni rilevanti, che vengono arricchiti da e raggruppano gli avvisi da M365D's Component Services **Microsoft Defender for endpoint**, **Microsoft Defender for Identity**, **Microsoft defender per Office 365** e **Microsoft cloud app Security**.

Il connettore consente anche di trasmettere gli eventi di **caccia avanzati** da Microsoft Defender per l'endpoint in Azure Sentinel, consentendo di copiare le query di ricerca avanzate MDE in Azure Sentinel, arricchire gli avvisi di Sentinel con i dati degli eventi non elaborati MDE per fornire informazioni aggiuntive e archiviare i log con una maggiore conservazione in log Analytics.

Per ulteriori informazioni sull'integrazione degli eventi imprevisti e sulla raccolta di eventi di caccia avanzata, vedere [Microsoft 365 Defender Integration with Azure Sentinel](microsoft-365-defender-sentinel-integration.md).

> [!IMPORTANT]
>
> Il connettore Microsoft 365 Defender è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre di una licenza valida per Microsoft 365 Defender, come descritto in [prerequisiti di Microsoft 365 Defender](/microsoft-365/security/mtp/prerequisites). 

- È necessario essere un **amministratore globale** o un **amministratore della sicurezza** in Azure Active Directory.

## <a name="connect-to-microsoft-365-defender"></a>Connettersi a Microsoft 365 Defender

1. In Sentinel di Azure selezionare **connettori dati**, selezionare **Microsoft 365 Defender (anteprima)** dalla raccolta e selezionare **Apri connettore pagina**.

1. In **configurazione** nella sezione **Connetti eventi imprevisti & avvisi** fare clic sul pulsante **Connetti eventi imprevisti & avvisi** .

1. Per evitare la duplicazione degli eventi imprevisti, è consigliabile contrassegnare la casella di controllo **Disattiva tutte le regole di creazione degli eventi imprevisti Microsoft per questi prodotti.**

    > [!NOTE]
    > Quando si Abilita il connettore Microsoft 365 Defender, tutti i connettori dei componenti di M365D (quelli citati all'inizio di questo articolo) vengono automaticamente connessi in background. Per disconnettere uno dei connettori dei componenti, è necessario innanzitutto disconnettere il connettore Microsoft 365 Defender.

1. Per eseguire query sui dati degli eventi imprevisti di M365 Defender, utilizzare l'istruzione seguente nella finestra di query:
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. Se si desidera raccogliere gli eventi di caccia avanzati da Microsoft Defender per l'endpoint, è possibile raccogliere i tipi di eventi seguenti dalle tabelle di caccia avanzate corrispondenti.

    1. Contrassegnare le caselle di controllo delle tabelle con i tipi di evento che si desidera raccogliere:

       | Nome tabella | Tipo di eventi |
       |-|-|
       | DeviceInfo | Informazioni sul computer (incluse le informazioni sul sistema operativo) |
       | DeviceNetworkInfo | Proprietà di rete dei computer |
       | DeviceProcessEvents | Creazione di processi ed eventi correlati |
       | DeviceNetworkEvents | Connessione di rete ed eventi correlati |
       | DeviceFileEvents | Creazione di file, modifiche e altri eventi file system |
       | DeviceRegistryEvents | Creazione e modifica delle voci del registro di sistema |
       | DeviceLogonEvents | Accessi e altri eventi di autenticazione |
       | DeviceImageLoadEvents | Eventi di caricamento DLL |
       | DeviceEvents | Tipi di eventi aggiuntivi |
       | DeviceFileCertificateInfo | Informazioni sui certificati dei file firmati |
       |

    1. Fare clic su **Applica modifiche**.

    1. Per eseguire una query sulle tabelle di caccia avanzate in Log Analytics, immettere il nome della tabella dall'elenco precedente nella finestra query.

## <a name="verify-data-ingestion"></a>Verificare l'inserimento dei dati

Il grafico dei dati nella pagina del connettore indica che i dati vengono inseriti. Si noterà che viene visualizzata una riga per ogni evento imprevisto, avvisi ed eventi e la riga Events è un'aggregazione di volume di eventi in tutte le tabelle abilitate. Dopo aver abilitato il connettore, è possibile usare le query KQL seguenti per generare grafici più specifici.

Usare la query KQL seguente per un grafico degli eventi imprevisti di M365 Defender in ingresso:

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

Usare la query KQL seguente per generare un grafico del volume dell'evento per una singola tabella (modificare la tabella *DeviceEvents* nella tabella desiderata scelta):

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

Nella scheda **passaggi successivi** sono disponibili alcune cartelle di lavoro utili, query di esempio e modelli di regole di analisi che sono stati inclusi. È possibile eseguirli in loco o modificarli e salvarli.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come integrare Microsoft 365 Defender Incidents e i dati degli eventi di caccia avanzati da Microsoft Defender for endpoint, in Azure Sentinel, usando il connettore Microsoft 365 Defender. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](./tutorial-detect-threats-built-in.md).
