---
title: Note sulla versione di Azure Data Box Gateway 2101 | Microsoft Docs
description: Vengono descritti i problemi critici aperti e le risoluzioni per la Azure Data Box Gateway che esegue la versione 2101.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072637"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Note sulla versione di Azure Data Box Gateway 2101

Le seguenti note sulla versione identificano i problemi critici aperti e i problemi risolti per la versione 2101 di Azure Data Box Gateway.

Le note sulla versione vengono aggiornate continuamente. Quando vengono individuati problemi critici che richiedono una soluzione alternativa, vengono aggiunti. Prima di distribuire il Azure Data Box Gateway, esaminare attentamente le informazioni contenute nelle note sulla versione.  

Questa versione corrisponde alle versioni del software:

- **Data Box Gateway 2101 (1.6.1475.2528)** -KB 4603462

> [!NOTE]
> L'aggiornamento 2101 può essere applicato solo a tutti i dispositivi che eseguono versioni di disponibilità generale (GA) del software o versione successiva.

## <a name="whats-new"></a>Novità

Questa versione include la correzione di bug seguente:

- **Problema di caricamento** : questa versione corregge un problema di caricamento in cui il caricamento viene riavviato a causa di errori che possono rallentare la frequenza di completamento del caricamento. Questo problema può verificarsi quando si carica un set di dati costituito principalmente da file di dimensioni elevate rispetto alla larghezza di banda disponibile, in particolare, ma non limitato, quando la limitazione della larghezza di banda è attiva. Questa modifica garantisce un'opportunità sufficiente per il completamento del caricamento prima di riavviare il caricamento per un determinato file.

Questa versione contiene anche gli aggiornamenti seguenti:

- Tutti gli aggiornamenti cumulativi di Windows e gli aggiornamenti di .NET Framework rilasciati fino al 2020 ottobre.
- L'indirizzo IP statico per Azure Data Box Gateway viene mantenuto attraverso gli aggiornamenti software.

## <a name="known-issues-in-this-release"></a>Problemi noti in questa versione

Non sono stati rilasciati nuovi problemi per questa versione. Tutti i problemi rilasciati sono stati rilasciati dalle versioni precedenti. Per visualizzare un elenco di problemi noti, vedere [problemi noti nella versione GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la distribuzione di Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
