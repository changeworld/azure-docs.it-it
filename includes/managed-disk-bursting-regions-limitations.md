---
title: includere file
description: includere file
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102178360"
---
- Non è possibile abilitare l'espansione su richiesta in un'unità SSD Premium con meno di 512 GiB. Unità SSD Premium inferiori a 512 GiB utilizzeranno sempre il picchi basato sul credito.
- L'espansione su richiesta è supportata solo su unità SSD Premium. Se un'unità SSD Premium con funzionalità di espansione su richiesta è impostata su un altro tipo di disco, l'espansione del disco è disabilitata.
- L'espansione su richiesta non si disattiva automaticamente quando il livello di prestazioni viene modificato. Se si desidera modificare il livello di prestazioni ma non si desidera che venga mantenuto il disco, è necessario disabilitarlo.
- L'espansione su richiesta può essere abilitata solo quando il disco viene scollegato da una macchina virtuale o quando la macchina virtuale viene arrestata. L'espansione su richiesta può essere disabilitata per 12 ore dopo l'abilitazione.