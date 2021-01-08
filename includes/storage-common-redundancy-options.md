---
title: includere file
description: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 65729934ea7c4037d6857aec10b14cdddd616368
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805608"
---
Le opzioni di ridondanza per un account di archiviazione includono:

* Archiviazione con ridondanza locale (LRS): strategia di ridondanza semplice e a costi contenuti. I dati vengono copiati in modo sincrono per tre volte all'interno di un'unica posizione fisica nell'area primaria.
* Archiviazione con ridondanza della zona (ZRS): ridondanza per gli scenari in cui è richiesta la disponibilità elevata. I dati vengono copiati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria.
* Archiviazione con ridondanza geografica: ridondanza tra più aree per evitare interruzioni a livello di area. I dati vengono copiati in modo sincrono per tre volte all'interno dell'area primaria e quindi copiati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).
* Archiviazione con ridondanza geografica della zona (anteprima): ridondanza per gli scenari in cui sono richieste sia la disponibilità elevata cha la durabilità massima. I dati vengono copiati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria e quindi copiati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica della zona e accesso in lettura (RA-GZRS).

Per altre informazioni sulle opzioni di ridondanza in Archiviazione di Azure, vedere [Ridondanza di Archiviazione di Azure](../articles/storage/common/storage-redundancy.md).
