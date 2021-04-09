---
title: includere file
description: includere file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6d06a46d2eaaad362890f1e3e44dbc746fa10898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98633455"
---
File di Azure offre quattro diversi livelli di archiviazione, ovvero Premium, Ottimizzato per le transazioni, Accesso frequente e Accesso sporadico, che consentono di adattare le condivisioni ai requisiti dello scenario in termini di prestazioni e prezzi:

- **Premium**: Le condivisioni file Premium sono basate su unità SSD e offrono prestazioni elevate omogenee e bassa latenza, nell'ordine di pochi millisecondi per la maggior parte delle operazioni di I/O, per i carichi di lavoro con I/O elevato. Le condivisioni file Premium sono adatte per una vasta gamma di carichi di lavoro, ad esempio database, hosting di siti Web e ambienti di sviluppo. Le condivisioni file Premium possono essere usate con entrambi i protocolli SMB (Server Message Block) e NFS (Network File System).
- **Ottimizzato per le transazioni**: le condivisioni file ottimizzate per le transazioni consentono di gestire carichi di lavoro con un numero elevato di transazioni che non necessitano della latenza offerta dalle condivisioni file Premium. Le condivisioni file ottimizzate per le transazioni sono disponibili nell'hardware di archiviazione standard basato su unità disco rigido (HDD). Questo livello di archiviazione ottimizzato per le transazioni viene in genere definito "standard", ma in realtà tale aggettivo si riferisce al tipo di supporto di archiviazione e non al livello. Di fatto anche il livello ad accesso frequente e quello ad accesso sporadico sono "standard", perché usano hardware di archiviazione standard.
- **Hot**: le condivisioni file ad accesso frequente offrono archiviazione ottimizzata per scenari di condivisione di file per utilizzo generico, ad esempio condivisioni di team. Le condivisioni file ad accesso frequente sono disponibili nell'hardware di archiviazione standard basato su unità disco rigido (HDD).
- **Cool**: le condivisioni file ad accesso sporadico offrono un'archiviazione conveniente ottimizzata per scenari di archiviazione online. Le condivisioni file ad accesso sporadico sono disponibili nell'hardware di archiviazione standard basato su unità disco rigido (HDD).

Le condivisioni file Premium vengono distribuite nel tipo di **account di archiviazione FileStorage** e sono disponibili solo in un modello di fatturazione con provisioning. Per altre informazioni sul modello di fatturazione con provisioning per le condivisioni file Premium, vedere [Informazioni sul provisioning per le condivisioni file Premium](../articles/storage/files/understanding-billing.md#provisioned-model). Le condivisioni file standard, ovvero quelle ottimizzate per le transazioni, ad accesso frequente e ad accesso sporadico, vengono distribuite nel tipo di **account di archiviazione per utilizzo generico versione 2 (GPv2)** e prevedono la fatturazione con pagamento in base al consumo. Le condivisioni file ad accesso frequente e ad accesso sporadico sono disponibili in tutte le aree pubbliche di Azure e nelle aree di Azure per enti pubblici. Le condivisioni file ottimizzate per le transazioni sono disponibili in tutte le aree di Azure, incluse le aree di Azure Cina e Azure Germania.

Prima di scegliere un livello di archiviazione per il carico di lavoro, considerare i propri requisiti in termini di prestazioni e utilizzo. Se il carico di lavoro richiede una latenza minima o se si usa un supporto di archiviazione SSD locale, il livello Premium è probabilmente quello più adatto. Se la bassa latenza non rappresenta un problema, come nel caso delle condivisioni di team montate in locale da Azure o memorizzate nella cache locale con Sincronizzazione file di Azure, l'archiviazione standard può essere una soluzione migliore dal punto di vista dei costi.

Dopo aver creato una condivisione file in un account di archiviazione, non è possibile spostarla in livelli che appartengono esclusivamente a tipi di account di archiviazione diversi. Ad esempio, per spostare una condivisione file ottimizzata per le transazioni nel livello Premium, è necessario creare una nuova condivisione file in un account di archiviazione FileStorage e copiare i dati dalla condivisione originale in una nuova condivisione file nell'account FileStorage. È consigliabile usare AzCopy per copiare dati tra condivisioni file di Azure, ma si possono usare anche strumenti come `robocopy` in Windows o `rsync` per macOS e Linux. 

Le condivisioni file distribuite negli account di archiviazione GPv2 possono essere spostate tra i livelli standard (ottimizzato per le transazioni, ad accesso frequente e ad accesso sporadico) senza creare un nuovo account di archiviazione ed eseguire la migrazione dei dati, ma il passaggio a un altro livello comporta dei costi di transazione. Quando si sposta una condivisione da un livello ad accesso più frequente a un livello ad accesso più sporadico, viene addebitato il costo della transazione di scrittura del livello ad accesso più sporadico per ogni file nella condivisione. Se si sposta una condivisione file da un livello ad accesso più sporadico a un livello ad accesso più frequente, viene addebitato il costo della transazione di lettura del livello ad accesso sporadico per ogni file nella condivisione.

Per altre informazioni, vedere [Informazioni sulla fatturazione di File di Azure](../articles/storage/files/understanding-billing.md).