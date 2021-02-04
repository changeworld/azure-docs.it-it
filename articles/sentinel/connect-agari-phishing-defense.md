---
title: Connetti le tue soluzioni di difesa del phishing e di protezione del marchio di Agari ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare il connettore Agari phishing Defense e la protezione del marchio per eseguire il pull dei log in Sentinel di Azure. Visualizzare i dati di Agari nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
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
ms.openlocfilehash: ba37b2280ba4d7138f4ed652b7b330bcaf7b9935
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566876"
---
# <a name="connect-your-agari-phishing-defense-and-brand-protection-solutions-to-azure-sentinel"></a>Connetti le tue soluzioni di difesa del phishing e di protezione del marchio di Agari ad Azure Sentinel

> [!IMPORTANT]
> Il connettore Agari phishing Defense e la protezione del marchio sono attualmente in **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Il connettore Agari phishing Defense and Brand Protection consente di connettere facilmente i log delle soluzioni di protezione del marchio e di protezione da phishing ad Azure Sentinel, in modo da poter visualizzare i dati nelle cartelle di lavoro, eseguire query per creare avvisi personalizzati e incorporarli per migliorare l'analisi. Le soluzioni di Agari si integrano con Azure Sentinel con funzioni di Azure e l'API REST.

Inoltre, i clienti con la protezione del marchio e la risposta al phishing possono sfruttare i vantaggi della condivisione di intelligence per le minacce tramite il API Graph di sicurezza.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

Di seguito sono riportate le informazioni necessarie per connettere le soluzioni di difesa del phishing e di protezione del marchio di Agari ad Azure Sentinel

- Autorizzazioni di lettura e scrittura per l'area di lavoro di Azure Sentinel.

- Autorizzazioni di lettura per le chiavi condivise per l'area di lavoro. [Altre informazioni sulle chiavi dell'area di lavoro](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

- Autorizzazioni di lettura e scrittura per le funzioni di Azure per creare un app per le funzioni. [Altre informazioni su funzioni di Azure](../azure-functions/index.yml).

- Assicurarsi di avere l' **ID client** e le **chiavi segrete** di Agari (identico in tutte le soluzioni Agari). Per istruzioni, vedere il [sito degli sviluppatori di Agari](https://developers.agari.com/agari-platform/docs/quick-start) .

## <a name="configure-and-connect-agari-solutions"></a>Configurare e connettere soluzioni di Agari 

Le soluzioni di Agari possono integrare ed esportare i log direttamente in Sentinel di Azure usando un app per le funzioni di Azure.

> [!NOTE]
> Questo connettore usa funzioni di Azure per connettersi alle soluzioni di Agari per eseguire il pull dei log in Sentinel di Azure. Questo può comportare costi di inserimento dati aggiuntivi. Per informazioni dettagliate, vedere la pagina dei [prezzi di funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/) .

1. **Raccogliere le credenziali dell'API di Agari:** 

    Assicurarsi di avere l' **ID client** e le **chiavi segrete** di Agari. Le istruzioni sono disponibili nel [sito degli sviluppatori di Agari](https://developers.agari.com/agari-platform/docs/quick-start#generate-api-credentials).

1. **Opzionale Abilitare il API Graph di sicurezza:** 

    Il app per le funzioni Agari consente di condividere l'Intelligence per le minacce con Sentinel tramite il API Graph di sicurezza. Per usare questa funzionalità, è necessario abilitare il [connettore Sentinel Threat Intelligence Platforms](connect-threat-intelligence.md) e [registrare un'applicazione](/graph/auth-register-app-v2) in Azure Active Directory.

    Questo processo fornirà tre informazioni da usare quando si distribuisce il app per le funzioni seguente: l' **ID del tenant del grafo**, l' **ID client** del grafo e il **segreto client** del grafo.

1. **Distribuire il connettore e il app per le funzioni di Azure associato:** 

    1. Nel portale di Azure Sentinel selezionare **connettori dati**. Selezionare **Agari phishing Defense and Brand Protection (anteprima)** e quindi **aprire la pagina del connettore**.

    1. In **configurazione** copiare l'ID e la **chiave primaria** dell' **area di lavoro** di Azure Sentinel e incollarli a parte.

    1. Selezionare **Distribuisci in Azure**. (Potrebbe essere necessario scorrere verso il basso per trovare il pulsante).

    1. Verrà visualizzata la schermata **distribuzione personalizzata** .

        - Immettere l' **ID client** e il **segreto client** di Agari (chiavi segrete)

        - Immettere l'ID dell' **area** di lavoro di Azure Sentinel e la **chiave dell'area di lavoro** (chiave primaria) copiati e accantonati.

        - Selezionare **true** o **false** per le soluzioni Agari per le quali sono presenti sottoscrizioni attive.

        - Se è stato creato un applicazione Azure per condividere IOC con Azure Sentinel usando il API Graph di sicurezza, selezionare **true** per **abilitare la condivisione dei grafici di sicurezza** e immettere l'ID del tenant del **grafo**, l' **ID client** del grafo e il **segreto client del grafo**.

    1. Selezionare **Rivedi e crea**. Al termine della convalida, fare clic su **Crea**.

1. **Assegnare le autorizzazioni necessarie per il app per le funzioni:**

    Il connettore Agari usa una variabile di ambiente per archiviare i timestamp di accesso ai log. Per consentire all'applicazione di scrivere in questa variabile, è necessario assegnare le autorizzazioni all'identità assegnata dal sistema.

    1. Nella portale di Azure passare a **app per le funzioni**.

    1. Nel pannello **app per le funzioni** selezionare l'app per le funzioni dall'elenco, quindi selezionare **identità** in **Impostazioni** nel menu di navigazione del app per le funzioni.

    1. Nella scheda **sistema assegnato** impostare lo **stato** **su on**. 

    1. Selezionare **Save (Salva**). verrà visualizzato un pulsante **assegnazioni di ruolo di Azure** . quindi farvi clic.

    1. Nella schermata **assegnazioni di ruolo di Azure** selezionare **Aggiungi assegnazione ruolo**. Impostare **ambito** su **sottoscrizione**, selezionare la sottoscrizione dall'elenco a discesa **sottoscrizione** e impostare **ruolo** su dati di **configurazione app proprietario**. 

    1. Selezionare **Salva**.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log** in *CustomLogs*, nelle tabelle seguenti: 

- `agari_apdtc_log_CL`
- `agari_apdpolicy_log_CL`
- `agari_bpalerts_log_CL`

Per eseguire query sui dati delle soluzioni di Agari, immettere uno dei nomi di tabella sopra indicati nella finestra query.

Per alcune query di esempio utili, vedere la scheda **passaggi successivi** nella pagina del connettore.

## <a name="validate-connectivity"></a>Convalidare la connettività

Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics. 

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere Agari phishing Defense e soluzioni di protezione del marchio a Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
