---
title: Note sulla versione dell'aggiornamento 1,3 dell'array virtuale Microsoft Azure StorSimple | Microsoft Docs
description: Descrive i problemi e le risoluzioni critici aperti per l'array virtuale Azure StorSimple che esegue l'aggiornamento 1,3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 52b30730ee8ab126521ad0760204ee48ef6a63fe
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576009"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>Note sulla versione dell'aggiornamento 1,3 di StorSimple Virtual Array

Le seguenti note sulla versione identificano i problemi critici aperti e risolti per lgi aggiornamenti dell'array virtuale di Microsoft Azure StorSimple.

Le note sulla versione vengono aggiornate continuamente. Nel caso venissero rilevati problemi critici che richiedono soluzioni alternative, le informazioni relative verranno immediatamente aggiunte. Prima di distribuire l'array virtuale StorSimple, esaminare attentamente le informazioni contenute nelle note sulla versione.

L'aggiornamento 1,3 corrisponde alla versione del software 10.0.10319.0.

> [!IMPORTANT]
> - L'aggiornamento 1,3 è un aggiornamento critico. Si consiglia vivamente di installarlo appena possibile.
> - È possibile installare l'aggiornamento 1,3 solo nei dispositivi che eseguono l'aggiornamento 1,2.
> - Gli aggiornamenti comportano il riavvio del dispositivo. Se I/O è in corso, il dispositivo comporta tempi di inattività. Per istruzioni dettagliate sui pacchetti usati per applicare questo aggiornamento, vedere [scaricare l'aggiornamento 1,3](#download-update-13).

## <a name="whats-new-in-update-13"></a>Novità dell'aggiornamento 1,3

Questo aggiornamento contiene i miglioramenti seguenti: KB4540725

- Transport Layer Security (TLS) 1,2 è un aggiornamento obbligatorio e deve essere installato. Da questa versione in poi, TLS 1,2 diventa il protocollo standard per tutte le comunicazioni portale di Azure.
- Le correzioni di bug di Garbage Collection migliorano le prestazioni del ciclo Garbage Collection quando il dispositivo e l'account di archiviazione si trovano in due aree distanti.
- Correzione per errori di backup causati da timeout del BLOB.
- Patch di sicurezza del sistema operativo/.NET Framework aggiornate:
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86): 2020 di marzo SSU (aggiornamento dello stack di manutenzione)
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01): rollup 2020 luglio
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00): 2020 luglio .NET Framework aggiornamento

## <a name="download-update-13"></a>Scaricare l'aggiornamento 1,3

Per scaricare l'aggiornamento, passare al server di [catalogo Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) e scaricare il pacchetto KB4575898. Questo pacchetto contiene i pacchetti seguenti:

- **KB4540725**, che contiene gli aggiornamenti cumulativi di Windows per 2012 R2 fino al 2020 marzo. Per ulteriori informazioni su ciò che è incluso in questo rollup, passare al [rollup della sicurezza mensile di marzo](https://support.microsoft.com/help/4540725).
- **KB4565541**, che contiene gli aggiornamenti cumulativi di Windows per 2012 R2 fino al 2020 luglio. Per ulteriori informazioni su ciò che è incluso in questo rollup, vedere [rollup della sicurezza mensile di febbraio](https://support.microsoft.com/help/4565541).
- **KB4565622**, che contiene gli aggiornamenti di cumulative.NET Framework fino al 2020 luglio. Per ulteriori informazioni su ciò che è incluso in questo rollup, vedere [rollup della sicurezza mensile di febbraio](https://support.microsoft.com/help/4565622).
- **KB3011067**, che contiene gli aggiornamenti del software del dispositivo.

Scaricare KB4575898 e seguire queste istruzioni per [applicare l'aggiornamento tramite l'interfaccia utente Web locale](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="known-issues-in-update-13"></a>Problemi noti nell'aggiornamento 1,3
Non sono stati rilasciati nuovi problemi, indicati nell'aggiornamento 1,3. Tutti i problemi indicati dalla versione vengono rilasciati dalle versioni precedenti. Per visualizzare il riepilogo dei problemi noti inclusi nelle versioni precedenti, passare a [problemi noti nell'aggiornamento 1,2](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12).

## <a name="next-steps"></a>Passaggi successivi
Scaricare KB4575898 e [applicare l'aggiornamento tramite l'interfaccia utente Web locale](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## <a name="references"></a>Riferimenti
Si desidera consultare le note su una versione precedente? Passare a:

- [Note sulla versione dell'aggiornamento 1,2 di StorSimple Virtual Array](./storsimple-virtual-array-update-12-release-notes.md)
- [Note sulla versione dell'aggiornamento 1.0 per l'array virtuale StorSimple](./storsimple-virtual-array-update-1-release-notes.md)
- [Note sulla versione dell'aggiornamento 0.6 per l'array virtuale StorSimple](./storsimple-virtual-array-update-06-release-notes.md)
- [Note sulla versione dell'aggiornamento 0.5 per l'array virtuale StorSimple](./storsimple-virtual-array-update-05-release-notes.md)
- [Note sulla versione dell'aggiornamento 0.4 per l'array virtuale StorSimple](./storsimple-virtual-array-update-04-release-notes.md)
- [Note sulla versione dell'aggiornamento 0.3 per l'array virtuale StorSimple](./storsimple-ova-update-03-release-notes.md)
- [Note sulla versione dell'Aggiornamento 0.1 e 0.2 per l'array virtuale StorSimple](./storsimple-ova-update-01-release-notes.md)
- [Note sulla versione con disponibilità generale dell'array virtuale StorSimple](https://review.docs.microsoft.com/en-us/azure/storsimple/storsimple-ova-pp-release-notes)