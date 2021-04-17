---
title: Ripristinare il database Oracle nell'infrastruttura BareMetal di Azure
description: Informazioni su come ripristinare il database Oracle nell'infrastruttura BareMetal di Azure.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: fc7464474d01314a52a77e0f28b1df160a9f42a0
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590275"
---
# <a name="recover-your-oracle-database-on-azure-baremetal-infrastructure"></a>Ripristinare il database Oracle nell'infrastruttura BareMetal di Azure

Anche se nessuna singola tecnologia protegge da tutti gli scenari di errore, la combinazione di funzionalità offre agli amministratori di database la possibilità di ripristinare il database in quasi tutte le situazioni.

## <a name="causes-of-database-failure"></a>Cause dell'errore del database

Gli errori del database possono verificarsi per molti motivi, ma in genere rientrano in diverse categorie:

- Errori di manipolazione dei dati.
- Perdita di log di ripristina online.
- Perdita di file di controllo del database.
- Perdita di file di dati di database.
- Danneggiamento dei dati fisici.

## <a name="choose-your-method-of-recovery"></a>Scegliere il metodo di ripristino

Il tipo di ripristino dipende dal tipo di errore. Si supponiamo che un oggetto sia stato eliminato o che i dati non vengono modificati in modo errato. La soluzione più rapida consiste in genere nell'eseguire un'operazione di database flashback. In altri casi, il ripristino tramite uno snapshot Azure NetApp Files può fornire il ripristino desiderato. L'albero delle decisioni della figura seguente rappresenta scenari comuni di errore e ripristino se vengono implementate tutte le opzioni di protezione dei dati descritte in precedenza.

:::image type="content" source="media/oracle-high-availability/db-recovery-decision-tree.png" alt-text="Diagramma dell'albero delle decisioni di ripristino del database." lightbox="media/oracle-high-availability/db-recovery-decision-tree.png":::

Tenere presente che questo albero delle decisioni di esempio viene visualizzato solo dall'obiettivo di un amministratore del database. Ogni distribuzione può avere requisiti diversi che potrebbero modificare l'ordine delle scelte. Ad esempio, l'esecuzione di un passaggio del ruolo del database a un'area diversa tramite Data Guard può avere un effetto negativo sulle prestazioni dell'applicazione. Potrebbe assegnare al metodo di ripristino dello snapshot un RTO inferiore. Per garantire che i requisiti RTO/RPO siano soddisfatti, è consigliabile eseguire queste operazioni e creare procedure documentate per eseguirle quando necessario.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'infrastruttura BareMetal:

- [Che cos'è l'infrastruttura BareMetal in Azure?](../../concepts-baremetal-infrastructure-overview.md)
- [Connettere istanze dell'infrastruttura BareMetal in Azure](../../connect-baremetal-infrastructure.md)
