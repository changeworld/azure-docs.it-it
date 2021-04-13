---
title: Azure Advisor per MariaDB
description: Informazioni sulle Azure Advisor per MariaDB.
author: alau-ms
ms.author: alau
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 60a0a5763085de38dbfd4dabcb65dc24b299b29a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368651"
---
# <a name="azure-advisor-for-mariadb"></a>Azure Advisor per MariaDB
Informazioni su come Azure Advisor viene applicato a Database di Azure per MariaDB e ottenere risposte alle domande comuni.
## <a name="what-is-azure-advisor-for-mariadb"></a>Che cos'Azure Advisor per MariaDB?
Il Azure Advisor usa i dati di telemetria per emettere raccomandazioni su prestazioni e affidabilità per il database MariaDB. 

Alcune raccomandazioni sono comuni a più offerte di prodotti, mentre altre sono basate su ottimizzazioni specifiche del prodotto.
## <a name="where-can-i-view-my-recommendations"></a>Dove è possibile visualizzare le raccomandazioni?
Le raccomandazioni sono disponibili **nella barra** laterale di spostamento Panoramica nella portale di Azure. Verrà visualizzata un'anteprima come notifica banner e  i dettagli possono essere visualizzati nella sezione Notifiche che si trova sotto i grafici sull'utilizzo delle risorse.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Screenshot dell'portale di Azure che mostra una Azure Advisor raccomandazione.":::

## <a name="recommendation-types"></a>Tipi di raccomandazione
Database di Azure per MariaDB assegna la priorità ai tipi di raccomandazioni seguenti:
* **Prestazioni:** per migliorare la velocità del server MariaDB. Sono inclusi l'utilizzo della CPU, l'utilizzo elevato di memoria, l'utilizzo del disco e i parametri del server specifici del prodotto. Per altre informazioni, vedere [Advisor Performance recommendations](../advisor/advisor-performance-recommendations.md) (Consigli di Advisor sulle prestazioni).
* **Affidabilità:** per garantire e migliorare la continuità dei database business critical. Sono incluse le raccomandazioni relative al limite di archiviazione e al limite di connessione. Per altre informazioni, vedere Advisor [Reliability recommendations](../advisor/advisor-high-availability-recommendations.md).
* **Costo:** per ottimizzare e ridurre la spesa complessiva di Azure. Sono incluse le raccomandazioni per il ridimensionamento del server. Per altre informazioni, vedere [Advisor Cost recommendations](../advisor/advisor-cost-recommendations.md) (Consigli di Azure sui costi).

## <a name="understanding-your-recommendations"></a>Informazioni sulle raccomandazioni
* **Pianificazione giornaliera:** per i database MariaDB di Azure, si controllano i dati di telemetria del server e si emettere raccomandazioni in base a una pianificazione giornaliera. Se si apporta una modifica alla configurazione del server, le raccomandazioni esistenti rimarranno visibili fino a quando i dati di telemetria non verranno nuovamente esaminati il giorno successivo. 
* **Cronologia delle prestazioni:** alcune raccomandazioni sono basate sulla cronologia delle prestazioni. Queste raccomandazioni verranno visualizzate solo dopo che un server ha utilizzato la stessa configurazione per 7 giorni. Ciò consente di rilevare modelli di utilizzo elevato ,ad esempio un'elevata attività della CPU o un volume di connessione elevato, in un periodo di tempo lungo. Se si effettua il provisioning di un nuovo server o si passa a una nuova configurazione vCore, questi consigli verranno sospesi temporaneamente. Ciò impedisce ai dati di telemetria legacy di attivare raccomandazioni in un server appena riconfigurato. Ciò significa anche che le raccomandazioni basate sulla cronologia delle prestazioni potrebbero non essere identificate immediatamente.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere Azure Advisor [Panoramica di](../advisor/advisor-overview.md).
