---
title: Criteri di suddivisione in livelli cloud Sincronizzazione file di Azure | Microsoft Docs
description: Informazioni dettagliate sul modo in cui i criteri di spazio libero di data e volume interagiscono per diversi scenari.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3020737e91e1fe5c4af3e23a147fa0ea16037d3b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204358"
---
# <a name="cloud-tiering-policies"></a>Criteri di suddivisione in livelli cloud

La suddivisione in livelli cloud prevede due criteri che determinano quali file sono suddivisi in livelli nel cloud, ovvero i **criteri di spazio libero del volume** e i **criteri di data**.

Il **criterio di spazio disponibile del volume** assicura che una percentuale specificata del volume locale in cui si trova l'endpoint server sia sempre disponibile. 

Data e ora dell'ultimo accesso ai file dei livelli di **criteri della data** x giorni fa o versione successiva. I criteri dello spazio disponibile del volume avranno sempre la precedenza. Quando lo spazio disponibile nel volume non è sufficiente per archiviare il numero di giorni in cui i file sono descritti in base ai criteri relativi alla data, Sincronizzazione file di Azure sostituirà i criteri relativi alla data e continuerà a livelli i file più freddi fino a quando non viene soddisfatta la percentuale di spazio disponibile nel volume.

## <a name="how-both-policies-work-together"></a>Interazione tra entrambi i criteri

Verrà usato un esempio per illustrare il funzionamento di questi criteri: si configurano Sincronizzazione file di Azure in un volume locale da 500 GB e la suddivisione in livelli nel cloud non è mai stata abilitata. Questi sono i file nella condivisione file:

|File Name |Ora ultimo accesso  |Dimensioni file  |Archiviato in |
|----------|------------------|-----------|----------|
|File 1    | 2 giorni fa  | 10 GB | Server e condivisione file di Azure
|File 2    | 10 giorni fa | 30 GB | Server e condivisione file di Azure
|File 3    | 1 anno fa | 200 GB | Server e condivisione file di Azure
|File 4    | 1 anno, 2 giorni fa | 130 GB | Server e condivisione file di Azure
|File 5    | 2 anni, 1 giorno fa | 140 GB | Server e condivisione file di Azure

**Modifica 1:** È stata abilitata la suddivisione in livelli nel cloud, è stato impostato un criterio di spazio disponibile del volume del 20% e il criterio di data è stato disabilitato Con questa configurazione, la suddivisione in livelli cloud garantisce che il 20% (in questo caso 100 GB) di spazio venga mantenuto libero e disponibile nel computer locale. Di conseguenza, la capacità totale della cache locale è di 400 GB. 400 GB archivieranno i file utilizzati più di recente e usati di frequente nel volume locale.

Con questa configurazione, solo i file da 1 a 4 verrebbero archiviati nella cache locale e il file 5 verrebbe suddiviso in livelli. Si tratta solo di 370 GB di 400 GB che potrebbero essere usati. Il file 5 è 140 GB e supera il limite di 400 GB se è stato memorizzato nella cache locale. 

**Modifica 2:** Supponiamo che un utente acceda al file 5. In questo modo si crea il file 5 il file a cui si accede più di recente nella condivisione. Di conseguenza, il file 5 verrebbe archiviato nella cache locale e per adattarsi al limite di 400 GB, il file 4 verrebbe suddiviso in livelli. La tabella seguente mostra la posizione in cui vengono archiviati i file, con questi aggiornamenti:

|File Name |Ora ultimo accesso  |Dimensioni file  |Archiviato in |
|----------|------------------|-----------|----------|
|File 5    | 2 ore fa | 140 GB | Server e condivisione file di Azure
|File 1    | 2 giorni fa  | 10 GB | Server e condivisione file di Azure
|File 2    | 10 giorni fa | 30 GB | Server e condivisione file di Azure
|File 3    | 1 anno fa | 200 GB | Server e condivisione file di Azure
|File 4    | 1 anno, 2 giorni fa | 130 GB | Condivisione file di Azure, a livelli localmente

**Modifica 3:** Si consiglia di aggiornare i criteri in modo che i criteri di suddivisione in livelli basati sulla data siano pari a 60 giorni e che i criteri di spazio disponibile del volume siano 70%. A questo punto, è possibile archiviare nella cache locale solo fino a 150 GB. Anche se è stato eseguito l'accesso al file 2 meno di 60 giorni fa, i criteri di spazio disponibile del volume sostituiscono i criteri di data e il file 2 viene suddiviso in livelli per mantenere lo spazio disponibile locale del 70%.

**Modifica 4:** Se il criterio di spazio disponibile nel volume è stato modificato in 20% e quindi viene usato `Invoke-StorageSyncFileRecall` per richiamare tutti i file che rientrano nell'unità locale rispettando i criteri di suddivisione in livelli nel cloud, la tabella avrà un aspetto simile al seguente:

|File Name |Ora ultimo accesso  |Dimensioni file  |Archiviato in |
|----------|------------------|-----------|----------|
|File 5    | 1 ora fa  | 140 GB | Server e condivisione file di Azure
|File 1    | 2 giorni fa  | 10 GB | Server e condivisione file di Azure
|File 2    | 10 giorni fa | 30 GB | Server e condivisione file di Azure
|File 3    | 1 anno fa | 200 GB | Condivisione file di Azure, a livelli localmente
|File 4    | 1 anno, 2 giorni fa | 130 GB | Condivisione file di Azure, a livelli localmente

In questo caso, i file 1, 2 e 5 verrebbero memorizzati nella cache locale e i file 3 e 4 verrebbero suddivisi a livelli. Poiché i criteri relativi alla data sono 60 giorni, i file 3 e 4 sono a livelli, anche se i criteri di spazio libero del volume consentono fino a 400 GB in locale.

> [!NOTE] 
> I file non vengono richiamati automaticamente quando i clienti modificano i criteri di spazio disponibile nel volume in un valore inferiore, ad esempio dal 20% al 10% in alternativa, impostare i criteri relativi alla data su un valore più grande, ad esempio da 20 giorni a 50 giorni.

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Più endpoint server in un volume locale

È possibile abilitare la suddivisione in livelli nel cloud per più endpoint server in un singolo volume locale. Per questa configurazione, è necessario impostare lo spazio disponibile sul volume sullo stesso importo per tutti gli endpoint server nello stesso volume. Se si impostano criteri di spazio libero del volume diversi per diversi endpoint server nello stesso volume, la percentuale di spazio disponibile del volume maggiore avrà la precedenza. Questa operazione è denominata **criterio di spazio disponibile del volume effettivo**. Se, ad esempio, si dispone di tre endpoint server nello stesso volume locale, uno impostato su 15%, un altro impostato sul 20% e un terzo impostato sul 30%, tutti inizieranno a livelli i file più freddi se hanno meno del 30% di spazio libero disponibile.

## <a name="next-steps"></a>Passaggi successivi
* [Monitorare la suddivisione in livelli cloud](storage-sync-monitor-cloud-tiering.md)
