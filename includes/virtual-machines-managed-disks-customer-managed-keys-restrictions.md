---
title: includere file
description: includere file
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 75acbb30c2bf811b7ae72d6939b9f164554fdd32
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98859703"
---
- Sono supportate solo [le chiavi RSA software e HSM](../articles/key-vault/keys/about-keys.md) di dimensioni 2.048 bit, 3.072 bit e 4.096 bit, senza altre chiavi o dimensioni.
    - Le chiavi [HSM](../articles/key-vault/keys/hsm-protected-keys.md) richiedono il livello **Premium** di Azure Key Vault.
- I dischi creati da immagini personalizzate crittografati con crittografia lato server e chiavi gestite dal cliente devono essere crittografati usando le stesse chiavi gestite dal cliente e devono trovarsi nella stessa sottoscrizione.
- Gli snapshot creati da dischi crittografati con crittografia lato server e chiavi gestite dal cliente devono essere crittografati con le stesse chiavi gestite dal cliente.
- Tutte le risorse correlate alle chiavi gestite dal cliente (istanze di Azure Key Vault, set di crittografia dischi, macchine virtuali, dischi e snapshot) devono trovarsi nella stessa sottoscrizione e nella stessa area.
- I dischi, gli snapshot e le immagini crittografati con chiavi gestite dal cliente non possono essere spostati in un altro gruppo di risorse e sottoscrizione.
- I dischi gestiti attualmente o crittografati in precedenza usando crittografia dischi di Azure non possono essere crittografati con chiavi gestite dal cliente.
- Può creare solo fino a 1000 set di crittografia del disco per area per sottoscrizione.
- Per informazioni sull'uso delle chiavi gestite dal cliente con le raccolte immagini condivise, vedere [Anteprima: Usare le chiavi gestite dal cliente per crittografare le immagini](../articles/virtual-machines/image-version-encryption.md).
