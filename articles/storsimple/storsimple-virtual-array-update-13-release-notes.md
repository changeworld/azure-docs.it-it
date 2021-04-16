---
title: Microsoft Azure StorSimple note sulla versione di Virtual Array Update 1.3 | Microsoft Docs
description: Vengono descritti i problemi critici di apertura e le soluzioni per l'array virtuale StorSimple di Azure che esegue l'aggiornamento 1.3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 04/13/2021
ms.author: alkohli
ms.openlocfilehash: 498e3d11d8188850a918c67a9a88643d15c134c5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389520"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>Note sulla versione di StorSimple Virtual Array Update 1.3

Le seguenti note sulla versione identificano i problemi critici aperti e risolti per lgi aggiornamenti dell'array virtuale di Microsoft Azure StorSimple.

Le note sulla versione vengono aggiornate continuamente. Nel caso venissero rilevati problemi critici che richiedono soluzioni alternative, le informazioni relative verranno immediatamente aggiunte. Prima di distribuire l'array virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

L'aggiornamento 1.3 corrisponde alla versione 10.0.10319.0 del software.

> [!IMPORTANT]
> - L'aggiornamento 1.3 è un aggiornamento critico. È consigliabile installarlo appena possibile.
> - È possibile installare l'aggiornamento 1.3 solo nei dispositivi che eseguono l'aggiornamento 1.2.
> - Gli aggiornamenti comportano il riavvio del dispositivo. Se è in corso l'I/O, il dispositivo comporta tempi di inattività. Per istruzioni dettagliate sui pacchetti usati per applicare questo aggiornamento, vedere [Scaricare l'aggiornamento 1.3.](#download-update-13)

## <a name="whats-new-in-update-13"></a>Novità dell'aggiornamento 1.3

Questo aggiornamento contiene i miglioramenti seguenti:KB4540725

- Transport Layer Security (TLS) 1.2 è un aggiornamento obbligatorio e deve essere installato. Da questa versione in poi, TLS 1.2 diventa il protocollo standard per tutte portale di Azure comunicazioni.
  
   Se viene visualizzato l'avviso seguente, è necessario aggiornare il software nel dispositivo prima di procedere:

   Uno o più dispositivi StorSimple eseguono una versione software precedente. L'aggiornamento più recente disponibile per TLS 1.2 è un aggiornamento obbligatorio e deve essere installato immediatamente in questi dispositivi. TLS 1.2 viene usato per tutte le comunicazioni portale di Azure e senza questo aggiornamento, il dispositivo non sarà in grado di comunicare con il servizio StorSimple.

- Le correzioni di bug di Garbage Collection migliorano le prestazioni del ciclo di Garbage Collection quando il dispositivo e l'account di archiviazione si verificano in due aree distanti.
- Correzione degli errori di backup a causa di timeout del BLOB.
- Patch di sicurezza aggiornate del sistema operativo/.NET Framework:
  - [KB4540725:](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86)SSU di marzo 2020 (aggiornamento dello stack di manutenzione)
  - [KB4565541:](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01)rollup di luglio 2020
  - [KB4565622:](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00)aggiornamento .NET Framework luglio 2020

## <a name="download-update-13"></a>Scaricare l'aggiornamento 1.3

Per scaricare questo aggiornamento, passare al server [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx) e scaricare il pacchetto KB4575898. Questo pacchetto contiene i pacchetti seguenti. Installare i pacchetti nell'ordine seguente:

1. **KB4540725,** che contiene gli aggiornamenti cumulativi di Windows per 2012 R2 fino a marzo 2020. Per altre informazioni sugli elementi inclusi in questo rollup, vedere Rollup mensile [della sicurezza di marzo.](https://support.microsoft.com/help/4540725)
1. **KB4565541,** che contiene gli aggiornamenti cumulativi di Windows per 2012 R2 fino a luglio 2020. Per altre informazioni sugli elementi inclusi in questo rollup, vedere Rollup mensile [della sicurezza di luglio.](https://support.microsoft.com/help/4565541)
1. **KB4565622,** che cumulative.NET aggiornamenti di Framework fino a luglio 2020. Per altre informazioni sugli elementi inclusi in questo rollup, vedere [KB4565622.](https://support.microsoft.com/help/4565622)<!--The Help link opens the KB. I can't find a monthly rollup. I updated the link text to accurately describe what opens.-->
1. **KB3011067,** che contiene gli aggiornamenti software del dispositivo.

Scaricare KB4575898 e seguire queste istruzioni per Applicare [l'aggiornamento tramite l'interfaccia utente Web locale.](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)

## <a name="known-issues-in-update-13"></a>Problemi noti nell'aggiornamento 1.3
Non sono stati rilasciati nuovi problemi nell'aggiornamento 1.3. Tutti i problemi di cui è stata effettuata la versione sono relativi alle versioni precedenti. Per visualizzare il riepilogo dei problemi noti inclusi nelle versioni precedenti, vedere [Problemi noti nell'aggiornamento 1.2.](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12)

## <a name="next-steps"></a>Passaggi successivi
Scaricare KB4575898 e [applicare l'aggiornamento tramite l'interfaccia utente Web locale.](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui)

## <a name="references"></a>Riferimenti
Si desidera consultare le note su una versione precedente? Passare a:

- [Note sulla versione di StorSimple Virtual Array Update 1.2](./storsimple-virtual-array-update-12-release-notes.md)
- [Note sulla versione dell'aggiornamento 1.0 per l'array virtuale StorSimple](./storsimple-virtual-array-update-1-release-notes.md)
- [Note sulla versione dell'aggiornamento 0.6 per l'array virtuale StorSimple](./storsimple-virtual-array-update-06-release-notes.md)
- [Note sulla versione dell'aggiornamento 0.5 per l'array virtuale StorSimple](./storsimple-virtual-array-update-05-release-notes.md)
- [Note sulla versione dell'aggiornamento 0.4 per l'array virtuale StorSimple](./storsimple-virtual-array-update-04-release-notes.md)
- [Note sulla versione dell'aggiornamento 0.3 per l'array virtuale StorSimple](./storsimple-ova-update-03-release-notes.md)
- [Note sulla versione dell'Aggiornamento 0.1 e 0.2 per l'array virtuale StorSimple](./storsimple-ova-update-01-release-notes.md)
- [Note sulla versione con disponibilità generale dell'array virtuale StorSimple](./storsimple-virtual-array-update-06-release-notes.md)