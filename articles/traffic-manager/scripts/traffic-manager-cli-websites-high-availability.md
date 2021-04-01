---
title: Instradare il traffico per la disponibilità elevata di applicazioni-Gestione traffico di Azure
description: Esempio di script dell'interfaccia della riga di comando di Azure - Instradare il traffico per la disponibilità elevata delle applicazioni
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
manager: twooley
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: duau
ms.openlocfilehash: abac2e5b1fcba65db92edd2bcc2f390ec0114f59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185343"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-cli"></a>Instradare il traffico per la disponibilità elevata delle applicazioni con l'interfaccia della riga di comando di Azure

In questo script viene creato un gruppo di risorse, due piani di servizio app, due app Web, un profilo di gestione traffico e due endpoint di gestione traffico. Gestione traffico indirizza il traffico verso l'applicazione in un'area come area primaria e nell'area secondaria quando l'applicazione nell'area primaria non è disponibile. Prima di eseguire lo script, è necessario modificare i valori MyWebApp, MyWebAppL1 e MyWebAppL2 in valori univoci in Azure. Dopo aver eseguito lo script, è possibile accedere all'app nell'area primaria con l'URL mywebapp.trafficmanager.net.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.sh "Route traffic for high availability")]


## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dello script di esempio, eseguire questo comando per rimuovere il gruppo di risorse, l'app del servizio app e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup1 --yes
az group delete --name myResourceGroup2 --yes
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, un'App Web, un profilo di Gestione traffico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az appservice plan create](/cli/azure/appservice/plan) | Consente di creare un piano di servizio app. Equivale a una server farm per l'App Web di Azure. |
| [az webapp web create](/cli/azure/webapp#az-webapp-create) | Consente di creare un'App Web di Azure all'interno del piano di servizio app. |
| [az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile) | Crea un profilo di Gestione traffico di Azure. |
| [az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint) | Aggiunge un endpoint a un profilo di Gestione traffico di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione della rete di Azure](../cli-samples.md).