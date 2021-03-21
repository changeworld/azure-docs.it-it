---
title: Convalida degli avvisi nel centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come verificare che gli avvisi di sicurezza siano configurati correttamente nel centro sicurezza di Azure
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: b90ca39d7bf01b84400464240bb581a5e7bc922a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602384"
---
# <a name="alert-validation-in-azure-security-center"></a>Convalida degli avvisi nel centro sicurezza di Azure
Questo documento illustra come verificare che il sistema sia configurato correttamente per gli avvisi del Centro sicurezza di Azure.

## <a name="what-are-security-alerts"></a>Informazioni sugli avvisi di sicurezza
Gli avvisi sono le notifiche generate dal Centro sicurezza quando rileva minacce per le risorse. Assegna priorità ed elenca gli avvisi insieme alle informazioni necessarie per analizzare rapidamente il problema. Il Centro sicurezza offre anche raccomandazioni per la risoluzione di un attacco.
Per altre informazioni, vedere [avvisi di sicurezza nel centro sicurezza](security-center-alerts-overview.md) e [gestione e risposta agli avvisi di sicurezza](security-center-managing-and-responding-alerts.md)


## <a name="generate-sample-azure-defender-alerts"></a>Generare avvisi di Azure Defender di esempio

Se si usa la nuova esperienza di anteprima degli avvisi come descritto in [gestire e rispondere agli avvisi di sicurezza nel centro sicurezza di Azure](security-center-managing-and-responding-alerts.md), è possibile creare avvisi di esempio in pochi clic dalla pagina degli avvisi di sicurezza nella portale di Azure.

Usare gli avvisi di esempio per:

- valutare il valore e le funzionalità di Azure Defender
- convalidare le configurazioni effettuate per gli avvisi di sicurezza, ad esempio le integrazioni SIEM, l'automazione del flusso di lavoro e le notifiche tramite posta elettronica.

Per creare avvisi di esempio:

1. Un utente con ruolo amministratore della **sicurezza** o **collaboratore della sottoscrizione**, sulla barra degli strumenti della pagina avvisi selezionare **Crea avvisi di esempio**.
1. Selezionare la sottoscrizione.
1. Selezionare il piano/i pertinente di Azure Defender per il quale si desidera visualizzare gli avvisi. 
1. Selezionare **Crea avvisi di esempio**.

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="Procedura per creare avvisi di esempio nel centro sicurezza di Azure":::
    
    Viene visualizzata una notifica che informa che è in corso la creazione degli avvisi di esempio:

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="Notifica che vengono generati gli avvisi di esempio.":::

    Dopo alcuni minuti, gli avvisi vengono visualizzati nella pagina avvisi di sicurezza. Verranno visualizzati anche in qualsiasi altra posizione configurata per ricevere gli avvisi di sicurezza del Centro sicurezza di Azure (connessione SIEMs, notifiche tramite posta elettronica e così via).

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="Avvisi di esempio nell'elenco degli avvisi di sicurezza":::

    > [!TIP]
    > Gli avvisi sono per le risorse simulate.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Simulare gli avvisi nelle VM di Azure (Windows) <a name="validate-windows"></a>

Dopo aver installato l'agente del Centro sicurezza nel computer, attenersi alla seguente procedura dal computer in cui si vuole essere la risorsa attaccata dell'avviso:

1. Copiare un eseguibile (ad esempio **calc.exe**) nel desktop del computer o in un'altra directory di praticità e rinominarlo come **ASC_AlertTest_662jfi039N.exe**.
1. Aprire il prompt dei comandi ed eseguire il file con un argomento (solo un nome di argomento fittizio), ad esempio: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Attendere da 5 a 10 minuti e aprire gli avvisi del Centro sicurezza. Verrà visualizzato un avviso.

> [!NOTE]
> Quando si esamina questo avviso di test per Windows, verificare che gli argomenti di campo **controllo abilitato** sia **true**. Se è **false**, è necessario abilitare il controllo degli argomenti della riga di comando. Per abilitarlo, usare il comando seguente: 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Simulare gli avvisi nelle VM di Azure (Linux) <a name="validate-linux"></a>

Dopo aver installato l'agente del Centro sicurezza nel computer, attenersi alla seguente procedura dal computer in cui si vuole essere la risorsa attaccata dell'avviso:
1. Copiare un eseguibile in un percorso appropriato e rinominarlo in **./asc_alerttest_662jfi039n**, ad esempio:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Aprire il prompt dei comandi ed eseguire il file seguente:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Attendere da 5 a 10 minuti e aprire gli avvisi del Centro sicurezza. Verrà visualizzato un avviso.


## <a name="simulate-alerts-on-kubernetes"></a>Simulare gli avvisi in Kubernetes <a name="validate-kubernetes"></a>

Se il servizio Azure Kubernetes è stato integrato con il Centro sicurezza, è possibile verificare che gli avvisi funzionino con il comando kubectl seguente:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Per altre informazioni su come difendere i cluster e i nodi Kubernetes, vedere [Introduzione ad Azure Defender per Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha presentato il processo di convalida degli avvisi. Dopo aver acquisito familiarità con tale convalida, vedere gli articoli seguenti:

* [Convalidare il rilevamento delle minacce di Azure Key Vault nel Centro sicurezza di Azure](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Gestione e risposta agli avvisi di sicurezza nel centro sicurezza di Azure](security-center-managing-and-responding-alerts.md) : informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti della sicurezza nel centro sicurezza.
* Informazioni sugli [avvisi di sicurezza nel centro sicurezza di Azure](./security-center-alerts-overview.md) : informazioni sui diversi tipi di avvisi di sicurezza.