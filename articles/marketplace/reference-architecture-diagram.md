---
title: Diagramma dell'architettura di riferimento | Azure Marketplace
description: Come creare un diagramma dell'architettura di riferimento per un'offerta in Microsoft Commercial Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 66e4d498ff7584188d680e35c89c6f10cc43c9cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104604492"
---
# <a name="reference-architecture-diagram"></a>Diagramma dell'architettura di riferimento

Il diagramma dell'architettura di riferimento è un modello che rappresenta l'infrastruttura su cui si basa l'offerta. Per le soluzioni IP di Azure, il diagramma dovrebbe mostrare anche il modo in cui l'offerta USA i servizi cloud di Microsoft in base ai requisiti tecnici della co-selling IP. Non è progettato per valutare la qualità dell'architettura. È progettato per illustrare il modo in cui la soluzione USA i servizi Microsoft.

Il diagramma dell'architettura di riferimento può essere creato tramite più strumenti. Si consiglia Microsoft Visio, in quanto dispone di più stencil che illustrano i modelli di architettura di Azure.

Un punto di partenza utile per la creazione di diagrammi di architettura di riferimento è quello di sfruttare i [modelli di architettura di Azure](/azure/architecture/browse/).

## <a name="typical-components-of-a-reference-architecture-diagram"></a>Componenti tipici di un diagramma dell'architettura di riferimento

- Servizi cloud che ospitano e interagiscono con l'offerta, inclusi quelli che utilizzano le risorse di Azure
- Connessioni dati, livelli di dati e servizi dati usati dall'offerta
- Servizi cloud usati per controllare la sicurezza, l'autenticazione e gli utenti dell'offerta
- Interfacce utente e altri servizi che espongono l'offerta agli utenti
- Connettività e integrazioni ibride o locali o una combinazione di entrambi

Questa schermata mostra un esempio di diagramma dell'architettura di riferimento.

[![Questa immagine è un diagramma dell'architettura di co-selling di esempio.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

Questo diagramma dell'architettura di riferimento di esempio è per un chatbot di settore verticale che può essere integrato con i siti Intranet per semplificare gli scenari di richiesta di previsione tramite un algoritmo di machine learning. Questa soluzione USA i dati di pianificazione della supply chain e della produzione da diversi sistemi ERP. Il bot è progettato per risolvere le domande relative al momento in cui un venditore può eseguire il commit delle date di consegna possibili per un ordine.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare la co-selling per un'offerta di Marketplace commerciale](commercial-marketplace-co-sell.md)
