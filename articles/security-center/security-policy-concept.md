---
title: Informazioni sui criteri di sicurezza, le iniziative e le raccomandazioni nel centro sicurezza di Azure
description: Informazioni sui criteri di sicurezza, le iniziative e le raccomandazioni nel centro sicurezza di Azure.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4dc29c8b52a3d0953445666672a716af013ee408
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176434"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Che cosa sono i criteri di sicurezza, le iniziative e le raccomandazioni?

Il Centro sicurezza applica le iniziative di sicurezza alle sottoscrizioni. Queste iniziative contengono uno o più criteri di sicurezza. Ognuno di questi criteri genera una raccomandazione sulla sicurezza per migliorare il comportamento di sicurezza. In questa pagina viene illustrata in modo dettagliato ognuna di queste idee.


## <a name="what-is-a-security-policy"></a>Cosa sono i criteri di sicurezza?

Una definizione di criteri di Azure, creata in criteri di Azure, è una regola relativa a specifiche condizioni di sicurezza che si desidera controllare. Le definizioni predefinite includono elementi come il controllo del tipo di risorse che è possibile distribuire o l'utilizzo di tag in tutte le risorse. È anche possibile creare definizioni di criteri personalizzate.

Per implementare le definizioni dei criteri, sia predefinite che personalizzate, è necessario assegnarle. Tutti questi criteri possono essere assegnati tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

Sono disponibili diversi tipi di criteri in criteri di Azure. Il Centro sicurezza usa principalmente i criteri di "controllo" che controllano condizioni e configurazioni specifiche, quindi segnalano la conformità. Sono inoltre disponibili criteri "applica" che possono essere utilizzati per applicare impostazioni sicure.

## <a name="what-is-a-security-initiative"></a>Che cos'è un'iniziativa di sicurezza?

Un'iniziativa di Azure è una raccolta di definizioni di criteri di Azure, o regole, raggruppate per un obiettivo o uno scopo specifico. Le iniziative di Azure semplificano la gestione dei criteri raggruppando un set di criteri, logicamente, come un singolo elemento.

Un'iniziativa di sicurezza definisce la configurazione desiderata dei carichi di lavoro e contribuisce a garantire la conformità ai requisiti di sicurezza dell'azienda o degli enti di regolamentazione.

Analogamente ai criteri di sicurezza, le iniziative del Centro sicurezza vengono create anche in criteri di Azure. È possibile usare i [criteri di Azure](../governance/policy/overview.md) per gestire i criteri, creare iniziative e assegnare iniziative a più sottoscrizioni o a interi gruppi di gestione.

L'iniziativa predefinita assegnata automaticamente a ogni sottoscrizione nel centro sicurezza di Azure è benchmark di sicurezza di Azure. Questo benchmark è il set di linee guida per le procedure consigliate per la sicurezza e la conformità di Microsoft per Azure, basate su Framework di conformità comuni. Questo benchmark ampiamente rispettato si basa sui controlli di [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) con particolare attenzione alla sicurezza incentrata sul cloud. Altre informazioni su [Azure Security Benchmark](../security/benchmarks/introduction.md).

Il Centro sicurezza offre le opzioni seguenti per lavorare con i criteri e le iniziative di sicurezza:

- **Visualizzare e modificare l'iniziativa predefinita incorporata** : quando si Abilita il Centro sicurezza, l'iniziativa denominata ' benchmark di sicurezza di Azure ' viene assegnata automaticamente a tutte le sottoscrizioni registrate del Centro sicurezza. Per personalizzare questa iniziativa, è possibile abilitare o disabilitare singoli criteri al suo interno modificando i parametri di un criterio. Per informazioni sulle opzioni disponibili, vedere l'elenco dei [criteri di sicurezza predefiniti](./policy-reference.md) .

- **Aggiungere iniziative personalizzate** : se si desidera personalizzare le iniziative di sicurezza applicate alla sottoscrizione, è possibile eseguire questa operazione nel centro sicurezza. Si riceveranno quindi consigli se i computer non seguono i criteri creati. Per istruzioni sulla creazione e l'assegnazione di criteri personalizzati, vedere [uso di iniziative e criteri di sicurezza personalizzati](custom-security-policies.md).

- **Aggiungere standard di conformità normativi come iniziative** : il dashboard di conformità normativa del Centro sicurezza Mostra lo stato di tutte le valutazioni all'interno dell'ambiente nel contesto di uno standard o di una norma particolare, ad esempio Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-V2020. Per ulteriori informazioni, vedere [migliorare la conformità alle normative](security-center-compliance-dashboard.md).

## <a name="what-is-a-security-recommendation"></a>Che cos'è un suggerimento per la sicurezza?

Usando i criteri, il Centro sicurezza analizza periodicamente lo stato di conformità delle risorse per identificare i potenziali problemi di configurazione e i punti di debolezza della sicurezza. Vengono quindi fornite indicazioni su come correggere tali problemi. Le raccomandazioni sono il risultato della valutazione delle risorse rispetto ai criteri pertinenti e l'identificazione delle risorse che non soddisfano i requisiti definiti.

Il Centro sicurezza apporta le raccomandazioni sulla sicurezza in base alle iniziative scelte. Quando un criterio dell'iniziativa viene confrontato con le risorse e trova uno o più che non sono conformi, viene presentato come raccomandazione nel centro sicurezza.

Le raccomandazioni sono azioni da intraprendere per proteggere e rafforzare le risorse. Ogni raccomandazione fornisce le informazioni seguenti:

- Breve descrizione del problema
- Procedura di correzione da eseguire per implementare la raccomandazione
- Risorse interessate

In pratica, funziona come segue:

1. Il benchmark di sicurezza di Azure è un' ***iniziativa***
1. Include un ***criterio*** per richiedere a tutti gli account di archiviazione di Azure di limitare l'accesso alla rete, in modo da ridurre la superficie di attacco. Questo criterio è denominato "gli account di archiviazione devono limitare l'accesso alla rete usando le regole della rete virtuale" e possono essere disabilitati o abilitati da criteri di Azure
1. Se il Centro sicurezza di Azure trova un account di archiviazione di Azure in una qualsiasi delle sottoscrizioni protette, valuta gli account per verificare se sono protetti con le regole della rete virtuale. In caso contrario, viene visualizzata una ***raccomandazione*** per correggere la situazione e rafforzare la sicurezza di tali risorse. 

Un'iniziativa (1) include quindi i criteri (2) che generano indicazioni quando appropriato (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Visualizzazione della relazione tra un Consiglio e un criterio

Come indicato in precedenza, le raccomandazioni predefinite del Centro sicurezza sono basate sul benchmark di sicurezza di Azure. Quasi ogni raccomandazione ha un criterio sottostante derivato da un requisito nel benchmark.

Quando si esaminano i dettagli di una raccomandazione, è spesso utile poter visualizzare i criteri sottostanti. Per ogni raccomandazione supportata da un criterio, usare il collegamento **Visualizza definizione criteri** della pagina dei dettagli della raccomandazione per passare direttamente alla voce relativa ai criteri di Azure per i criteri pertinenti:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Collegamento alla pagina Criteri di Azure per i criteri specifici che supportano una raccomandazione":::

Usare questo collegamento per visualizzare la definizione dei criteri ed esaminare la logica di valutazione. 

Se si sta esaminando l'elenco di raccomandazioni nella Guida di [riferimento](recommendations-reference.md)per le raccomandazioni sulla sicurezza, verranno visualizzati anche i collegamenti alle pagine di definizione dei criteri:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Accesso alla pagina dei criteri di Azure per un criterio specifico direttamente dalla pagina di riferimento consigli del Centro sicurezza di Azure":::


## <a name="next-steps"></a>Passaggi successivi

Questa pagina ha illustrato, a un livello elevato, i concetti e le relazioni di base tra criteri, iniziative e consigli. Per informazioni correlate, vedere:

- [Creare iniziative personalizzate](custom-security-policies.md)
- [Disabilitare i criteri di sicurezza per disabilitare le raccomandazioni](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Informazioni su come modificare i criteri di sicurezza in criteri di Azure](../governance/policy/tutorials/create-and-manage.md)