---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95560111"
---
Le route definite dall'utente con destinazione 0.0.0.0/0 e gruppi di sicurezza di rete in GatewaySubnet **non sono supportate**. I gateway creati con questa configurazione verranno bloccati. Per il corretto funzionamento è necessario che i gateway possano accedere ai controller di gestione. Per assicurare la disponibilità del gateway, l'opzione [Propagazione route BGP](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol) deve essere impostata su "Abilitato" in GatewaySubnet. Se è impostata su Disabilitato, il gateway non funziona.