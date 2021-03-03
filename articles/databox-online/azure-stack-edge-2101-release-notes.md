---
title: Azure Stack Edge Pro con le note sulla versione di FPGA 2101 | Microsoft Docs
description: Vengono descritti i problemi e le risoluzioni critici aperti per la versione di Azure Stack Edge 2101.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/01/2021
ms.author: alkohli
ms.openlocfilehash: 5c95da8d6ee88786ff983b4963c1cb96394886cf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727550"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Note sulla versione di Azure Stack Edge Pro con FPGA 2101

Le note sulla versione seguenti identificano i problemi critici aperti e i problemi risolti per la versione 2101 di Azure Stack Edge Pro con un FPGA (Field Programmable Gate Array) incorporato.

Le note sulla versione vengono aggiornate continuamente. Quando vengono individuati problemi critici che richiedono una soluzione alternativa, vengono aggiunti. Prima di distribuire il dispositivo Azure Stack Edge, esaminare attentamente le informazioni contenute nelle note sulla versione.  

Questa versione corrisponde alla versione del software:

- **Azure stack Edge 2101 (1.6.1475.2528)** -KB 4599267

> [!NOTE]
> L'aggiornamento 2101 può essere applicato solo ai dispositivi che eseguono versioni di disponibilità generale (GA) del software o versione successiva.

## <a name="whats-new"></a>Novità

Questa versione include la correzione di bug seguente:

- **Problema di caricamento** : questa versione corregge un problema di caricamento, in cui i riavvii di caricamento causati da un errore possono rallentare la frequenza di completamento del caricamento. Questo problema può verificarsi quando si carica un set di dati costituito principalmente da file di grandi dimensioni rispetto alla larghezza di banda disponibile, in particolare, ma non limitato, quando la limitazione della larghezza di banda è attiva. Questa modifica garantisce un'opportunità sufficiente per il completamento del caricamento prima di riavviare il caricamento per un determinato file.

Questa versione contiene anche gli aggiornamenti seguenti:

- Tutti gli aggiornamenti cumulativi di Windows e gli aggiornamenti di .NET Framework rilasciati fino al 2020 ottobre.
- La versione del firmware Baseboard Management Controller (BMC) viene aggiornata da 3.32.32.32 a 3.36.36.36 durante l'installazione della factory per risolvere l'incompatibilità con le unità di alimentazione di dell più recenti.
- Questa versione supporta IoT Edge 1.0.9.3 nei dispositivi Azure Stack Edge.

## <a name="known-issues-in-this-release"></a>Problemi noti in questa versione

Non sono stati rilasciati nuovi problemi per questa versione. Tutti i problemi rilasciati sono stati rilasciati dalle versioni precedenti. Per visualizzare un elenco di problemi noti, vedere [problemi noti nella versione GA](../databox-gateway/data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)