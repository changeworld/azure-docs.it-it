---
title: Verifica del Punteggio sicuro nel centro sicurezza di Azure
description: Informazioni sui diversi modi per accedere e tenere traccia del Punteggio sicuro nel centro sicurezza di Azure.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 5efc48d348e9cfceab590bcfba8c621e7721376f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107698"
---
# <a name="access-and-track-your-secure-score"></a>Accedere e tenere traccia del Punteggio sicuro

È possibile trovare il Punteggio sicuro complessivo, nonché il punteggio per ogni sottoscrizione, tramite il portale di Azure o a livello, come descritto nelle sezioni seguenti:

> [!TIP]
> Per una spiegazione dettagliata del modo in cui vengono calcolati i punteggi, vedere [calcoli-comprensione del Punteggio](secure-score-security-controls.md#calculations---understanding-your-score).

## <a name="get-your-secure-score-from-the-portal"></a>Ottenere il Punteggio sicuro dal portale

Il Centro sicurezza Visualizza il punteggio in primo piano nel portale: è il primo riquadro principale della pagina Panoramica del Centro sicurezza. Selezionando questo riquadro, viene visualizzata la pagina del Punteggio sicuro dedicato, in cui verrà visualizzato il Punteggio suddiviso in base alla sottoscrizione. Selezionare una singola sottoscrizione per visualizzare l'elenco dettagliato delle raccomandazioni con priorità e il potenziale impatto che il monitoraggio e l'aggiornamento avranno sul punteggio della sottoscrizione. 

Per riepilogo, il Punteggio sicuro viene visualizzato nei percorsi seguenti nelle pagine del portale del Centro sicurezza.

- In una sezione **Panoramica** del Centro sicurezza (dashboard principale):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Punteggio sicuro nel dashboard del Centro sicurezza":::

- Nella pagina **Punteggio sicuro** dedicato è possibile visualizzare il Punteggio sicuro per la sottoscrizione e i gruppi di gestione:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Il Punteggio sicuro per le sottoscrizioni nella pagina del Punteggio sicuro del Centro sicurezza":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Il Punteggio sicuro per i gruppi di gestione nella pagina del Punteggio sicuro del Centro sicurezza":::

    > [!NOTE]
    > I gruppi di gestione per i quali non si dispone di autorizzazioni sufficienti indicheranno il punteggio come "Restricted". 

- Nella parte superiore della pagina **raccomandazioni** :

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Il Punteggio sicuro nella pagina delle raccomandazioni del Centro sicurezza":::

## <a name="get-your-secure-score-from-the-rest-api"></a>Ottenere il Punteggio sicuro dall'API REST

È possibile accedere al Punteggio tramite l'API del Punteggio sicuro. I metodi dell'API offrono la flessibilità necessaria per eseguire query nei dati e creare un meccanismo personalizzato per la creazione di report sui punteggi di sicurezza nel tempo. Ad esempio, è possibile usare l' [API dei punteggi sicuri](/rest/api/securitycenter/securescores) per ottenere il punteggio per una sottoscrizione specifica. Inoltre, è possibile usare l' [API di controllo del Punteggio sicuro](/rest/api/securitycenter/securescorecontrols) per elencare i controlli di sicurezza e il punteggio corrente delle sottoscrizioni.

![Recupero di un singolo Punteggio sicuro tramite l'API](media/secure-score-security-controls/single-secure-score-via-api.png)

Per esempi di strumenti basati sull'API per il Punteggio sicuro, vedere [l'area di valutazione sicura della community di GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Ottieni il Punteggio sicuro dal grafico delle risorse di Azure

Azure Resource Graph fornisce l'accesso immediato alle informazioni sulle risorse negli ambienti cloud con potenti funzionalità di filtro, raggruppamento e ordinamento. Si tratta di un modo rapido ed efficiente di eseguire query sulle informazioni nelle sottoscrizioni di Azure a livello di codice o dall'interno del portale di Azure. Vedere [altre informazioni su Azure Resource Graph](../governance/resource-graph/index.yml).

Per accedere al Punteggio sicuro per più sottoscrizioni con Azure Resource Graph:

1. Dal portale di Azure aprire **Esplora grafico risorse di Azure**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Avvio della pagina consigli di Azure Resource Graph Explorer * *" :::

1. Immettere la query kusto (usando gli esempi riportati di seguito per informazioni aggiuntive).

    - Questa query restituisce l'ID sottoscrizione, il punteggio corrente in punti e come percentuale e il punteggio massimo per la sottoscrizione. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Questa query restituisce lo stato di tutti i controlli di sicurezza. Per ogni controllo, si otterrà il numero di risorse non integre, il punteggio corrente e il punteggio massimo. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Selezionare **Esegui query**.


## <a name="tracking-your-secure-score-over-time"></a>Rilevamento del Punteggio sicuro nel tempo

### <a name="secure-score-over-time-report-in-workbooks-page"></a>Report di sicurezza del punteggio nel tempo nella pagina cartelle di lavoro

La pagina cartelle di lavoro del Centro sicurezza include un report pronto per il rilevamento visivo dei punteggi delle sottoscrizioni, dei controlli di sicurezza e molto altro. Per altre informazioni [, vedere creare report interattivi avanzati dei dati del Centro sicurezza](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Sezione del report relativo al Punteggio sicuro nel tempo dalla raccolta di cartelle di lavoro del Centro sicurezza di Azure":::

### <a name="power-bi-pro-dashboards"></a>Dashboard Power BI Pro

Se si è un utente Power BI con un account Pro, è possibile usare il **Punteggio sicuro nel tempo** Power BI Dashboard per tenere traccia del Punteggio sicuro nel tempo ed esaminare le modifiche.

> [!TIP]
> È possibile trovare questo dashboard, oltre ad altri strumenti per l'uso di a livello con punteggio sicuro nell'area dedicata della community del Centro sicurezza di Azure su GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

Il dashboard contiene i due report seguenti che consentono di analizzare lo stato di sicurezza:

- **Riepilogo delle risorse** : fornisce dati riepilogati sull'integrità delle risorse.
- **Riepilogo del Punteggio sicuro** : fornisce dati riepilogati relativi all'avanzamento del punteggio. Usare il grafico "Punteggio sicuro nel tempo per sottoscrizione" per visualizzare le modifiche apportate al punteggio. Se si nota un cambiamento significativo nel punteggio, controllare la tabella "modifiche rilevate che potrebbero influire sul punteggio sicuro" per le possibili modifiche che potrebbero avere causato la modifica. Questa tabella presenta le risorse eliminate, le risorse appena distribuite o le risorse per cui è stato modificato lo stato di sicurezza per una delle raccomandazioni.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="Il Punteggio sicuro facoltativo nel tempo Power BI Dashboard per tenere traccia del Punteggio sicuro nel tempo e analizzare le modifiche":::


## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive come accedere e tenere traccia del Punteggio sicuro. Per i materiali correlati, vedere gli articoli seguenti:

- [Learn about the different elements of a recommendation](security-center-recommendations.md) (Informazioni sui diversi elementi di una raccomandazione)
- [Learn how to remediate recommendations](security-center-remediate-recommendations.md) (Informazioni su come correggere le raccomandazioni)
- [Visualizza gli strumenti basati su GitHub per lavorare a livello di codice con un punteggio sicuro](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)