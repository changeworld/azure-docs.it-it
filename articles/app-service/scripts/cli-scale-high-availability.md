---
title: "Interfaccia della riga di comando: Ridimensionare l'app con Gestione traffico"
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per automatizzare la distribuzione e la gestione dell'app Servizio app. Questo esempio illustra come ridimensionare un'app a livello globale con Gestione traffico.
author: msangapu-msft
tags: azure-service-management
ms.assetid: e4033a50-0e05-4505-8ce8-c876204b2acc
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/11/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 089ab9d6b3c0f678c2a200872a0da4160fa6292a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787666"
---
# <a name="scale-an-app-service-app-worldwide-with-a-high-availability-architecture-using-azure-cli"></a>Ridimensionare un'app del servizio app a livello globale con un'architettura a disponibilità elevata usando l'interfaccia della riga di comando di Azure

Questo script di esempio crea un gruppo di risorse, due piani di servizio app, due app, un profilo di Gestione traffico e due endpoint di gestione traffico. Dopo aver completato l'esercizio si avrà un'architettura a disponibilità elevata che fornisce la disponibilità globale dell'app in base alla latenza di rete più bassa.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/scale-geographic/scale-geographic.sh "Geographic Scale")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, un'app del servizio app, un profilo di Gestione traffico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Consente di creare un piano di servizio app. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Consente di creare un'app del servizio app. |
| [`az network traffic-manager profile create`](/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) | Crea un profilo di Gestione traffico di Azure. |
| [`az network traffic-manager endpoint create`](/cli/azure/network/traffic-manager/endpoint#az_network_traffic-manager_endpoint_create) | Aggiunge un endpoint a un profilo di Gestione traffico di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../samples-cli.md).
