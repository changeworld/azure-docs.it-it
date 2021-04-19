---
title: Escludere una raccomandazione Centro sicurezza di Azure una risorsa, una sottoscrizione, un gruppo di gestione e un punteggio di sicurezza
description: Informazioni su come creare regole per esentare le raccomandazioni sulla sicurezza da sottoscrizioni o gruppi di gestione e impedire loro di influire sul punteggio di sicurezza
author: memildin
ms.author: memildin
ms.date: 03/11/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 13abb35d0fa9ad3ee949b6edf5205de601a02956
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718558"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Esenzione di risorse e raccomandazioni dal punteggio di sicurezza 

Una priorità fondamentale di ogni team di sicurezza è garantire che gli analisti possano concentrarsi sulle attività e sugli eventi imprevisti importanti per l'organizzazione. Il Centro sicurezza offre molte funzionalità per personalizzare l'esperienza e assicurarsi che il punteggio di sicurezza rifletta le priorità di sicurezza dell'organizzazione. **L'opzione** di esenzione è una di queste funzionalità.

Quando si esaminano le raccomandazioni sulla sicurezza in Centro sicurezza di Azure, una delle prime informazioni esaminate è l'elenco delle risorse interessate.

In alcuni casi, viene elencata una risorsa che non dovrebbe essere inclusa. Oppure, una raccomandazione verrà mostrata in un ambito a cui si è insodd di non appartenere. La risorsa potrebbe essere stata monitorata da un processo non monitorato dal Centro sicurezza. La raccomandazione potrebbe non essere appropriata per una sottoscrizione specifica. O forse l'organizzazione ha semplicemente deciso di accettare i rischi correlati alla risorsa o alla raccomandazione specifica.

In questi casi, è possibile creare un'esenzione per una raccomandazione per:

- **Esentare una** risorsa per assicurarsi che non sia elencata con le risorse non integre in futuro e non influisce sul punteggio di sicurezza. La risorsa verrà elencata come non applicabile e il motivo verrà visualizzato come "esentato" con la giustificazione specifica selezionata.

- **Esentare una sottoscrizione o** un gruppo di gestione per assicurarsi che la raccomandazione non influisca sul punteggio di sicurezza e non verrà visualizzata per la sottoscrizione o il gruppo di gestione in futuro. Questo è correlato alle risorse esistenti e a qualsiasi risorsa creata in futuro. La raccomandazione verrà contrassegnata con la giustificazione specifica selezionata per l'ambito selezionato.

## <a name="availability"></a>Disponibilità

| Aspetto                          | Dettagli                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stato della versione:                  | Anteprima<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Prezzi:                        | Si tratta di una Criteri di Azure premium offerta per i clienti Azure Defender senza costi aggiuntivi. Per altri utenti, gli addebiti potrebbero essere applicati in futuro.                                                                                                                                                                 |
| Autorizzazioni e ruoli obbligatori: | **Proprietario o** **collaboratore ai criteri di risorsa per** creare un'esenzione<br>Per creare una regola, sono necessarie le autorizzazioni per modificare i criteri in Criteri di Azure.<br>Per altre informazioni, [vedere Autorizzazioni per il controllo degli accessi in base al ruolo di Azure in Criteri di Azure](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).                                            |
| Limitazioni                    | Le esenzioni possono essere create solo per le raccomandazioni incluse nell'iniziativa predefinita del Centro sicurezza, [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction)o per qualsiasi iniziativa standard normativa fornita. Le raccomandazioni generate da iniziative personalizzate non possono essere esentate. Altre informazioni sulle relazioni tra [criteri, iniziative e raccomandazioni.](security-policy-concept.md) |
| Cloud:                         | ![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>Definire un'esenzione

Per ottimizzare le raccomandazioni di sicurezza fornite dal Centro sicurezza per le sottoscrizioni, il gruppo di gestione o le risorse, è possibile creare una regola di esenzione per:

- Contrassegnare una **raccomandazione specifica** o come "mitigata" o "accettata dal rischio". È possibile creare esenzioni raccomandazioni per una sottoscrizione, più sottoscrizioni o un intero gruppo di gestione.
- Contrassegnare **una o più risorse** come "mitigate" o "accettate dal rischio" per una raccomandazione specifica.

> [!NOTE]
> Le esenzioni possono essere create solo per le raccomandazioni incluse nell'iniziativa predefinita del Centro sicurezza, in Azure Security Benchmark o in una delle iniziative standard normative fornite. Le raccomandazioni generate da qualsiasi iniziativa personalizzata assegnata alle sottoscrizioni non possono essere esentate. Altre informazioni sulle relazioni tra [criteri, iniziative e raccomandazioni.](security-policy-concept.md)

> [!TIP]
> È anche possibile creare esenzioni usando l'API. Per un esempio json e una spiegazione delle strutture pertinenti, vedere la Criteri di Azure [di esenzione](../governance/policy/concepts/exemption-structure.md).

Per creare una regola di esenzione:

1. Aprire la pagina dei dettagli delle raccomandazioni per la raccomandazione specifica.

1. Sulla barra degli strumenti nella parte superiore della pagina selezionare **Esente.**

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Creare una regola di esenzione per una raccomandazione da esentare da una sottoscrizione o da un gruppo di gestione.":::

1. Nel riquadro **Esenzione:**
    1. Selezionare l'ambito per questa regola di esenzione:
        - Se si seleziona un gruppo di gestione, la raccomandazione verrà esentato da tutte le sottoscrizioni all'interno di tale gruppo
        - Se si sta creando questa regola per esentare una o più risorse dalla raccomandazione, scegliere "Risorse selezionate" e selezionare quelle pertinenti dall'elenco

    1. Immettere un nome per questa regola di esenzione.
    1. Facoltativamente, impostare una data di scadenza.
    1. Selezionare la categoria per l'esenzione:
        - **Risoluzione tramite terze parti (attenuata):** se si usa un servizio di terze parti non identificato dal Centro sicurezza. 

            > [!NOTE]
            > Quando si esenta una raccomandazione come mitigata, non vengono dati punti verso il punteggio di sicurezza. Tuttavia, poiché i punti non *vengono rimossi* per le risorse non integre, il risultato è che il punteggio aumenterà.

        - **Rischio accettato (deroga):** se si è deciso di accettare il rischio di non attenuare questa raccomandazione
    1. Facoltativamente, immettere una descrizione.
    1. Selezionare **Crea**.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Passaggi per creare una regola di esenzione per esentare una raccomandazione dalla sottoscrizione o dal gruppo di gestione":::

    Quando l'esenzione ha effetto (potrebbero essere necessari fino a 30 minuti):
    - La raccomandazione o le risorse non inciderà sul punteggio di sicurezza.
    - Se sono stati esentati risorse specifiche, queste verranno elencate nella scheda **Non** applicabile della pagina dei dettagli della raccomandazione.
    - Se è stata esentato un consiglio, questa verrà nascosta per impostazione predefinita nella pagina raccomandazioni del Centro sicurezza. Ciò è dovuto al fatto che le opzioni predefinite del **filtro Stato** raccomandazione in tale pagina escludono **Le raccomandazioni non** applicabili. Lo stesso vale se si esenta tutte le raccomandazioni in un controllo di sicurezza.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="I filtri predefiniti nella Centro sicurezza di Azure di raccomandazioni di un utente nascondono le raccomandazioni e i controlli di sicurezza non applicabili":::

    - La striscia di informazioni nella parte superiore della pagina dei dettagli delle raccomandazioni aggiorna il numero di risorse esentate:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Numero di risorse esentate":::

1. Per esaminare le risorse esentate, aprire la **scheda Non** applicabile:

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Modifica di un'esenzione":::

    Il motivo di ogni esenzione è incluso nella tabella (1).

    Per modificare o eliminare un'esenzione, selezionare il menu con i puntini di sospensione ("...") come illustrato (2).

1. Per esaminare tutte le regole di esenzione nella sottoscrizione, selezionare **Visualizza esenzioni** dalla striscia di informazioni:

    > [!IMPORTANT]
    > Per visualizzare le esenzioni specifiche rilevanti per una raccomandazione, filtrare l'elenco in base all'ambito e al nome della raccomandazione pertinenti.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Criteri di Azure di esenzione":::

    > [!TIP]
    > In alternativa, [usare Azure Resource Graph per trovare raccomandazioni con esenzioni](#find-recommendations-with-exemptions-using-azure-resource-graph).

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Monitorare le esenzioni create nelle sottoscrizioni

Come illustrato in precedenza in questa pagina, le regole di esenzione sono uno strumento potente che offre un controllo granulare sulle raccomandazioni che interessano le risorse nelle sottoscrizioni e nei gruppi di gestione. 

Per tenere traccia del modo in cui gli utenti esercitano questa funzionalità, è stato creato un modello di Azure Resource Manager (ARM) che distribuisce un Playbook app per la logica e tutte le connessioni API necessarie per notificare quando è stata creata un'esenzione.

- Per altre informazioni sul playbook, vedere il post di blog della community tech [How to keep track of Resource Exemptions in Centro sicurezza di Azure](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- Il modello arm è presente nel [repository GitHub Centro sicurezza di Azure](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)
- Per distribuire tutti i componenti necessari, [usare questo processo automatizzato](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Trovare raccomandazioni con esenzioni usando Azure Resource Graph

Azure Resource Graph (ARG) consente l'accesso immediato alle informazioni sulle risorse negli ambienti cloud con funzionalità di filtro, raggruppamento e ordinamento affidabili. Si tratta di un modo rapido ed efficiente di eseguire query sulle informazioni nelle sottoscrizioni di Azure a livello di codice o dall'interno del portale di Azure.

Per visualizzare tutte le raccomandazioni con regole di esenzione:

1. Aprire **Azure Resource Graph Explorer.**

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Avvio Azure Resource Graph di raccomandazione di Explorer**" :::

1. Immettere la query seguente e selezionare **Esegui query**.

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


Altre informazioni sono disponibili nelle pagine seguenti:
- Vedere [altre informazioni su Azure Resource Graph](../governance/resource-graph/index.yml).
- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Linguaggio di query Kusto (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>Domande frequenti - Regole di esenzione

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Cosa accade quando una raccomandazione si trova in più iniziative di criteri?

In alcuni casi, una raccomandazione sulla sicurezza viene visualizzata in più di un'iniziativa di criteri. Se sono presenti più istanze della stessa raccomandazione assegnate alla stessa sottoscrizione e si crea un'esenzione per la raccomandazione, questa operazione influirà su tutte le iniziative che si è autorizzati a modificare. 

Ad esempio, la raccomandazione **** fa parte dell'iniziativa dei criteri predefinita assegnata a tutte le sottoscrizioni di Azure Centro sicurezza di Azure. Anche in XXXXX.

Se si tenta di creare un'esenzione per questa raccomandazione, verrà visualizzato uno dei due messaggi seguenti:

- Se si hanno le autorizzazioni necessarie per modificare entrambe le iniziative, verrà visualizzato:

    *Questa raccomandazione è inclusa in diverse iniziative di criteri: [nomi di iniziative separati da virgola]. Verranno create esenzioni per tutte le esenzioni.*  

- Se non si hanno autorizzazioni sufficienti per entrambe le iniziative, verrà invece visualizzato questo messaggio:

    *Si dispone di autorizzazioni limitate per applicare l'esenzione a tutte le iniziative dei criteri. Le esenzioni verranno create solo per le iniziative con autorizzazioni sufficienti.*


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come esentare una risorsa da una raccomandazione in modo che non influisca sul punteggio di sicurezza. Per altre informazioni sul punteggio di sicurezza, vedere:

- [Punteggio di sicurezza nel Centro sicurezza di Azure](secure-score-security-controls.md)
