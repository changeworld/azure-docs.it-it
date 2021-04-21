---
title: Percorsi dei dati per Desktop virtuale Windows - Azure
description: Breve panoramica delle posizioni in cui sono archiviati i dati e i metadati di Desktop virtuale Windows.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 02/17/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: eeba3cb579c6ef9158379403a3206f99a2cfb060
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830629"
---
# <a name="data-and-metadata-locations-for-windows-virtual-desktop"></a>Percorsi dei dati e dei metadati per Desktop virtuale Windows

>[!IMPORTANT]
>Questo contenuto si applica a Desktop virtuale Windows con gli oggetti Azure Resource Manager di Desktop virtuale Windows. Se si usa Desktop virtuale Windows (versione classica) senza gli oggetti Azure Resource Manager, vedere [questo articolo](./virtual-desktop-fall-2019/data-locations-2019.md).

Desktop virtuale Windows è attualmente disponibile per tutte le posizioni geografiche. Gli amministratori possono scegliere la posizione in cui archiviare i dati utente quando creano le macchine virtuali del pool di host e i servizi associati, ad esempio i file server. Per altre informazioni sulle aree geografiche di Azure, vedere la mappa [del data center di Azure.](https://azuredatacentermap.azurewebsites.net/)

>[!NOTE]
>Microsoft non controlla o limita le aree in cui l'utente o gli utenti possono accedere ai dati specifici dell'utente e dell'app.

>[!IMPORTANT]
>Desktop virtuale Windows archivia informazioni sui metadati globali, ad esempio i nomi delle aree di lavoro, i nomi dei pool host, i nomi dei gruppi di app e i nomi delle entità utente in un data center. Ogni volta che un cliente crea un oggetto servizio, deve immettere una posizione per l'oggetto servizio. La posizione immessa determina dove verranno archiviati i metadati per l'oggetto. Il cliente sceglierà un'area di Azure e i metadati verranno archiviati nell'area geografica correlata. Per un elenco di tutte le aree geografiche di Azure e le aree geografiche correlate, vedere [Aree geografiche di Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

Attualmente è possibile archiviare i metadati nelle aree geografiche seguenti:

- Stati Uniti (Stati Uniti) (disponibile a livello generale)
- Europa (UE) (anteprima pubblica) 

>[!NOTE]
> Quando si seleziona un'area in cui creare oggetti del servizio Desktop virtuale Windows, verranno visualizzati le aree geografiche degli Stati Uniti e dell'Unione Europea. Per assicurarsi di comprendere l'area più adatta per la distribuzione, vedere la mappa [dell'infrastruttura globale di Azure.](https://azure.microsoft.com/global-infrastructure/geographies/#geographies)

I metadati archiviati vengono crittografati in stato di inquieto e i mirror con ridondanza geografica vengono mantenuti all'interno dell'area geografica. Tutti i dati dei clienti, ad esempio le impostazioni dell'app e i dati utente, risiedono nella posizione scelta dal cliente e non vengono gestiti dal servizio. Man mano che il servizio aumenta, saranno disponibili più aree geografiche.

I metadati del servizio vengono replicati all'interno dell'area geografica di Azure per il ripristino di emergenza.
