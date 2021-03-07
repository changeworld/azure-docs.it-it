---
title: Correggere le raccomandazioni nel centro sicurezza di Azure | Microsoft Docs
description: Questo articolo illustra come rispondere alle raccomandazioni del Centro sicurezza di Azure per proteggere le risorse e soddisfare i criteri di sicurezza.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: f382646c889d004738064cae2d09fd66d897b110
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438268"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Correzione delle raccomandazioni nel Centro sicurezza di Azure

Le raccomandazioni forniscono suggerimenti su come proteggere meglio le risorse. Per implementare una raccomandazione, seguire la procedura di correzione fornita nella raccomandazione.

## <a name="remediation-steps"></a>Procedura di correzione <a name="remediation-steps"></a>

Dopo aver esaminato tutte le raccomandazioni, decidere quale risolvere prima di tutto. Si consiglia di assegnare priorità ai controlli di sicurezza con il massimo potenziale per aumentare il Punteggio sicuro.

1. Selezionare un suggerimento dall'elenco.

1. Seguire le istruzioni riportate nella sezione relativa alla **procedura di correzione** . Ogni raccomandazione dispone di un proprio set di istruzioni. Nella schermata seguente vengono illustrati i passaggi correttivi per la configurazione delle applicazioni in modo da consentire solo il traffico su HTTPS.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="Procedura di correzione manuale per una raccomandazione" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. Al termine, viene visualizzata una notifica che informa se il problema è stato risolto.

## <a name="quick-fix-remediation"></a>Correzione rapida

Per semplificare la correzione e migliorare la sicurezza dell'ambiente (e aumentare il Punteggio sicuro), molte raccomandazioni includono un'opzione di correzione rapida.

La correzione rapida consente di correggere rapidamente una raccomandazione su più risorse.

> [!TIP]
> Le soluzioni per la correzione rapida sono disponibili solo per raccomandazioni specifiche. Per trovare le raccomandazioni con una correzione rapida disponibile, usare il filtro **azioni di risposta** per l'elenco di raccomandazioni:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="Usare i filtri sopra l'elenco di indicazioni per trovare raccomandazioni con l'opzione correzione rapida":::

Per implementare una soluzione di correzione rapida:

1. Dall'elenco di raccomandazioni con la **correzione rapida.** Label selezionare un suggerimento.

    [![Selezionare correzione rapida.](media/security-center-remediate-recommendations/security-center-quick-fix-select.png)](media/security-center-remediate-recommendations/security-center-quick-fix-select.png#lightbox)

1. Dalla scheda **risorse non integre** selezionare le risorse su cui si desidera implementare l'indicazione e selezionare **Correggi.**

    > [!NOTE]
    > Alcune delle risorse elencate potrebbero essere disabilitate, perché non si dispone delle autorizzazioni appropriate per modificarle.

1. Nella casella Conferma leggere i dettagli e le implicazioni per la correzione.

    ![Correzione rapida](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > Le implicazioni sono elencate nella casella grigio della finestra Correggi **risorse** che viene visualizzata dopo aver **fatto clic su** Correggi. Elencano le modifiche che si verificano quando si procede con la correzione rapida.

1. Inserire i parametri rilevanti, se necessario, e approvare la correzione.

    > [!NOTE]
    > Potrebbero essere necessari alcuni minuti dopo il completamento della correzione per visualizzare le risorse nella scheda **risorse integre** . Per visualizzare le azioni correttive, controllare il [log attività](#activity-log).

1. Al termine, viene visualizzata una notifica che informa se la correzione ha avuto esito positivo.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Registrazione correzione rapida nel log attività <a name="activity-log"></a>

Per applicare la configurazione nella risorsa, l'operazione di monitoraggio e aggiornamento usa una chiamata API per la distribuzione di modelli o una PATCH REST. Queste operazioni vengono registrate nel [log attività di Azure](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato illustrato come correggere le raccomandazioni nel centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere le pagine seguenti:

* [Impostazione dei criteri di sicurezza nel centro sicurezza di Azure](tutorial-security-policy.md) : informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure
* [Che cosa sono i criteri di sicurezza, le iniziative e le raccomandazioni?](security-policy-concept.md)