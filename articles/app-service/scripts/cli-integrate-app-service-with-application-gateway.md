---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Integrare il servizio app con un gateway applicazione | Microsoft Docs
description: Esempio di script dell'interfaccia della riga di comando di Azure - Integrare il servizio app con un gateway applicazione
services: appservice
documentationcenter: appservice
author: madsd
manager: ccompy
editor: ''
tags: azure-service-management
ms.assetid: 6c16d6f8-3c08-4a59-858e-684a2ceccb5f
ms.service: app-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: d30cc27fc3c546619e85bb9aabd0b31c10102e96
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787810"
---
# <a name="integrate-app-service-with-application-gateway-using-cli"></a>Integrare il servizio app con un gateway applicazione tramite l'interfaccia della riga di comando

Questo script di esempio crea un'app Web del servizio app di Azure, una rete virtuale di Azure e un gateway applicazione. Limita quindi il traffico per l'app Web in modo che provenga solo dalla subnet del gateway applicazione.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0.74 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/integrate-with-app-gateway/integrate-with-app-gateway.sh "Integrate with Application Gateway")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, l'app del servizio app, Cosmos DB e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [`az group create`](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [`az network vnet create`](/cli/azure/network/vnet#az_network_vnet_create) | Crea una rete virtuale. |
| [`az network public-ip create`](/cli/azure/network/public-ip#az_network_public_ip_create) | Crea un indirizzo IP pubblico. |
| [`az network public-ip show`](/cli/azure/network/public-ip#az_network_public_ip_show) | Mostra i dettagli di un indirizzo IP pubblico. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az_appservice_plan_create) | Consente di creare un piano di servizio app. |
| [`az webapp create`](/cli/azure/webapp#az_webapp_create) | Crea un'app Web del servizio app. |
| [`az webapp show`](/cli/azure/webapp#az_webapp_show) | Mostra i dettagli di un'app Web del servizio app. |
| [`az webapp config access-restriction add`](/cli/azure/webapp/config/access-restriction#az_webapp_config_access_restriction_add) | Aggiunge una restrizione di accesso all'app Web del servizio app. |
| [`az network application-gateway create`](/cli/azure/network/application-gateway#az_network_application_gateway_create) | Crea un gateway applicazione. |
| [`az network application-gateway http-settings update`](/cli/azure/network/application-gateway/http-settings#az_network-application-gateway-http_settings_update) | Aggiorna le impostazioni HTTP del gateway applicazione. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../samples-cli.md).
