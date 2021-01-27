---
title: Informazioni sugli sconti per le prenotazioni per Azure Data Factory flussi di dati | Microsoft Docs
description: Informazioni sul modo in cui viene applicato uno sconto per la prenotazione ai flussi di dati di ADF. Lo sconto viene applicato a questi flussi di dati su base oraria.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: makromer
ms.openlocfilehash: 6936b9344196f8071a6c1859869c62e5bee22924
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811709"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Come viene applicato uno sconto di prenotazione per Azure Data Factory flussi di dati

Dopo aver acquistato la capacità riservata del flusso di dati ADF, lo sconto relativo alla prenotazione viene applicato automaticamente ai flussi di dati tramite un runtime di integrazione di Azure che corrisponde al tipo di calcolo e al numero di core della prenotazione.

## <a name="how-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione

Lo sconto per la prenotazione si basa sul principio "*use-it-or-lose-it*", ossia se non si usa si perde. Quindi, se non sono disponibili risorse di integrazione di Azure corrispondenti per ogni ora, si perderà la quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Quando si interrompe l'uso di Integration Runtime per i flussi di dati, lo sconto della prenotazione si applica automaticamente a un'altra risorsa corrispondente nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno *perse*.

## <a name="discount-applied-to-adf-data-flows"></a>Sconto applicato ai flussi di dati di ADF

Lo sconto di capacità riservata flusso di dati ADF viene applicato all'esecuzione di runtime di integrazione su base oraria. La prenotazione acquistata viene confrontata con l'utilizzo di calcolo generato dal runtime di integrazione usato. Per i flussi di dati che non vengono eseguiti nell'ora completa, la prenotazione viene applicata automaticamente ad altri flussi di dati corrispondenti agli attributi di prenotazione. Lo sconto può essere applicato ai flussi di dati in esecuzione simultanea. Se non si dispone di flussi di dati che vengono eseguiti per l'ora intera che corrispondono agli attributi di prenotazione, non si ottiene il vantaggio completo dello sconto relativo alla prenotazione per quell'ora.

Gli esempi seguenti illustrano il modo in cui viene applicato lo sconto relativo alla capacità riservata del flusso di dati ADF a seconda del numero di core acquistati e del momento in cui sono in esecuzione.

- Scenario 1: si acquista una capacità riservata del flusso di dati ADF per 80 core di calcolo con ottimizzazione per la memoria. Si esegue un flusso di dati con un runtime di integrazione di Azure impostato su 144 Core di memoria con ottimizzazione per un'ora. Viene addebitato il prezzo con pagamento in base al consumo per 64 core di utilizzo del flusso di dati per un'ora. Si ottiene lo sconto di prenotazione per un'ora di 80 core di utilizzo con ottimizzazione per la memoria.
- Scenario 2: si acquista una capacità riservata del flusso di dati ADF per 32 core di calcolo per utilizzo generico. È possibile eseguire il debug dei flussi di dati per 1 ora usando i core 32 del runtime di integrazione di calcolo di Azure generale. Si ottiene lo sconto relativo alla prenotazione per l'intera ora di utilizzo.

Per informazioni sull'applicazione delle prenotazioni di Azure nei report sull'utilizzo per la fatturazione, vedere [Informazioni sull'utilizzo delle prenotazioni di Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle prenotazioni di Azure, vedere l'articolo seguente:

- [Informazioni sulle prenotazioni di Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)
