---
title: Gestione traffico di Azure override della subnet usando l'interfaccia della riga di comando di Azure | Microsoft Docs
description: Questo articolo illustra come usare l'override della subnet di Gestione traffico per eseguire l'override del metodo di routing di un profilo di Gestione traffico per indirizzare il traffico a un endpoint in base all'indirizzo IP dell'utente finale tramite l'intervallo IP predefinito ai mapping degli endpoint.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.topic: how-to
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: fab167884d9060edc4f626d3ee05fa0b23389d92
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767800"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Override della subnet di Gestione traffico tramite l'interfaccia della riga di comando di Azure

L'override della subnet di Gestione traffico consente di modificare il metodo di routing di un profilo.  L'aggiunta di un override indirizza il traffico in base all'indirizzo IP dell'utente finale con un intervallo IP predefinito per il mapping dell'endpoint. 

## <a name="how-subnet-override-works"></a>Funzionamento dell'override della subnet

Quando vengono aggiunte sostituzioni di subnet a un profilo di Gestione traffico, Gestione traffico verifica innanzitutto se è presente una sostituzione di subnet per l'indirizzo IP dell'utente finale. Se ne viene trovata una, la query DNS dell'utente verrà indirizzata all'endpoint corrispondente.  Se non viene trovato un mapping, Gestione traffico esegue il fall back al metodo di routing originale del profilo. 

Gli intervalli di indirizzi IP possono essere specificati come intervalli CIDR (ad esempio, 1.2.3.0/24) o come intervalli di indirizzi (ad esempio, 1.2.3.4-5.6.7.8). Gli intervalli IP associati a ogni endpoint devono essere univoci per tale endpoint. Qualsiasi sovrapposizione di intervalli IP tra endpoint diversi causerà il rifiuto del profilo da parte di Gestione traffico.

Esistono due tipi di profili di routing che supportano le sostituzioni delle subnet:

* **Geografico:** se Gestione traffico trova una sostituzione della subnet per l'indirizzo IP della query DNS, instraderà la query all'endpoint indipendentemente dall'integrità dell'endpoint.
* **Prestazioni:** se Gestione traffico trova una sostituzione della subnet per l'indirizzo IP della query DNS, instraderà il traffico all'endpoint solo se è integro.  Gestione traffico eseguirà il fall back all'euristica del routing delle prestazioni se l'endpoint di override della subnet non è integro.

## <a name="create-a-traffic-manager-subnet-override"></a>Creare un override della subnet di Gestione traffico

Per creare un override della subnet di Gestione traffico, è possibile usare l'interfaccia della riga di comando di Azure per aggiungere le subnet per l'override all'endpoint di Gestione traffico.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.28 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>Aggiornare l'endpoint di Gestione traffico con l'override della subnet.
Usare l'interfaccia della riga di comando di Azure per aggiornare l'endpoint [con az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update).

```azurecli-interactive
### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints
```

È possibile rimuovere gli intervalli di indirizzi IP eseguendo [az network traffic-manager endpoint update](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update) con l'opzione **--remove.**

```azurecli-interactive
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [metodi di routing](traffic-manager-routing-methods.md)di Gestione traffico.

Informazioni sul metodo [di routing del traffico subnet](./traffic-manager-routing-methods.md#subnet-traffic-routing-method)