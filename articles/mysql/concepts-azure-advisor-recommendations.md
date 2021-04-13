---
title: Azure Advisor per MySQL
description: Informazioni sulle raccomandazioni Azure Advisor per MySQL.
author: alau-ms
ms.author: alau
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4b3d750c930defbfcb7db6d4d67210e001e3ce8b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315561"
---
# <a name="azure-advisor-for-mysql"></a>Azure Advisor per MySQL
Informazioni su come Azure Advisor viene applicato al database di Azure per MySQL e ottenere risposte alle domande più comuni.
## <a name="what-is-azure-advisor-for-mysql"></a>Che cos'è Azure Advisor per MySQL?
Il sistema Azure Advisor usa i dati di telemetria per emettere consigli sulle prestazioni e sull'affidabilità per il database MySQL. Le raccomandazioni di Advisor sono divise tra le offerte di database MySQL:
* Database di Azure per MySQL - Server singolo
* Database di Azure per MySQL-server flessibile

Alcune raccomandazioni sono comuni a più offerte di prodotti, mentre altre indicazioni sono basate su ottimizzazioni specifiche del prodotto.
## <a name="where-can-i-view-my-recommendations"></a>Dove è possibile visualizzare i consigli?
Le raccomandazioni sono disponibili nella barra laterale **Panoramica** di esplorazione della portale di Azure. Verrà visualizzata un'anteprima come notifica del banner e i dettagli possono essere visualizzati nella sezione **notifiche** situata immediatamente sotto i grafici di utilizzo delle risorse.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Screenshot del portale di Azure che mostra un Azure Advisor raccomandazione.":::

## <a name="recommendation-types"></a>Tipi di raccomandazione
Database di Azure per MySQL assegna priorità ai tipi di raccomandazioni seguenti:
* **Prestazioni**: per migliorare la velocità del server MySQL. Sono inclusi l'utilizzo della CPU, l'utilizzo della memoria, il pool di connessioni, l'utilizzo del disco e i parametri del server specifici del prodotto. Per altre informazioni, vedere [Advisor Performance recommendations](../advisor/advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni).
* **Affidabilità**: per garantire e migliorare la continuità dei database cruciali per l'azienda. Sono incluse le indicazioni relative al limite di archiviazione e al limite di connessione. Per ulteriori informazioni, vedere [consigli sull'affidabilità di Advisor](../advisor/advisor-high-availability-recommendations.md).
* **Costo**: per ottimizzare e ridurre la spesa complessiva di Azure. Sono incluse le indicazioni sul dimensionamento a destra del server. Per altre informazioni, vedere [Advisor Cost recommendations](../advisor/advisor-cost-recommendations.md) (Consigli di Azure sui costi).

## <a name="understanding-your-recommendations"></a>Informazioni sulle raccomandazioni
* **Pianificazione giornaliera**: per i database MySQL di Azure, vengono verificati i dati di telemetria del server e vengono forniti consigli su una pianificazione giornaliera. Se si modifica la configurazione del server, le raccomandazioni esistenti rimarranno visibili fino a quando non si riesaminano i dati di telemetria nel giorno successivo. 
* **Cronologia delle prestazioni**: alcune raccomandazioni sono basate sulla cronologia delle prestazioni. Queste indicazioni vengono visualizzate solo dopo che un server è stato in funzione con la stessa configurazione per 7 giorni. In questo modo è possibile rilevare modelli di utilizzo intensivo (ad esempio, un'attività CPU elevata o un volume di connessione elevato) in un periodo di tempo prolungato. Se si effettua il provisioning di un nuovo server o si passa a una nuova configurazione di vCore, queste raccomandazioni verranno sospese temporaneamente. In questo modo si evita che i dati di telemetria legacy attivino raccomandazioni in un server appena riconfigurato. Tuttavia, ciò significa anche che le raccomandazioni basate sulla cronologia delle prestazioni potrebbero non essere identificate immediatamente.

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere [Azure Advisor Overview](../advisor/advisor-overview.md).
