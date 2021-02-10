---
title: Punteggio di sicurezza nel Centro sicurezza di Azure
description: Descrizione del punteggio di sicurezza del Centro sicurezza di Azure e dei relativi controlli di sicurezza
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetd: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: memildin
ms.openlocfilehash: 24822777b06fadf87ca446d9b7ff8ba4df34adc5
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "100007678"
---
# <a name="secure-score-in-azure-security-center"></a>Punteggio di sicurezza nel Centro sicurezza di Azure

## <a name="introduction-to-secure-score"></a>Introduzione al punteggio di sicurezza

Il Centro sicurezza di Azure ha due obiettivi principali: 

- per semplificare la comprensione della situazione di sicurezza corrente
- per contribuire a migliorare in modo efficiente e efficace la sicurezza

La funzionalità centrale del Centro sicurezza che consente di raggiungere questi obiettivi è un **Punteggio sicuro**.

Il Centro sicurezza valuta continuamente risorse, sottoscrizioni e organizzazione per rilevare problemi di sicurezza. Aggrega quindi tutti i risultati in un singolo punteggio, in modo da poter indicare, a colpo d'occhio, lo stato di sicurezza attuale: maggiore è il punteggio, minore è il livello di rischio identificato.

Il Punteggio sicuro viene visualizzato nelle pagine portale di Azure come valore percentuale, ma vengono presentati chiaramente anche i valori sottostanti:

:::image type="content" source="./media/secure-score-security-controls/single-secure-score-via-ui.png" alt-text="Punteggio sicuro complessivo come illustrato nel portale":::

Per aumentare la sicurezza, vedere la pagina raccomandazioni del Centro sicurezza per le azioni in attesa necessarie per aumentare il punteggio. Ogni raccomandazione include istruzioni che consentono di correggere il problema specifico.

I consigli sono raggruppati in **controlli di sicurezza**. Ogni controllo è un gruppo logico di raccomandazioni sulla sicurezza correlate e riflette le superfici di attacco vulnerabili. Il punteggio viene migliorato solo quando si correggono *tutte* le raccomandazioni relative a una singola risorsa all'interno di un controllo. Per verificare in che modo l'organizzazione protegga ogni singola superficie di attacco, esaminare i punteggi per ogni controllo di sicurezza.

Per altre informazioni, vedere [come viene calcolato il Punteggio sicuro](secure-score-security-controls.md#how-your-secure-score-is-calculated) di seguito. 


## <a name="access-your-secure-score"></a>Accedi al tuo punteggio sicuro

È possibile trovare il Punteggio sicuro complessivo, nonché il punteggio per ogni sottoscrizione, tramite il portale di Azure o a livello, come descritto nelle sezioni seguenti:

- [Ottenere il Punteggio sicuro dal portale](#get-your-secure-score-from-the-portal)
- [Ottenere il Punteggio sicuro dall'API REST](#get-your-secure-score-from-the-rest-api)
- [Ottieni il Punteggio sicuro da Azure Resource Graph (ARG)](#get-your-secure-score-from-azure-resource-graph-arg)

### <a name="get-your-secure-score-from-the-portal"></a>Ottenere il Punteggio sicuro dal portale

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

### <a name="get-your-secure-score-from-the-rest-api"></a>Ottenere il Punteggio sicuro dall'API REST

È possibile accedere al Punteggio tramite l'API del Punteggio sicuro. I metodi dell'API offrono la flessibilità necessaria per eseguire query nei dati e creare un meccanismo personalizzato per la creazione di report sui punteggi di sicurezza nel tempo. Ad esempio, è possibile usare l' [API dei punteggi sicuri](/rest/api/securitycenter/securescores) per ottenere il punteggio per una sottoscrizione specifica. Inoltre, è possibile usare l' [API di controllo del Punteggio sicuro](/rest/api/securitycenter/securescorecontrols) per elencare i controlli di sicurezza e il punteggio corrente delle sottoscrizioni.

![Recupero di un singolo Punteggio sicuro tramite l'API](media/secure-score-security-controls/single-secure-score-via-api.png)

Per esempi di strumenti basati sull'API per il Punteggio sicuro, vedere [l'area di valutazione sicura della community di GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

### <a name="get-your-secure-score-from-azure-resource-graph-arg"></a>Ottieni il Punteggio sicuro da Azure Resource Graph (ARG)

Azure Resource Graph fornisce l'accesso immediato alle informazioni sulle risorse negli ambienti cloud con potenti funzionalità di filtro, raggruppamento e ordinamento. Si tratta di un modo rapido ed efficiente di eseguire query sulle informazioni nelle sottoscrizioni di Azure a livello di codice o dall'interno del portale di Azure. Vedere [altre informazioni su Azure Resource Graph](../governance/resource-graph/index.yml).

Per accedere al Punteggio sicuro per più sottoscrizioni con ARG:

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

Se si è un utente Power BI con un account Pro, è possibile usare il **Punteggio sicuro nel tempo** Power BI Dashboard per tenere traccia del Punteggio sicuro nel tempo ed esaminare le modifiche.

> [!TIP]
> È possibile trovare questo dashboard, oltre ad altri strumenti per l'uso di a livello con punteggio sicuro nell'area dedicata della community del Centro sicurezza di Azure su GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

Il dashboard contiene i due report seguenti che consentono di analizzare lo stato di sicurezza:

- **Riepilogo delle risorse** : fornisce dati riepilogati sull'integrità delle risorse.
- **Riepilogo del Punteggio sicuro** : fornisce dati riepilogati relativi all'avanzamento del punteggio. Usare il grafico "Punteggio sicuro nel tempo per sottoscrizione" per visualizzare le modifiche apportate al punteggio. Se si nota un cambiamento significativo nel punteggio, controllare la tabella "modifiche rilevate che potrebbero influire sul punteggio sicuro" per le possibili modifiche che potrebbero avere causato la modifica. Questa tabella presenta le risorse eliminate, le risorse appena distribuite o le risorse per cui è stato modificato lo stato di sicurezza per una delle raccomandazioni.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="Il Punteggio sicuro facoltativo nel tempo Power BI Dashboard per tenere traccia del Punteggio sicuro nel tempo e analizzare le modifiche":::





## <a name="how-your-secure-score-is-calculated"></a>Calcolo del Punteggio sicuro 

Il contributo di ogni controllo di sicurezza rispetto al punteggio di sicurezza complessivo viene visualizzato chiaramente nella pagina Consigli.

[![Il punteggio di sicurezza migliorato introduce i controlli di sicurezza](media/secure-score-security-controls/security-controls.png)](media/secure-score-security-controls/security-controls.png#lightbox)

Per ottenere tutti i punti possibili per un controllo di sicurezza, tutte le risorse devono essere conformi a tutte le raccomandazioni sulla sicurezza all'interno del controllo di sicurezza. Ad esempio, il Centro sicurezza offre più raccomandazioni su come proteggere le porte di gestione. È necessario risolverli tutti per distinguere il Punteggio sicuro.

Ad esempio, il controllo di sicurezza denominato "Applica gli aggiornamenti del sistema" ha un punteggio massimo di sei punti, che è possibile visualizzare nella descrizione comando sul valore di incremento potenziale del controllo:

[![Controllo di sicurezza "Applica gli aggiornamenti del sistema"](media/secure-score-security-controls/apply-system-updates-control.png)](media/secure-score-security-controls/apply-system-updates-control.png#lightbox)

Il punteggio massimo per il controllo Applica gli aggiornamenti del sistema, è sempre 6. In questo esempio sono disponibili 50 risorse. Dividendo quindi il punteggio massimo per 50, il risultato è che ogni risorsa contribuisce con 0,12 punti. 

* **Incremento potenziale** (0,12 x 8 risorse non integre = 0,96): punti rimanenti disponibili nel controllo. Se si aggiornano tutte le raccomandazioni in questo controllo, il punteggio aumenterà del 2% (in questo caso, 0,96 punti arrotondati per eccesso a 1 punto). 
* **Punteggio corrente** (0,12 x 42 risorse integre = 5,04): punteggio corrente per il controllo. Ogni controllo contribuisce a raggiungere il punteggio totale. In questo esempio, il controllo contribuisce con 5,04 punti al punteggio di sicurezza totale corrente.
* **Punteggio massimo**: numero massimo di punti che è possibile ottenere completando tutte le raccomandazioni in un controllo. Il punteggio massimo per un controllo indica il significato relativo del controllo. Usare i valori del punteggio massimo per valutare i problemi da risolvere per primi. 


### <a name="calculations---understanding-your-score"></a>Calcoli: comprensione del punteggio

|Metrica|Formula ed esempio|
|-|-|
|**Punteggio corrente del controllo di sicurezza**|<br>![Equazione per il calcolo del Punteggio di un controllo di sicurezza](media/secure-score-security-controls/secure-score-equation-single-control.png)<br><br>Ogni singolo controllo di sicurezza contribuisce a raggiungere il punteggio di sicurezza. Ogni risorsa interessata da una raccomandazione all'interno del controllo contribuisce al punteggio corrente del controllo. Il punteggio corrente per ogni controllo è una misura dello stato delle risorse *all'interno* del controllo.<br>![Descrizioni comando che mostrano i valori usati durante il calcolo del punteggio corrente del controllo di sicurezza](media/secure-score-security-controls/security-control-scoring-tooltips.png)<br>In questo esempio, il punteggio massimo di 6 viene diviso per 78 perché questa è la somma delle risorse integre e non integre.<br>6 / 78 = 0,0769<br>Moltiplicando questo valore per il numero di risorse integre (4) si ottiene il punteggio corrente:<br>0,0769 * 4 = **0,31**<br><br>|
|**Punteggio di sicurezza**<br>Singola sottoscrizione|<br>![Equazione per il calcolo del Punteggio sicuro di una sottoscrizione](media/secure-score-security-controls/secure-score-equation-single-sub.png)<br><br>![Punteggio di sicurezza di una singola sottoscrizione con tutti i controlli abilitati](media/secure-score-security-controls/secure-score-example-single-sub.png)<br>In questo esempio, è presente una singola sottoscrizione con tutti i controlli di sicurezza disponibili (punteggio massimo potenziale di 60 punti). Il punteggio mostra 28 punti su 60 possibili e i restanti 32 punti vengono riflessi nei valori di "Incremento potenziale del punteggio" dei controlli di sicurezza.<br>![Elenco di controlli e incremento potenziale del punteggio](media/secure-score-security-controls/secure-score-example-single-sub-recs.png)|
|**Punteggio di sicurezza**<br>Più sottoscrizioni|<br>![Equazione per il calcolo del Punteggio sicuro per più sottoscrizioni](media/secure-score-security-controls/secure-score-equation-multiple-subs.png)<br><br>Quando si calcola il punteggio combinato per più sottoscrizioni, il Centro sicurezza include un *peso* per ogni sottoscrizione. I pesi relativi per le sottoscrizioni sono determinati dal centro sicurezza in base a fattori quali il numero di risorse.<br>Il punteggio corrente per ogni sottoscrizione viene calcolato in modo analogo a quello di una singola sottoscrizione, ma il peso viene applicato come illustrato nell'equazione.<br>Quando si visualizzano più sottoscrizioni, il punteggio di sicurezza valuta tutte le risorse all'interno dei criteri abilitati e raggruppa il loro effetto combinato sul punteggio massimo di ogni controllo di sicurezza.<br>![Punteggio di sicurezza di più sottoscrizioni con tutti i controlli abilitati](media/secure-score-security-controls/secure-score-example-multiple-subs.png)<br>Il punteggio combinato **non** è una media. Si tratta, piuttosto, della posizione valutata dello stato di tutte le risorse in tutte le sottoscrizioni.<br>Anche in questo caso, se si passa alla pagina Consigli e si aggiungono i punti potenziali disponibili, si noterà che si tratta della differenza tra il punteggio corrente (24) e il punteggio massimo disponibile (60).|
||||

### <a name="which-recommendations-are-included-in-the-secure-score-calculations"></a>Quali raccomandazioni sono incluse nei calcoli di valutazione sicura?

Solo le raccomandazioni predefinite hanno effetto sul punteggio di sicurezza.

Le raccomandazioni contrassegnate come **Anteprima** non sono incluse nei calcoli del Punteggio sicuro. È comunque necessario correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima.

Esempio di una raccomandazione in anteprima:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Raccomandazione con il flag di anteprima":::

## <a name="improve-your-secure-score"></a>Migliorare il punteggio di sicurezza

Per migliorare il punteggio di sicurezza, correggere le raccomandazioni sulla sicurezza presenti nell'elenco delle raccomandazioni. È possibile correggere ogni raccomandazione manualmente per ogni risorsa o usando l' opzione **Correzione rapida** (se disponibile) per applicare rapidamente una correzione per una raccomandazione a un gruppo di risorse. Per altre informazioni, vedere [Correggere le raccomandazioni](security-center-remediate-recommendations.md).

Un altro modo per migliorare il punteggio e assicurarsi che gli utenti non creino risorse che influiscono negativamente sul punteggio consiste nel configurare le opzioni Applica e nega sulle raccomandazioni pertinenti. Per altre informazioni, vedere [Impedire gli errori di configurazione con le raccomandazioni di tipo Imponi/Nega](prevent-misconfigurations.md).

## <a name="security-controls-and-their-recommendations"></a>Controlli di sicurezza e relative raccomandazioni

La tabella seguente elenca i controlli di sicurezza nel Centro sicurezza di Azure. Per ogni controllo è possibile visualizzare il numero massimo di punti da aggiungere al punteggio di sicurezza, se si correggono *tutte* le raccomandazioni elencate nel controllo per *tutte* le risorse. 

Il set di raccomandazioni sulla sicurezza fornito con il Centro sicurezza è adattato alle risorse disponibili nell'ambiente di ogni organizzazione. Le raccomandazioni possono essere ulteriormente personalizzate [disabilitando i criteri](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations) ed [escludendo le risorse specifiche da un'indicazione](exempt-resource.md). 
 
È consigliabile esaminare con attenzione le iniziative di criteri di Azure assegnate a ogni organizzazione. 

> [!TIP]
> Per informazioni dettagliate sulla revisione e la modifica delle iniziative, vedere [utilizzo dei criteri di sicurezza](tutorial-security-policy.md). 

Anche se l'iniziativa di sicurezza predefinita del Centro sicurezza è basata su standard e procedure consigliate per il settore, esistono scenari in cui i consigli predefiniti elencati di seguito potrebbero non essere completamente idonei per l'organizzazione. Di conseguenza, talvolta è necessario modificare l'iniziativa predefinita, senza compromettere la sicurezza, per garantire che sia allineata ai criteri dell'organizzazione. standard del settore, standard normativi e benchmark che è necessario soddisfare.<br><br>
<div class="foo">

<style type="text/css"> .tg  {border-collapse:collapse;border-spacing:0;} .tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px; overflow:hidden;padding:10px 5px;word-break:normal;} .tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:18px; font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;} .tg .tg-cly1{text-align:left;vertical-align:middle} .tg .tg-lboi{border-color:inherit;text-align:left;vertical-align:middle} </style>

[!INCLUDE [security-center-controls-and-recommendations](../../includes/asc/security-control-recommendations.md)]

</div>




## <a name="secure-score-faq"></a>Domande frequenti sul punteggio di sicurezza

### <a name="if-i-address-only-three-out-of-four-recommendations-in-a-security-control-will-my-secure-score-change"></a>Se si correggono solo tre su quattro raccomandazioni in un controllo di sicurezza, il punteggio di sicurezza cambierà?
No. Non verrà modificato fino a quando non si correggeranno tutte le raccomandazioni relative una singola risorsa. Per ottenere il punteggio massimo per un controllo, è necessario correggere tutte le raccomandazioni relative a tutte le risorse.

### <a name="if-a-recommendation-isnt-applicable-to-me-and-i-disable-it-in-the-policy-will-my-security-control-be-fulfilled-and-my-secure-score-updated"></a>Se una raccomandazione non è applicabile all'utente e viene disabilitata nei criteri, il controllo di sicurezza sarà soddisfatto e il punteggio di sicurezza sarà aggiornato?
Sì. Si consiglia di disabilitare le raccomandazioni quando non sono applicabili all'ambiente in uso. Per istruzioni su come disabilitare una raccomandazione specifica, vedere [Disabilitare i criteri di sicurezza](./tutorial-security-policy.md#disable-security-policies-and-disable-recommendations).

### <a name="if-a-security-control-offers-me-zero-points-towards-my-secure-score-should-i-ignore-it"></a>Se un controllo di sicurezza offre zero punti per il punteggio di sicurezza, è consigliabile ignorarlo?
In alcuni casi, si noterà un punteggio massimo del controllo maggiore di zero, ma l'effetto sarà lo stesso di un punteggio pari a zero. Quando il punteggio incrementale per la correzione delle risorse è trascurabile, viene arrotondato a zero. Non ignorare queste raccomandazioni perché contribuiscono a migliorare la sicurezza. L'unica eccezione è il controllo "Procedura consigliata aggiuntiva". La correzione di queste raccomandazioni non aumenterà il punteggio, ma migliorerà la sicurezza complessiva.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive il punteggio di sicurezza e i controlli di sicurezza introdotti. Per i materiali correlati, vedere gli articoli seguenti:

- [Learn about the different elements of a recommendation](security-center-recommendations.md) (Informazioni sui diversi elementi di una raccomandazione)
- [Learn how to remediate recommendations](security-center-remediate-recommendations.md) (Informazioni su come correggere le raccomandazioni)
- [Visualizza gli strumenti basati su GitHub per lavorare a livello di codice con un punteggio sicuro](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)