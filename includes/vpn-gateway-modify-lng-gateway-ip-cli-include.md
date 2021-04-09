---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 069d7af9cee0bee673c8f0b32659ce362fe4dd70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026068"
---
### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Per modificare il gateway di rete locale 'gatewayIpAddress'

Se l'indirizzo IP pubblico del dispositivo VPN a cui ci si vuole connettere è stato modificato, è necessario modificare il gateway di rete locale per riflettere tale modifica. L'indirizzo IP del gateway può essere modificato senza rimuovere una connessione gateway VPN esistente, se disponibile. Per modificare l'indirizzo IP del gateway, sostituire i valori 'Site2' e 'TestRG1' con i valori personalizzati usando il comando [az network local-gateway update](/cli/azure/network/local-gateway).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Verificare che l'indirizzo IP sia corretto nell'output:

```
"gatewayIpAddress": "23.99.222.170",
```