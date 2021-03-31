---
title: Informazioni sulle funzionalità del glossario aziendale in Azure (anteprima)
description: Questo articolo illustra il glossario aziendale in Azure.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 8b391438d8d6605e7ef493a6552af634db840ad5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555160"
---
# <a name="understand-business-glossary-features-in-azure-purview"></a>Informazioni sulle funzionalità del glossario aziendale in Azure

Questo articolo fornisce una panoramica della funzionalità glossario aziendale in Azure. 

## <a name="business-glossary"></a>Glossario aziendale

Un glossario fornisce il vocabolario per gli utenti aziendali.  È costituito da termini aziendali che possono essere correlati tra loro e consentirne la categorizzazione in modo che possano essere riconosciuti in contesti diversi. È possibile eseguire il mapping di questi termini a asset come un database, tabelle, colonne e così via. Questo consente di astrarre il gergo tecnico associato ai repository di dati e consente all'utente aziendale di individuare e usare i dati nel vocabolario che è più familiare.


Un glossario aziendale è una raccolta di termini. Ogni termine rappresenta un oggetto in un'organizzazione ed è molto probabile che esistano più termini che rappresentano lo stesso oggetto. Un cliente può anche essere definito client, acquirente o acquirente. Questi più termini hanno una relazione tra loro. La relazione tra questi termini può essere una delle seguenti:

- Sinonimi-termini diversi con la stessa definizione
- correlato: nome diverso con una definizione simile

Lo stesso termine può implicare anche più oggetti business. È importante che ogni termine sia ben definito e chiaramente comprensibile all'interno dell'organizzazione.

## <a name="custom-attributes"></a>Attributi personalizzati

Azure competenza supporta otto attributi predefiniti per qualsiasi termine del glossario aziendale:
- Nome
- Definizione
- Amministratori dei dati
- Esperti di dati
- Acronimo
- Sinonimi
- Termini correlati
- Risorse

Questi attributi non possono essere modificati o eliminati. Tuttavia, questi attributi non sono sufficienti per definire completamente un termine in un'organizzazione. Per risolvere questo problema, l'ambito fornisce una funzionalità in cui è possibile definire attributi personalizzati per il glossario.

## <a name="term-templates"></a>Modelli di termini

Il termine modelli fornisce attributi personalizzati del glossario che possono essere raggruppati logicamente in catalogo. La funzionalità consente di raggruppare tutti gli attributi personalizzati pertinenti insieme in un modello e quindi di applicare il modello durante la creazione del termine del glossario. Ad esempio, tutti gli attributi personalizzati correlati al Finance, come centro di costo, centro di profitto, codice contabilità, possono essere raggruppati in un modello di modello Finance e il modello Finance può essere usato per creare termini di glossario finanziari.

Tutti gli attributi standard sono raggruppati in un modello predefinito di sistema. Qualsiasi modello di termine creato conterrà questi attributi insieme a eventuali attributi personalizzati aggiuntivi creati come parte del processo di creazione del modello.

## <a name="glossary-vs-classification-vs-sensitivity-labels"></a>Etichette di distinzione tra Glossario e classificazione di Visual Studio

Anche se i termini di glossario, le classificazioni e le etichette sono annotazioni in un asset di dati, ognuna di esse ha un significato diverso nel contesto di Catalog. 

### <a name="glossary"></a>Glossario

Come indicato in precedenza, il termine glossario aziendale definisce il vocabolario aziendale per un'organizzazione e contribuisce a colmare il divario tra i vari reparti dell'azienda.

### <a name="classifications"></a>Classificazioni

Le classificazioni sono annotazioni che possono essere assegnate alle entità. La flessibilità delle classificazioni consente di utilizzarle per diversi scenari, ad esempio:

- informazioni sulla natura dei dati archiviati negli asset di dati
- definizione di criteri di controllo di accesso

La competenza ha oggi più di 100 classificatori di sistema ed è possibile definire i propri classificatori in Catalog. Come parte del processo di analisi, le classificazioni vengono automaticamente rilevate e applicate agli asset e agli schemi di dati. Tuttavia, è possibile eseguirne l'override in qualsiasi momento. Le sostituzioni umane non vengono mai sostituite dalle analisi automatiche.

### <a name="sensitivity-labels"></a>Etichette di riservatezza

Le etichette di riservatezza sono un tipo di annotazione che consente di classificare e proteggere i dati dell'organizzazione, senza compromettere la produttività e la collaborazione. Le etichette di riservatezza vengono utilizzate per identificare le categorie di tipi di classificazione nei dati aziendali e raggruppare i criteri che si desidera applicare a ogni categoria. Il livello di competenza usa gli stessi tipi di informazioni riservate Microsoft 365, che consente di estendere i criteri di sicurezza e la protezione esistenti nell'intero contenuto e nell'intero patrimonio di dati. Le stesse etichette possono essere condivise tra Microsoft Office prodotti e gli asset di dati in ambito di competenza.

## <a name="next-steps"></a>Passaggi successivi

- [Gestisci modelli di termini](how-to-manage-term-templates.md)
- [Esplorare il Catalogo dati in Azure](how-to-browse-catalog.md)
