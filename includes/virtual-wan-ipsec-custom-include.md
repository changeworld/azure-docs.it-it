---
title: includere file
description: includere file
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 83f0ce27172879a37de9488499e46de30b8e112c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98147340"
---
Quando si utilizzano i criteri IPsec personalizzati, tenere presenti i requisiti seguenti:

* **IKE** : per IKE è possibile selezionare qualsiasi parametro dalla crittografia IKE, oltre a qualsiasi parametro dell'integrità IKE, più qualsiasi parametro del gruppo DH.
* **IPSec** : per IPSec è possibile selezionare qualsiasi parametro dalla crittografia IPSec, oltre a qualsiasi parametro dall'integrità IPSec, più PFS. Se uno dei parametri per la crittografia IPsec o l'integrità IPsec è GCM, i parametri per entrambe le impostazioni devono essere GCM.

>[!NOTE]
> Con i criteri IPsec personalizzati non esiste alcun concetto di risponditore e iniziatore (a differenza dei criteri IPsec predefiniti). Entrambi i lati (locale e gateway VPN di Azure) utilizzeranno le stesse impostazioni per IKE fase 1 e IKE fase 2. Sono supportati sia i protocolli IKEv1 che IKEv2. Non è disponibile alcun supporto per Azure come risponditore.
>

**Impostazioni e parametri disponibili**

| Impostazione | Parametri |
|--- |--- |
| Crittografia IKE | GCMAES256, GCMAES128, AES256, AES128 |
| Integrità IKE | SHA384, SHA256 |
| Gruppo DH | ECP384, ECP256, DHGroup24, DHGroup14 |
| Crittografia IPsec | GCMAES256, GCMAES128, AES256, AES128, None |
| Integrità IPsec | GCMAES256, GCMAES128, SHA256 |
| Gruppo PFS | ECP384, ECP256, PFS24, PFS14, None |
| Durata dell'associazione di sicurezza |intero min. 300/default 27000 secondi |
