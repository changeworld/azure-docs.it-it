---
title: Informazioni sul modo in cui vengono applicati gli sconti di prenotazione ai servizi di archiviazione di Azure | Microsoft Docs
description: Informazioni sul modo in cui vengono applicati gli sconti di capacità riservata a risorse di archiviazione BLOB, File di Azure e Azure Data Lake Storage Gen2 di Azure.
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: tamram
ms.openlocfilehash: f18097f0da658b3a485b79f5279e9ecc54a41ea0
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024031"
---
# <a name="understand-how-reservation-discounts-are-applied-to-azure-storage-services"></a>Informazioni sul modo in cui vengono applicati gli sconti di prenotazione ai servizi di archiviazione di Azure 
I servizi di archiviazione di Azure consentono di risparmiare sui costi di archiviazione riservando la capacità. Archiviazione BLOB di Azure, File di Azure e Azure Data Lake le istanze di riserva del supporto di archiviazione di generazione 2. Dopo aver acquistato la capacità riservata, lo sconto relativo alla prenotazione viene applicato automaticamente alle risorse di archiviazione che soddisfano le condizioni della prenotazione. Lo sconto per la prenotazione si applica solo alla capacità di archiviazione. La larghezza di banda e la frequenza delle richieste sono addebitate in base alle tariffe a consumo.

Per altre informazioni su archiviazione BLOB di Azure e Azure Data Lake capacità riservata di archiviazione di generazione 2, vedere [ottimizzare i costi per l'archiviazione BLOB con capacità riservata](../../storage/blobs/storage-blob-reserved-capacity.md). Per ulteriori informazioni su File di Azure capacità riservata, vedere [ottimizzare i costi per file di Azure con capacità riservata](../../storage/files/files-reserve-capacity.md).

Per informazioni sui prezzi di prenotazione dell'archiviazione BLOB di Azure, vedere prezzi di [BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs/) e [prezzi di Azure Data Lake storage generazione 2](https://azure.microsoft.com/pricing/details/storage/data-lake/). Per informazioni sui prezzi di prenotazione per l'archiviazione File di Azure, vedere [prezzi file di Azure](https://azure.microsoft.com/pricing/details/storage/files).

## <a name="how-the-reservation-discount-is-applied"></a>Come viene applicato lo sconto per la prenotazione
Lo sconto sulla capacità riservata viene applicato alle risorse di archiviazione supportate su base oraria.

Lo sconto per la capacità riservata è uno sconto "use-it-or-lose-it". Se non si dispone di BLOB in blocchi, condivisioni file di Azure o Azure Data Lake Storage Gen2 risorse che soddisfano le condizioni della prenotazione per un'ora specifica, si perde una quantità di prenotazione per quell'ora. Non è possibile riportare le ore prenotate inutilizzate.

Quando si elimina una risorsa, lo sconto per la prenotazione si applica automaticamente a un'altra risorsa idonea nell'ambito specificato. Se non si trovano risorse corrispondenti nell'ambito specificato, le ore prenotate vanno perse.

## <a name="discount-examples"></a>Esempi di sconto
Gli esempi seguenti illustrano come viene applicato lo sconto della capacità riservata, a seconda delle distribuzioni.

Si supponga di aver acquistato 100 TiB di capacità riservata nell'area Stati Uniti occidentali 2 per un periodo di 1 anno. La prenotazione è relativa all'archiviazione BLOB con ridondanza locale (con ridondanza locale) nel livello di accesso frequente.

Si supponga che il costo di questa prenotazione di esempio sia $ 18.540. È possibile scegliere di pagare la quantità totale in anticipo o di pagare rate mensili fisse di $ 1.545 per i dodici mesi successivi.

Per questi esempi si supponga di aver effettuato l'iscrizione per un piano di pagamento per una prenotazione mensile. Negli scenari seguenti viene descritto cosa accade in caso di sottoutilizzo o di uso eccessivo della capacità riservata.

### <a name="underusing-your-capacity"></a>Sottoutilizzo della capacità
Si supponga che in una determinata ora del periodo di prenotazione sia stato utilizzato solo 80 TiB della capacità riservata di 100 TiB. I rimanenti 20 TiB non vengono applicati per quell'ora e non vengono riportate.

### <a name="overusing-your-capacity"></a>Uso eccessivo della capacità
Si supponga che in una determinata ora del periodo di prenotazione sia stato usato 101 TiB di capacità di archiviazione. Lo sconto relativo alla prenotazione si applica a 100 TiB dei dati e il 1 TiB rimanente viene addebitato alle tariffe con pagamento in base al consumo per quell'ora. Se nell'ora successiva l'utilizzo passa a 100 TiB, tutti i dati di utilizzo sono coperti dalla prenotazione.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contatti
In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- [Ottimizzare i costi per l'archiviazione BLOB con capacità riservata](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Ottimizzare i costi per File di Azure con capacità riservata](../../storage/files/files-reserve-capacity.md)
- [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)
