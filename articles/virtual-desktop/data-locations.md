---
title: Percorsi dati per desktop virtuale Windows-Azure
description: Breve panoramica delle posizioni in cui vengono archiviati i metadati e i dati del desktop virtuale di Windows.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 12ec71a86a5df5954c14097e6a0ec5c8a5138fc5
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652429"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Percorsi di dati e metadati per desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/data-locations-2019.md).

Desktop virtuale di Windows è attualmente disponibile per tutte le posizioni geografiche. Gli amministratori possono scegliere il percorso in cui archiviare i dati utente quando creano le macchine virtuali del pool host e i servizi associati, ad esempio i file server. Scopri di più sulle aree geografiche di Azure nella [mappa del Data Center di Azure](https://azuredatacentermap.azurewebsites.net/).

>[!NOTE]
>Microsoft non controlla né limita le aree in cui l'utente o gli utenti possono accedere ai dati specifici dell'utente e dell'app.

>[!IMPORTANT]
>Desktop virtuale di Windows archivia informazioni sui metadati globali, ad esempio nomi di tenant, nomi dei pool di host, nomi dei gruppi di app e nomi dell'entità utente in un Data Center. Ogni volta che un cliente crea un oggetto servizio, deve immettere un percorso per l'oggetto servizio. Il percorso immesso determina la posizione in cui verranno archiviati i metadati per l'oggetto. Il cliente sceglie un'area di Azure e i metadati verranno archiviati nell'area geografica correlata. Per un elenco di tutte le aree di Azure e le aree geografiche correlate, vedere [geografie di Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

Attualmente è supportata l'archiviazione dei metadati nelle seguenti aree geografiche:

- Stati Uniti (US) (disponibile a livello generale)
- Europa (UE) (anteprima pubblica) 

>[!NOTE]
> Quando si seleziona un'area in cui creare oggetti servizio desktop virtuale Windows, verranno visualizzate le aree sotto le aree geografiche US e UE. Per assicurarsi di comprendere quale area funziona meglio per la distribuzione, vedere la [mappa dell'infrastruttura globale di Azure](https://azure.microsoft.com/global-infrastructure/geographies/#geographies).

I metadati archiviati sono crittografati e i mirror con ridondanza geografica vengono mantenuti all'interno dell'area geografica. Tutti i dati dei clienti, ad esempio le impostazioni dell'app e i dati utente, si trovano nella posizione scelta dal cliente e non sono gestiti dal servizio. Più aree geografiche diventeranno disponibili man mano che il servizio cresce.

I metadati del servizio vengono replicati all'interno dell'area geografica di Azure a scopo di ripristino di emergenza.