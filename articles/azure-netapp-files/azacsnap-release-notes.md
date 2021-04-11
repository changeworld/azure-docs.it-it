---
title: Note sulla versione per applicazione Azure strumento di snapshot coerente per Azure NetApp Files | Microsoft Docs
description: Vengono fornite le note sulla versione per lo strumento applicazione Azure snapshot coerente che è possibile utilizzare con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: phjensen
ms.openlocfilehash: 01fb93dcd0a1d5c1db615c47d7811a0baa863c9b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111446"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Note sulla versione per applicazione Azure strumento snapshot coerente (anteprima)

In questa pagina sono elencate le modifiche principali apportate a AzAcSnap per fornire nuove funzionalità o risolvere i difetti.

## <a name="march-2021"></a>2021 marzo

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v 5.0 Preview (Build: 20210318.30771)

AzAcSnap v 5.0 Preview (Build: 20210318.30771) è stato rilasciato con le correzioni e i miglioramenti seguenti:

- È stata rimossa la necessità di aggiungere l'utente AZACSNAP in SAP HANA database tenant, vedere la sezione [abilitare la comunicazione con SAP Hana](azacsnap-installation.md#enable-communication-with-sap-hana) .
- Correzione per consentire un [ripristino](azacsnap-cmd-ref-restore.md) con volumi configurati con QoS manuale.
- Aggiunto il controllo mutex per limitare le connessioni SSH per istanze large di Azure.
- Correzione del programma di installazione per la gestione dei nomi di percorso con spazi e altri problemi correlati.
- In preparazione per il supporto di altri server di database, il parametro facoltativo '--hanasid ' è stato modificato in '--dbsid '.

Scaricare la [versione più recente](https://aka.ms/azacsnapdownload) del programma di installazione [e vedere come iniziare.](azacsnap-get-started.md)

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione allo strumento di snapshot coerente con applicazione Azure](azacsnap-get-started.md)
