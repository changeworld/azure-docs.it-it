---
title: Gestire i criteri di registrazione-Azure
description: In questo argomento viene illustrato come gestire i criteri di registrazione.
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: ec72f28496c1392b9d95134c343e1892998a0c28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99224990"
---
# <a name="manage-recording-policy"></a>Gestire i criteri di registrazione

È possibile usare l'analisi video in tempo reale su IoT Edge per la [registrazione video continua](continuous-video-recording-concept.md), in cui è possibile registrare video nel cloud per settimane o mesi. È possibile gestire la lunghezza (in giorni) dell'archivio cloud usando gli strumenti di [gestione del ciclo](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal) di vita incorporati in archiviazione di Azure.  

L'account di servizi multimediali è collegato a un account di archiviazione di Azure e, quando si registra un video nel cloud, il contenuto viene scritto in un [Asset](../latest/assets-concept.md)di servizi multimediali. Ogni asset è mappato a un contenitore nell'account di archiviazione. Il ciclo di vita di gestione consente di definire un [criterio](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy) per un account di archiviazione, in cui è possibile specificare una [regola](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules) come la seguente.

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

Regola precedente:

* Si applica a tutti i BLOB in blocchi nell'account di archiviazione.
* Specifica che quando i BLOB superano i 30 giorni, vengono spostati dal [livello di accesso frequente a](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)sporadico.
* E quando i BLOB superano i 90 giorni, devono essere eliminati.

Quando si usa analisi video in tempo reale per registrare in un asset, si specifica una `segmentLength` proprietà che indica al modulo di aggregare una durata minima del video (in secondi) prima che venga scritta nel cloud. L'asset conterrà una serie di segmenti, ognuno con un timestamp di creazione `segmentLength` più recente rispetto a quello precedente. Quando vengono avviati i criteri di gestione del ciclo di vita, vengono eliminati i segmenti anteriori alla soglia specificata. Tuttavia, si continuerà a essere in grado di accedere e riprodurre i segmenti rimanenti tramite le API del servizio multimediale. Per altre informazioni, vedere [riprodurre registrazioni](playback-recordings-how-to.md). 

## <a name="limitations"></a>Limitazioni

Di seguito sono elencate alcune limitazioni note relative alla gestione del ciclo di vita:

* È possibile avere al massimo 100 regole all'interno del criterio e ogni regola può specificare fino a 10 contenitori. Quindi, se era necessario disporre di criteri di registrazione diversi (ad esempio, l'archivio di 3 giorni per la fotocamera con il parcheggio, 30 giorni per la fotocamera nel Dock di caricamento e 180 giorni per la fotocamera dietro il contatore di checkout), con un account del servizio multimediale è possibile personalizzare le regole per al massimo 1000 fotocamere.
* Gli aggiornamenti dei criteri di gestione del ciclo di vita non sono immediato. Per ulteriori informazioni, vedere [la sezione Domande frequenti](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq) .
* Se si sceglie di applicare un criterio in cui i BLOB vengono spostati nel livello di accesso sporadico, potrebbe essere interessata la riproduzione della parte dell'archivio. È possibile che vengano visualizzate latenze aggiuntive o errori sporadici. Servizi multimediali non supporta la riproduzione del contenuto nel livello archivio.

## <a name="next-steps"></a>Passaggi successivi

[Riproduzione di registrazioni](playback-recordings-how-to.md)
