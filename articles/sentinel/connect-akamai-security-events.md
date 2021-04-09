---
title: Connettere l'agente di raccolta degli eventi di sicurezza di Akamai ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore Akamai Security Events per eseguire il pull dei log di sicurezza delle soluzioni Akamai in Sentinel di Azure. Visualizzare i dati Akamai nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 8aa5a52a06713b4f00b43205a57148049a8ef8da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711961"
---
# <a name="connect-your-akamai-security-events-collector-to-azure-sentinel"></a>Connettere l'agente di raccolta degli eventi di sicurezza di Akamai ad Azure Sentinel

> [!IMPORTANT]
> Il connettore per gli eventi di sicurezza di Akamai è attualmente in versione di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere l'agente di raccolta degli eventi di sicurezza di Akamai ad Azure Sentinel. Il connettore dati degli eventi di sicurezza di Akamai consente di connettere facilmente i log Akamai con Azure Sentinel, in modo che sia possibile visualizzare i dati nelle cartelle di lavoro, eseguire query per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra l'agente di raccolta degli eventi di sicurezza di Akamai e Azure Sentinel usa syslog con formato CEF, un server d'utilità di log basato su Linux e l'agente di Log Analytics. USA anche un parser di log personalizzato basato su una funzione kusto.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre delle autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- È necessario disporre delle autorizzazioni di lettura per le chiavi condivise per l'area di lavoro. [Altre informazioni sulle chiavi dell'area di lavoro](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-akamai-security-events-logs-to-azure-sentinel"></a>Inviare i log degli eventi di sicurezza di Akamai ad Azure Sentinel

Per ottenere i log in Azure Sentinel, configurare l'agente di raccolta degli eventi di sicurezza di Akamai per l'invio di messaggi syslog in formato CEF a un server di inoltrato di log basato su Linux (che esegue rsyslog o syslog-ng). In questo server verrà installato l'agente Log Analytics e l'agente li inoltrerà all'area di lavoro di Azure Sentinel.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare **eventi di sicurezza Akamai (anteprima)**, quindi aprire la pagina del **connettore**.

1. Seguire le istruzioni riportate nella scheda **istruzioni** in **configurazione**:

    1. Inferiore a **1. Configurazione dell'agente syslog di Linux** : eseguire questo passaggio se non si dispone già di un server d'esecuzione del log o se ne è necessario un altro. Vedere [Step 1: deploy the log](connect-cef-agent.md) retailer nella documentazione di Azure Sentinel per informazioni sul dimensionamento, istruzioni più dettagliate e una spiegazione approfondita.

    1. Inferiore a **2. Eseguire l'avanzamento dei log CEF (Common Event Format) nell'agente syslog** . seguire le istruzioni di Akamai per [configurare l'integrazione Siem](https://developer.akamai.com/tools/integrations/siem) e configurare [un connettore CEF](https://developer.akamai.com/tools/integrations/siem/siem-cef-connector). Questo connettore riceve gli eventi di sicurezza dalle soluzioni Akamai in tempo quasi reale usando l'API SIEM OPEN e li converte da JSON in formato CEF.
    
        Questa configurazione deve includere gli elementi seguenti:
    
        - Destinazione log: il nome host e/o l'indirizzo IP del server di accesso di log
        - Protocollo e porta-TCP 514 (se consigliato altrimenti, assicurarsi di apportare la modifica parallela nel daemon syslog nel server di inoltri di log)
        - Formato log-CEF
        - Tipi di log: tutti disponibili

    1. In **3. Convalidare la connessione** : verificare l'inserimento dei dati copiando il comando nella pagina del connettore ed eseguendolo sul server d'accesso. Vedere [Step 3: Validate Connectivity](connect-cef-verify.md) nella documentazione di Azure Sentinel per istruzioni e spiegazioni più dettagliate.

        Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, nella sezione **Azure Sentinel** , nella tabella *CommonSecurityLog* .

Questo connettore dati dipende da un parser basato su una funzione kusto per funzionare come previsto. Usare la procedura seguente per configurare la funzione kusto di **AkamaiSIEMEvent** da usare nelle query e nelle cartelle di lavoro di.

1. Nel menu di navigazione di Azure Sentinel selezionare **logs**.

1. Copiare la query seguente e incollarla nella finestra query.
    ```kusto
    CommonSecurityLog 
    | where DeviceVendor == 'Akamai'
    | where DeviceProduct == 'akamai_siem'
    | extend EventVendor = 'Akamai'
    | extend EventProduct = 'akamai_siem'
    | extend EventProductVersion = '1.0'
    | extend EventId = DeviceEventClassID
    | extend EventCategory = Activity
    | extend EventSeverity = LogSeverity
    | extend DvcAction = DeviceAction
    | extend NetworkApplicationProtocol = ApplicationProtocol
    | extend Ipv6Src = DeviceCustomIPv6Address2
    | extend RuleName = DeviceCustomString1
    | extend RuleMessages = DeviceCustomString2
    | extend RuleData = DeviceCustomString3
    | extend RuleSelectors = DeviceCustomString4
    | extend ClientReputation = DeviceCustomString5
    | extend ApiId = DeviceCustomString6
    | extend RequestId = DevicePayloadId
    | extend DstDvcHostname = DestinationHostName
    | extend DstPortNumber = DestinationPort
    | extend ConfigId = FlexString1
    | extend PolicyId = FlexString2
    | extend NetworkBytes = SentBytes
    | extend UrlOriginal = RequestURL
    | extend HttpRequestMethod = RequestMethod
    | extend SrcIpAddr = SourceIP
    | extend EventStartTime = datetime(1970-01-01) + tolong(extract(@'.*start=(.*?);', 1, AdditionalExtensions)) * 1s 
    | extend SlowPostAction = extract(@'.*AkamaiSiemSlowPostAction=(.*?);', 1, AdditionalExtensions)
    | extend SlowPostRate = extract(@'.*AkamaiSiemSlowPostRate=(.*?);', 1, AdditionalExtensions)
    | extend RuleVersions = extract(@'.*AkamaiSiemRuleVersions=,?(.*?);', 1, AdditionalExtensions)
    | extend RuleTags = extract(@'.*AkamaiSiemRuleTags=(.*?);', 1, AdditionalExtensions)
    | extend ApiKey = extract(@'.*AkamaiSiemApiKey=(.*?);', 1, AdditionalExtensions)
    | extend Tls = extract(@'.*AkamaiSiemTLSVersion=(.*?);', 1, AdditionalExtensions)
    | extend RequestHeaders = extract(@'.*AkamaiSiemRequestHeaders=;?(.*?);', 1, AdditionalExtensions)
    | extend ResponseHeaders = extract(@'.*AkamaiSiemResponseHeaders=(.*?);', 1, AdditionalExtensions)
    | extend HttpStatusCode = extract(@'.*AkamaiSiemResponseStatus=(.*?);', 1, AdditionalExtensions)
    | extend GeoContinent = extract(@'.*AkamaiSiemContinent=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCountry = extract(@'.*AkamaiSiemCountry=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoCity = extract(@'.*AkamaiSiemCity=(.*?);', 1, AdditionalExtensions)
    | extend SrcGeoRegion = extract(@'.*AkamaiSiemRegion=(.*?);', 1, AdditionalExtensions)
    | extend GeoAsn = extract(@'.*AkamaiSiemASN=(\d+)', 1, AdditionalExtensions)
    | extend Custom = extract(@'.*AkamaiSiemCusomData=(.*?)', 1, AdditionalExtensions)
    | project TimeGenerated
            , EventVendor
            , EventProduct
            , EventProductVersion
            , EventStartTime
            , EventId
            , EventCategory
            , EventSeverity
            , DvcAction
            , NetworkApplicationProtocol
            , Ipv6Src
            , RuleName
            , RuleMessages
            , RuleData
            , RuleSelectors
            , ClientReputation
            , ApiId
            , RequestId
            , DstDvcHostname
            , DstPortNumber
            , ConfigId
            , PolicyId
            , NetworkBytes
            , UrlOriginal
            , HttpRequestMethod
            , SrcIpAddr
            , SlowPostAction
            , SlowPostRate
            , RuleVersions
            , RuleTags
            , ApiKey
            , Tls
            , RequestHeaders
            , ResponseHeaders
            , HttpStatusCode
            , GeoContinent
            , SrcGeoCountry
            , SrcGeoCity
            , SrcGeoRegion
            , GeoAsn
            , Custom
    ```

1. Fare clic sull'elenco a discesa **Salva** , quindi fare clic su **Salva**. Nel pannello **Salva** ,

    1. In **nome** immettere **AkamaiSIEMEvent**.

    1. In **Salva con nome** scegliere **funzione**.

    1. In **alias funzione** immettere **AkamaiSIEMEvent**.

    1. In **Category** immettere **Functions**.

    1. Fare clic su **Salva**.

    Per l'attivazione delle app per le funzioni sono in genere necessari da 10 a 15 minuti.

A questo punto si è pronti per eseguire query sui dati di Akamai, immettendo `AkamaiSIEMEvent` nella prima riga della finestra query.

Per altri esempi di query, vedere la scheda **passaggi successivi** nella pagina del connettore.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere gli eventi di sicurezza di Akamai ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.