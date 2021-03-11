---
title: Esentare una raccomandazione del Centro sicurezza di Azure da una risorsa, una sottoscrizione, un gruppo di gestione e un punteggio sicuro
description: Informazioni su come creare regole per esentare le raccomandazioni di sicurezza da sottoscrizioni o gruppi di gestione e impedire che influiscano sul punteggio sicuro
author: memildin
ms.author: memildin
ms.date: 03/10/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: a7a010b1014181ed325500fa501212579ef67d26
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617574"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>Esenzione di risorse e consigli dal punteggio sicuro 

Una priorità di base di ogni team di sicurezza consiste nel garantire che gli analisti possano concentrarsi sulle attività e gli eventi imprevisti che interessano l'organizzazione. Il Centro sicurezza offre molte funzionalità per personalizzare l'esperienza e garantire che il Punteggio sicuro rispecchi le priorità di sicurezza dell'organizzazione. L'opzione **esenzione** è una funzionalità di questo tipo.

Quando si esaminano le raccomandazioni per la sicurezza nel centro sicurezza di Azure, una delle prime informazioni riportate è l'elenco delle risorse interessate.

In alcuni casi, una risorsa viene elencata e non deve essere inclusa. In alternativa, una raccomandazione verrà visualizzata in un ambito in cui si ritiene che non appartenga. È possibile che la risorsa sia stata risolta da un processo non rilevato dal centro sicurezza. Il suggerimento potrebbe non essere appropriato per una sottoscrizione specifica. È possibile che l'organizzazione abbia semplicemente deciso di accettare i rischi correlati alla risorsa o al Consiglio specifico.

In questi casi, è possibile creare un'esenzione per un Consiglio per:

- **Esentare una risorsa** per assicurarsi che non sia elencata con le risorse non integre in futuro e non influisca sul punteggio sicuro. La risorsa sarà elencata come non applicabile e il motivo verrà visualizzato come "esentato" con la giustificazione specifica selezionata.

- **Esentare una sottoscrizione o un gruppo di gestione** per assicurarsi che la raccomandazione non influisca sul punteggio sicuro e non venga visualizzata per la sottoscrizione o il gruppo di gestione in futuro. Questo si riferisce alle risorse esistenti e a quelle create in futuro. La raccomandazione verrà contrassegnata con la giustificazione specifica che si seleziona per l'ambito selezionato.

## <a name="availability"></a>Disponibilità

| Aspetto                          | Dettagli                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stato della versione:                  | Anteprima<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                                                                                                                                                                                                             |
| Prezzi:                        | Si tratta di una funzionalità di criteri di Azure Premium offerta per i clienti di Azure Defender senza costi aggiuntivi. Per gli altri utenti, potrebbero essere applicati addebiti in futuro.                                                                                                                                                                 |
| Autorizzazioni e ruoli obbligatori: | **Proprietario della sottoscrizione** o **collaboratore dei criteri** per creare un'esenzione<br>Per creare una regola, è necessario disporre delle autorizzazioni per modificare i criteri in criteri di Azure.<br>Per altre informazioni, vedere [autorizzazioni RBAC di Azure in criteri di Azure](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).                                            |
| Limitazioni                    | Le esenzioni possono essere create solo per le raccomandazioni incluse nell'iniziativa predefinita del Centro sicurezza, benchmark di sicurezza di Azure. I consigli generati da iniziative personalizzate non possono essere esentati. Altre informazioni sulle relazioni tra [criteri, iniziative e raccomandazioni](security-policy-concept.md). |
| Cloud:                         | ![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![No](./media/icons/no-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)                                                                                                                                                                                         |
|                                 |                                                                                                                                                                                                                                                                                                                                    |

## <a name="define-an-exemption"></a>Definire un'esenzione

Per ottimizzare le raccomandazioni relative alla sicurezza che il Centro sicurezza apporta per le sottoscrizioni, il gruppo di gestione o le risorse, è possibile creare una regola di esenzione per:

- Contrassegnare una **raccomandazione** specifica o come "mitigata" o "rischio accettato". È possibile creare esenzioni delle raccomandazioni per una sottoscrizione, più sottoscrizioni o un intero gruppo di gestione.
- Contrassegnare **una o più risorse** come "mitigate" o "rischio accettate" per una raccomandazione specifica.

> [!NOTE]
> Le esenzioni possono essere create solo per le raccomandazioni incluse nell'iniziativa predefinita del Centro sicurezza, benchmark di sicurezza di Azure. Le indicazioni generate da eventuali iniziative personalizzate assegnate alle sottoscrizioni non possono essere esentate. Altre informazioni sulle relazioni tra [criteri, iniziative e raccomandazioni](security-policy-concept.md).

> [!TIP]
> È anche possibile creare esenzioni usando l'API. Per un esempio JSON e una spiegazione delle strutture pertinenti, vedere [struttura di esenzione dei criteri di Azure](../governance/policy/concepts/exemption-structure.md).

Per creare una regola di esenzione:

1. Aprire la pagina dei dettagli delle raccomandazioni per una raccomandazione specifica.

1. Dalla barra degli strumenti nella parte superiore della pagina selezionare **esenzione**.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="Creare una regola di esenzione per una raccomandazione da esentare da una sottoscrizione o un gruppo di gestione.":::

1. Nel riquadro **esenzione** :
    1. Selezionare l'ambito per questa regola di esenzione:
        - Se si seleziona un gruppo di gestione, la raccomandazione verrà esentata da tutte le sottoscrizioni all'interno del gruppo
        - Se si sta creando questa regola per esentare una o più risorse dalla raccomandazione, scegliere "risorse selezionate" e selezionare quelle pertinenti dall'elenco

    1. Immettere un nome per la regola di esenzione.
    1. Facoltativamente, impostare una data di scadenza.
    1. Selezionare la categoria per l'esenzione:
        - **Risolto tramite la terza parte (attenuata)** : se si usa un servizio di terze parti non identificato dal centro sicurezza. 

            > [!NOTE]
            > Quando si esenta una raccomandazione come attenuata, non vengono assegnati punti per il Punteggio sicuro. Tuttavia, poiché i punti non vengono *rimossi* per le risorse non integre, il risultato è che il punteggio aumenterà.

        - **Rischio accettato (rinuncia)** : se si è deciso di accettare il rischio di non attenuare questa raccomandazione
    1. Facoltativamente, immettere una descrizione.
    1. Selezionare **Crea**.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="Procedura per creare una regola di esenzione per esentare una raccomandazione dalla sottoscrizione o dal gruppo di gestione":::

    Quando l'esenzione diventa effettiva (potrebbero essere necessari fino a 30 minuti):
    - Il Punteggio sicuro non avrà alcun effetto sulla raccomandazione o sulle risorse.
    - Se sono state esentate risorse specifiche, verranno elencate nella scheda **non applicabile** della pagina dei dettagli della raccomandazione.
    - Se è stata esentata una raccomandazione, questa verrà nascosta per impostazione predefinita nella pagina delle raccomandazioni del Centro sicurezza. Ciò è dovuto al fatto che le opzioni predefinite del filtro di **stato della raccomandazione** in tale pagina sono di escludere le raccomandazioni **non applicabili** . Lo stesso accade se si esentano tutte le raccomandazioni in un controllo di sicurezza.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Filtri predefiniti nella pagina raccomandazioni del Centro sicurezza di Azure Nascondi le raccomandazioni non applicabili e i controlli di sicurezza":::

    - L'elenco di informazioni nella parte superiore della pagina dei dettagli della raccomandazione aggiorna il numero di risorse esentate:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Numero di risorse esentate":::

1. Per esaminare le risorse esentate, aprire la scheda **non applicabile** :

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Modifica di un'esenzione":::

    Il motivo di ogni esenzione è incluso nella tabella (1).

    Per modificare o eliminare un'esenzione, selezionare il menu con i puntini di sospensione ("...") come illustrato (2).

1. Per esaminare tutte le regole di esenzione per la sottoscrizione, selezionare **Visualizza esenzioni** dall'elenco informazioni:

    > [!IMPORTANT]
    > Per visualizzare le esenzioni specifiche relative a una raccomandazione, filtrare l'elenco in base all'ambito pertinente e al nome di raccomandazione.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Pagina di esenzione di criteri di Azure":::

    > [!TIP]
    > In alternativa, [usare Azure Resource Graph per trovare raccomandazioni con esenzioni](#find-recommendations-with-exemptions-using-azure-resource-graph).

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>Monitorare le esenzioni create nelle sottoscrizioni

Come spiegato in precedenza in questa pagina, le regole di esenzione sono uno strumento potente che offre un controllo granulare sulle raccomandazioni che interessano le risorse nelle sottoscrizioni e nei gruppi di gestione. 

Per tenere traccia del modo in cui gli utenti stanno esercitando questa funzionalità, è stato creato un modello di Azure Resource Manager (ARM) che distribuisce un playbook di app per la logica e tutte le connessioni API necessarie per ricevere una notifica quando è stata creata un'esenzione.

- Per altre informazioni sul PlayBook, vedere il post di Blog della community tecnica [come tenere traccia delle esenzioni delle risorse nel centro sicurezza di Azure](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)
- Il modello ARM è presente nel [repository GitHub del Centro sicurezza di Azure](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)
- Per distribuire tutti i componenti necessari, [usare questo processo automatizzato](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Trovare raccomandazioni con esenzioni usando il grafico delle risorse di Azure

Azure Resource Graph (ARG) fornisce l'accesso immediato alle informazioni sulle risorse negli ambienti cloud con funzionalità di filtro, raggruppamento e ordinamento affidabili. Si tratta di un modo rapido ed efficiente di eseguire query sulle informazioni nelle sottoscrizioni di Azure a livello di codice o dall'interno del portale di Azure.

Per visualizzare tutte le raccomandazioni con regole di esenzione:

1. Aprire **Esplora grafico risorse di Azure**.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Avvio della pagina consigli di Azure Resource Graph Explorer * *" :::

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


Ulteriori informazioni sono disponibili nelle pagine seguenti:
- Vedere [altre informazioni su Azure Resource Graph](../governance/resource-graph/index.yml).
- [Come creare query con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Linguaggio di query Kusto (KQL)](/azure/data-explorer/kusto/query/)





## <a name="faq---exemption-rules"></a>Domande frequenti: regole di esenzione

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>Cosa accade quando una raccomandazione si trova in più iniziative di criteri?

In alcuni casi, una raccomandazione sulla sicurezza viene visualizzata in più di un'iniziativa di criteri. Se si dispone di più istanze della stessa raccomandazione assegnate alla stessa sottoscrizione e si crea un'esenzione per la raccomandazione, questo influirà su tutte le iniziative per le quali si dispone dell'autorizzazione per la modifica. 

Ad esempio, la raccomandazione * * * * fa parte dell'iniziativa di criteri predefinita assegnata a tutte le sottoscrizioni di Azure dal centro sicurezza di Azure. E anche in XXXXX.

Se si tenta di creare un'esenzione per questa raccomandazione, verrà visualizzato uno dei due messaggi seguenti:

- Se si dispone delle autorizzazioni necessarie per modificare entrambe le iniziative, verrà visualizzato quanto segue:

    *Questa raccomandazione è inclusa in diverse iniziative di criteri: [nomi di iniziativa separati da virgola]. Verranno create esenzioni in tutte.*  

- Se non si dispone di autorizzazioni sufficienti per entrambe le iniziative, verrà visualizzato il messaggio seguente:

    *Si dispone di autorizzazioni limitate per applicare l'esenzione per tutte le iniziative dei criteri, le esenzioni verranno create solo nelle iniziative con autorizzazioni sufficienti.*


## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come esentare una risorsa da una raccomandazione, in modo che non influisca sul punteggio sicuro. Per ulteriori informazioni sul punteggio sicuro, vedere:

- [Punteggio di sicurezza nel Centro sicurezza di Azure](secure-score-security-controls.md)