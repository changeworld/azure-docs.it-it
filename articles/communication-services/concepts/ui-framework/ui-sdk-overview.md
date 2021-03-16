---
title: Panoramica del Framework dell'interfaccia utente di servizi di comunicazione Azure
titleSuffix: An Azure Communication Services conceptual document
description: Informazioni sul Framework dell'interfaccia utente di servizi di comunicazione Azure
author: ddematheu2
ms.author: dademath
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 47a32815ded5809edfde856a38c69ec7c6fd6fdf
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493357"
---
# <a name="azure-communication-services-ui-framework"></a>Framework dell'interfaccia utente di servizi di comunicazione Azure

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="Confronto tra compositi precompilati e personalizzati":::

Il Framework dell'interfaccia utente di servizi di comunicazione Azure semplifica la compilazione di moderne esperienze utente per le comunicazioni. Offre una libreria di componenti dell'interfaccia utente pronti per l'ambiente di produzione che è possibile rilasciare nelle applicazioni:

- **Componenti compositi** : questi componenti sono soluzioni chiavi in volta che implementano scenari di comunicazione comuni. È possibile aggiungere rapidamente esperienze di chiamata o chat video alle applicazioni. I compositi sono componenti open source compilati con componenti di base.
- **Componenti di base** : questi componenti sono blocchi predefiniti open source che consentono di creare esperienze di comunicazione personalizzate. I componenti sono disponibili per le funzionalità di chiamata e di chat che possono essere combinate per le esperienze di compilazione. 

Queste librerie client dell'interfaccia utente usano tutti [il linguaggio di progettazione e gli asset Microsoft Fluent](https://developer.microsoft.com/fluentui/) . L'interfaccia utente di Fluent fornisce un livello di base per il Framework dell'interfaccia utente che è stato testato per tutti i prodotti Microsoft.

## <a name="differentiating-components-and-composites"></a>**Differenziazione dei componenti e dei compositi**

I **componenti di base** sono basati sulle librerie client principali dei servizi di comunicazione di Azure e implementano azioni di base, ad esempio l'inizializzazione delle librerie client di base, il rendering di video e la fornitura di controlli utente per muting, video on/off e così via. È possibile usare questi **componenti di base** per creare esperienze di layout personalizzate usando componenti di comunicazione precompilati e pronti per la produzione.

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="Panoramica del componente per il Framework dell'interfaccia utente":::

I **componenti composti** combinano più **componenti di base** per creare esperienze di comunicazione più complete. Questi componenti di livello superiore possono essere facilmente integrati in un'app esistente per eliminare un'esperienza di comunicazione completamente fledge senza l'attività di compilazione da zero. Gli sviluppatori possono concentrarsi sulla creazione dell'esperienza e del flusso desiderati nelle app e lasciano la complessità delle comunicazioni ai componenti compositi.

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="Panoramica di composite per il Framework dell'interfaccia utente":::

## <a name="what-ui-framework-is-best-for-my-project"></a>Quale Framework dell'interfaccia utente è migliore per il progetto?

La comprensione di questi requisiti ti aiuterà a scegliere la libreria client appropriata:

- **Quanto si desidera personalizzare?** Le librerie client di Azure Communication Core non hanno un'esperienza utente e sono progettate in modo da poter compilare qualsiasi tipo di esperienza utente. I componenti del Framework dell'interfaccia utente forniscono risorse dell'interfaccia utente al costo della personalizzazione ridotta.
- **Sono necessarie funzionalità per la riunione?** Il sistema di riunioni dispone di diverse funzionalità esclusive attualmente non disponibili nelle librerie client dei servizi di comunicazione di Azure principali, ad esempio sfondo sfocato e mano sollevata.
- **Quali piattaforme sono destinate?** Diverse piattaforme hanno funzionalità diverse.

Per informazioni dettagliate sulla disponibilità delle funzionalità nei vari [SDK di interfaccia utente](ui-sdk-features.md), vedere qui, ma sono riepilogati i compromessi chiave.

|Libreria client/SDK|Complessità di implementazione|    Funzionalità di personalizzazione|  Chiamata| Chat| [Interoperabilità team](./../teams-interop.md)
|---|---|---|---|---|---|---|
|Componenti compositi|Bassa|Bassa|✔|✔|✕
|Componenti di base|Livello medio|Livello medio|✔|✔|✕
|Librerie client Core|Alto|Alto|✔|✔ |✔

## <a name="cost"></a>Costo

L'uso dei framework dell'interfaccia utente di Azure non ha alcun costo o misurazione aggiuntivo di Azure. Si paga solo per l'utilizzo del servizio sottostante, usando gli stessi contatori di chiamata, chat e PSTN.

## <a name="supported-use-cases"></a>Casi d'uso supportati

Chiamata

- Aggiunta della chiamata ai servizi di comunicazione di Azure con ID gruppo

Chat

- Partecipare alla chat di servizi di comunicazione di Azure con ID thread

## <a name="supported-identities"></a>Identità supportate:

È necessaria un'identità dei servizi di comunicazione di Azure per inizializzare il Framework dell'interfaccia utente ed eseguire l'autenticazione al servizio. Per altre informazioni sull'autenticazione, vedere [autenticazione](../authentication.md) e [token di accesso](../../quickstarts/access-tokens.md)


## <a name="recommended-architecture"></a>Architettura consigliata 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="Architettura consigliata dell'interfaccia utente con architettura client-server ":::

I componenti compositi e di base vengono inizializzati usando un token di accesso di servizi di comunicazione di Azure. I token di accesso devono essere acquistati da servizi di comunicazione di Azure tramite un servizio attendibile gestito dall'utente. Per altre informazioni, vedere [Guida introduttiva: creare token di accesso](../../quickstarts/access-tokens.md) e un [servizio attendibile](../../tutorials/trusted-service-tutorial.md) .

Queste librerie client richiedono anche il contesto per la chiamata o la chat a cui verranno aggiunti. Analogamente ai token di accesso degli utenti, questo contesto deve essere divulgato ai client tramite il proprio servizio attendibile. L'elenco seguente riepiloga le funzioni di inizializzazione e di gestione delle risorse che è necessario rendere operativo.

| Responsabilità di contoso                                 | Responsabilità del Framework dell'interfaccia utente                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Fornire il token di accesso da Azure                    | Passa attraverso il token di accesso specificato per inizializzare i componenti        |
| Fornire la funzione Refresh                                 | Aggiornare il token di accesso usando la funzione fornita dallo sviluppatore          |
| Recuperare/passare le informazioni di join per la chiamata o la chat          | Passare attraverso le informazioni di chiamata e chat per inizializzare i componenti |
| Recuperare/passare informazioni utente per qualsiasi modello di dati personalizzato | Passare il modello di dati personalizzato ai componenti per il rendering          |
