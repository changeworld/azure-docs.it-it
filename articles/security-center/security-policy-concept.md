---
title: Informazioni su criteri di sicurezza, iniziative e raccomandazioni in Centro sicurezza di Azure
description: Informazioni su criteri di sicurezza, iniziative e raccomandazioni in Centro sicurezza di Azure.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4eea2611997732a263e9e824bc150b45ed145ecd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738973"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Che cosa sono i criteri di sicurezza, le iniziative e le raccomandazioni?

Il Centro sicurezza applica le iniziative di sicurezza alle sottoscrizioni. Queste iniziative contengono uno o più criteri di sicurezza. Ognuno di questi criteri comporta una raccomandazione sulla sicurezza per migliorare il proprio stato di sicurezza. Questa pagina illustra in dettaglio ognuna di queste idee.


## <a name="what-is-a-security-policy"></a>Cosa sono i criteri di sicurezza?

Una definizione di criteri di Azure, creata in Criteri di Azure, è una regola sulle condizioni di sicurezza specifiche che si vuole controllare. Le definizioni incorporate includono elementi come il controllo del tipo di risorse che è possibile distribuire o l'applicazione dell'uso di tag a tutte le risorse. È anche possibile creare definizioni di criteri personalizzate.

Per implementare queste definizioni di criteri( incorporate o personalizzate), è necessario assegnarle. Tutti questi criteri possono essere assegnati tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

Esistono diversi tipi di criteri in Criteri di Azure. Il Centro sicurezza usa principalmente criteri di controllo che controllano condizioni e configurazioni specifiche e quindi segnalano la conformità. Esistono anche criteri "Imponi" che possono essere usati per applicare impostazioni sicure.

## <a name="what-is-a-security-initiative"></a>Che cos'è un'iniziativa di sicurezza?

Un'iniziativa di Azure è una raccolta di definizioni di criteri di Azure, o regole, raggruppate in base a uno scopo o un obiettivo specifico. Le iniziative di Azure semplificano la gestione dei criteri raggruppando un set di criteri, logicamente, come singolo elemento.

Un'iniziativa di sicurezza definisce la configurazione desiderata dei carichi di lavoro e consente di garantire la conformità ai requisiti di sicurezza dell'azienda o delle autorità di controllo.

Analogamente ai criteri di sicurezza, anche le iniziative del Centro sicurezza vengono create in Criteri di Azure. È possibile usare [Criteri di Azure](../governance/policy/overview.md) per gestire i criteri, creare iniziative e assegnare iniziative a più sottoscrizioni o a interi gruppi di gestione.

L'iniziativa predefinita assegnata automaticamente a ogni sottoscrizione in Centro sicurezza di Azure è Azure Security Benchmark. Questo benchmark è il set di linee guida per la sicurezza e la conformità basato su framework di conformità comuni creati da Microsoft e specifici di Azure. Questo benchmark ampiamente rispettato si basa sui controlli del [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e del National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) con particolare attenzione alla sicurezza incentrata sul cloud. Altre informazioni su [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction).

Il Centro sicurezza offre le opzioni seguenti per l'uso di iniziative e criteri di sicurezza:

- **Visualizzare e modificare l'iniziativa** predefinita predefinita: quando si abilita Centro sicurezza, l'iniziativa denominata "Azure Security Benchmark" viene assegnata automaticamente a tutte le sottoscrizioni registrate del Centro sicurezza. Per personalizzare questa iniziativa, è possibile abilitare o disabilitare singoli criteri al suo interno modificando i parametri di un criterio. Vedere l'elenco [dei criteri di](./policy-reference.md) sicurezza predefiniti per comprendere le opzioni disponibili predefinite.

- **Aggiungere iniziative personalizzate:** se si vogliono personalizzare le iniziative di sicurezza applicate alla sottoscrizione, è possibile farlo nel Centro sicurezza. Si riceveranno quindi raccomandazioni se i computer non seguono i criteri creati. Per istruzioni sulla compilazione e l'assegnazione di criteri personalizzati, vedere Uso di iniziative [e criteri di sicurezza personalizzati.](custom-security-policies.md)

- Aggiungere standard di conformità alle normative come **iniziative:** il dashboard di conformità alle normative del Centro sicurezza mostra lo stato di tutte le valutazioni all'interno dell'ambiente nel contesto di un determinato standard o regolamento (ad esempio Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Per altre informazioni, vedere [Migliorare la conformità alle normative.](security-center-compliance-dashboard.md)

## <a name="what-is-a-security-recommendation"></a>Che cos'è un suggerimento per la sicurezza?

Usando i criteri, il Centro sicurezza analizza periodicamente lo stato di conformità delle risorse per identificare potenziali errori di configurazione e punti deboli della sicurezza. Fornisce quindi consigli su come risolvere questi problemi. Le raccomandazioni sono il risultato della valutazione delle risorse rispetto ai criteri pertinenti e dell'identificazione delle risorse che non sono in grado di soddisfare i requisiti definiti.

Il Centro sicurezza crea le raccomandazioni sulla sicurezza in base alle iniziative scelte. Quando un criterio dell'iniziativa viene confrontato con le risorse e trova uno o più criteri non conformi, viene presentato come raccomandazione nel Centro sicurezza.

Le raccomandazioni sono azioni da intraprendere per proteggere e proteggere le risorse. Ogni raccomandazione fornisce le informazioni seguenti:

- Breve descrizione del problema
- Procedura di correzione da eseguire per implementare la raccomandazione
- Risorse interessate

In pratica, funziona nel modo seguente:

1. Azure Security Benchmark è ***un'iniziativa***
1. Include un criterio che ***richiede tutti*** gli account Archiviazione di Azure per limitare l'accesso alla rete in modo da ridurre la superficie di attacco. Questo criterio è denominato "Gli account di archiviazione devono limitare l'accesso alla rete usando le regole di rete virtuale" e possono essere disabilitati o abilitati da Criteri di Azure
1. Se Centro sicurezza di Azure trova un account Archiviazione di Azure in una delle sottoscrizioni protette, valuta tali account per verificare se sono protetti con regole di rete virtuale. In caso contrario, viene  visualizzato un consiglio per risolvere tale situazione e per la protezione avanzata di tali risorse. 

Un'iniziativa (1) include quindi criteri (2) che generano raccomandazioni quando appropriato (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Visualizzazione della relazione tra una raccomandazione e un criterio

Come accennato in precedenza, le raccomandazioni integrate del Centro sicurezza si basano su Azure Security Benchmark. Quasi tutte le raccomandazioni hanno un criterio sottostante derivato da un requisito nel benchmark.

Quando si esaminano i dettagli di una raccomandazione, è spesso utile essere in grado di visualizzare i criteri sottostanti. Per ogni raccomandazione supportata da  un criterio, usare il collegamento Visualizza definizione dei criteri dalla pagina dei dettagli della raccomandazione per passare direttamente alla voce Criteri di Azure per i criteri pertinenti:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Collegamento alla Criteri di Azure per i criteri specifici che supportano una raccomandazione":::

Usare questo collegamento per visualizzare la definizione dei criteri ed esaminare la logica di valutazione. 

Se si sta esaminando l'elenco di raccomandazioni nella guida di riferimento sulle raccomandazioni per la [sicurezza,](recommendations-reference.md)verranno visualizzati anche i collegamenti alle pagine di definizione dei criteri:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Accesso alla pagina Criteri di Azure per un criterio specifico direttamente dalla pagina di riferimento Centro sicurezza di Azure raccomandazioni":::


## <a name="next-steps"></a>Passaggi successivi

Questa pagina ha illustrato, a livello di alto livello, i concetti di base e le relazioni tra criteri, iniziative e raccomandazioni. Per informazioni correlate, vedere:

- [Creare iniziative personalizzate](custom-security-policies.md)
- [Disabilitare i criteri di sicurezza per disabilitare le raccomandazioni](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Informazioni su come modificare i criteri di sicurezza in Criteri di Azure](../governance/policy/tutorials/create-and-manage.md)