---
title: includere file
description: includere file
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 03/04/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2459cf6c5055dcde83dccf37addc160aeeaa64ad
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198660"
---
I domini eventi si possono comprendere più facilmente con un esempio. Supponiamo di eseguire contoso Operating Machinery, dove Produci i trattori, le attrezzature di scavo e altri macchinari pesanti. Quando si gestisce un'azienda, si esegue il push verso i clienti di informazioni in tempo reale riguardanti la manutenzione delle apparecchiature, l'integrità dei sistemi e gli aggiornamenti dei contratti. Tutte queste informazioni vengono inoltrate a vari endpoint, tra cui l'app, gli endpoint dei clienti e altre infrastrutture configurate dai clienti.

I domini eventi consentono di modellare Contoso Construction Machinery come una singola entità di gestione degli eventi. Ogni cliente viene rappresentato come un argomento all'interno del dominio. L'autenticazione e l'autorizzazione vengono gestite mediante Azure Active Directory. Ogni cliente può sottoscrivere il proprio argomento e ricevere i relativi eventi. L'accesso gestito tramite il dominio eventi garantisce che il cliente possa accedere solo al proprio argomento.

Offre inoltre un singolo endpoint, in cui è possibile pubblicare tutti gli eventi dei clienti. Griglia di eventi assicurerà che ogni argomento venga a conoscenza solo degli eventi con ambito a livello del proprio tenant.

:::image type="content" source="./media/event-grid-domain-example-use-case/contoso-construction-example.png" alt-text="Esempio di Contoso Construction" lightbox="./media/event-grid-domain-example-use-case/contoso-construction-example.png":::