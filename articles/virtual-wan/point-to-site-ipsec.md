---
title: Criteri IPsec da punto a sito della rete WAN virtuale
titleSuffix: Azure Virtual WAN
description: Informazioni sui criteri di connettività IPsec da punto a sito della rete WAN virtuale di Azure.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: wellee
ms.openlocfilehash: d64748bf7719af52b76c5e4141bfbbabe80bbae2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745910"
---
# <a name="point-to-site-ipsec-policies"></a>Criteri IPsec da punto a sito

Questo articolo illustra le combinazioni di criteri IPsec supportate per la connettività VPN da punto a sito nella rete WAN virtuale di Azure.

## <a name="default-ipsec-policies"></a>Criteri IPsec predefiniti

La tabella seguente illustra i parametri IPsec predefiniti per le connessioni VPN da punto a sito. Questi parametri vengono scelti automaticamente dal gateway VPN da punto a sito della rete WAN virtuale quando viene creato un profilo da punto a sito.

| Impostazione | Parametri |
|--- |--- |
| Fase 1, crittografia IKE | AES256 |
| Integrità IKE fase 1 |  SHA256 |
| Gruppo DH | DHGroup24 |
| Crittografia IPsec della fase 2 | GCMAES256|
| Integrità IPsec della fase 2 | GCMAES25 |
| Gruppo PFS |PFS24|

## <a name="custom-ipsec-policies"></a>Criteri IPsec personalizzati

Quando si utilizzano i criteri IPsec personalizzati, tenere presenti i requisiti seguenti:

* **IKE** : per la fase 1 IKE, è possibile selezionare qualsiasi parametro dalla crittografia IKE, oltre a qualsiasi parametro dell'integrità IKE, più qualsiasi parametro del gruppo DH.
* **IPSec** : per IPSec della fase 2, è possibile selezionare qualsiasi parametro dalla crittografia IPSec, oltre a qualsiasi parametro dall'integrità IPSec, più PFS. Se uno dei parametri per la crittografia IPsec o l'integrità IPsec è GCM, sia la crittografia IPsec che l'integrità devono usare lo stesso algoritmo. Se, ad esempio, si sceglie GCMAES128 per la crittografia IPsec, è necessario scegliere anche GCMAES128 per l'integrità IPsec.  

La tabella seguente illustra i parametri IPsec disponibili per le connessioni VPN da punto a sito.

| Impostazione | Parametri |
|--- |--- |
| Fase 1, crittografia IKE | AES128, AES256, GCMAES128, GCMAES256 |
| Integrità IKE fase 1 |  SHA256, SHA384 |
| Gruppo DH | DHGroup14, DHGroup24, ECP256, ECP384 |
| Crittografia IPsec della fase 2 | GCMAES128, GCMAES256, SHA256|
| Integrità IPsec della fase 2 | GCMAES128, GCMAES256 |
| Gruppo PFS |PFS14, PFS24, ECP256, ECP384|

## <a name="next-steps"></a>Passaggi successivi

Vedere [come creare una connessione da punto a sito](virtual-wan-point-to-site-portal.md)

Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti sulla rete WAN virtuale](virtual-wan-faq.md).
