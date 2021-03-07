---
title: Come impedire configurazioni non configurate con il Centro sicurezza di Azure
description: Informazioni su come usare le opzioni ' enforce ' è Deny ' del Centro sicurezza nelle pagine dei dettagli delle raccomandazioni
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 5d172a73674195e7f64f5ef02322e2bd2d6314df
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439528"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Impedire gli errori di configurazione con i consigli di tipo Imponi/Nega

Gli errori di configurazione della sicurezza sono una delle cause principali degli eventi imprevisti di sicurezza. Il Centro sicurezza consente ora di contribuire a *evitare* gli errori di configurazione delle nuove risorse per quanto riguarda raccomandazioni specifiche. 

Questa funzionalità può contribuire alla protezione dei carichi di lavoro e alla stabilizzazione del punteggio di sicurezza.

È possibile imporre una configurazione di sicurezza, basata su una raccomandazione specifica, in due modi:

- L'effetto **Deny** di Criteri di Azure consente di interrompere la creazione di risorse non integre
- L'opzione **Imponi** consente di sfruttare i vantaggi dell'effetto **DeployIfNotExist** di Criteri di Azure e di correggere automaticamente eventuali risorse non conformi al momento della creazione

Si trova nella parte superiore della pagina dei dettagli della risorsa per le raccomandazioni di sicurezza selezionate (vedere le [raccomandazioni con le opzioni di negazione/applicazione](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Impedisci la creazione di risorse

1. Aprire la raccomandazione che le nuove risorse devono soddisfare e selezionare il pulsante **Nega** nella parte superiore della pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Pagina di raccomandazione con pulsante nega evidenziato":::

    Viene visualizzato il riquadro configurazione che elenca le opzioni di ambito. 

1. Impostare l'ambito selezionando la sottoscrizione o il gruppo di gestione pertinente.

    > [!TIP]
    > È possibile utilizzare i tre puntini alla fine della riga per modificare una singola sottoscrizione oppure utilizzare le caselle di controllo per selezionare più sottoscrizioni o gruppi, quindi selezionare **modifica per nega**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Impostazione dell'ambito per Deny di criteri di Azure":::


## <a name="enforce-a-secure-configuration"></a>Applicare una configurazione sicura

1. Aprire la raccomandazione per la distribuzione di un modello se le nuove risorse non soddisfano le esigenze e selezionare il pulsante **applica** nella parte superiore della pagina.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Pagina di raccomandazione con il pulsante Applica evidenziato":::

    Viene visualizzato il riquadro Configurazione con tutte le opzioni di configurazione dei criteri. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Imponi opzioni di configurazione":::

1. Impostare l'ambito, il nome dell'assegnazione e altre opzioni rilevanti.

1. Selezionare **Rivedi e crea**.

## <a name="recommendations-with-denyenforce-options"></a>Raccomandazioni con opzioni Deny/enforce

Questi consigli possono essere usati con l'opzione **Deny** :

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

Questi consigli possono essere usati con l'opzione **Imponi** :

- È consigliabile abilitare il controllo in SQL Server
- La soluzione Backup di Azure deve essere abilitata per le macchine virtuali
- Azure Defender per SQL deve essere abilitato nei server SQL
- I log di diagnostica devono essere abilitati in Analisi di flusso di Azure
- È consigliabile abilitare i log di diagnostica negli account Batch
- È consigliabile abilitare i log di diagnostica Data Lake Analytics
- È consigliabile abilitare i log di diagnostica in Hub eventi
- I log di diagnostica in Key Vault devono essere abilitati
- È consigliabile abilitare i log di diagnostica in App per la logica
- È consigliabile abilitare i log di diagnostica nei servizi di ricerca
- È consigliabile abilitare i log di diagnostica nel bus di servizio
