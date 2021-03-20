---
title: "Interfaccia della riga di comando: Distribuire i file dell'app con FTP"
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per automatizzare la distribuzione e la gestione dell'app Servizio app. Questo esempio illustra come creare un'app e distribuire file con FTP.
author: msangapu-msft
tags: azure-service-management
ms.devlang: azurecli
ms.topic: sample
ms.date: 12/12/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurecli
ms.openlocfilehash: 50a7d5e4a33762b5ae0725165e6062a83fec9110
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97006277"
---
# <a name="create-an-app-service-app-and-deploy-files-with-ftp-using-azure-cli"></a>Creare un'app del servizio app e distribuire i file con FTP usando l'interfaccia della riga di comando di Azure

Questo script di esempio crea un'app nel servizio app con le relative risorse e quindi distribuisce una pagina HTML statica con FTP. Per il caricamento FTP, lo script usa [cURL](https://en.wikipedia.org/wiki/CURL) come esempio. È possibile usare qualsiasi strumento FTP per caricare i file.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/deploy-ftp/deploy-ftp.sh "Create an app and deploy files with FTP")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script 

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [`az group create`](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create) | Consente di creare un piano di servizio app. |
| [`az webapp create`](/cli/azure/webapp#az-webapp-create) | Consente di creare un'app del servizio app. |
| [`az webapp deployment list-publishing-profiles`](/cli/azure/webapp/deployment#az-webapp-deployment-list-publishing-profiles) | Consente di ottenere i dettagli dei profili disponibili per la distribuzione dell'app. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando del servizio app sono disponibili nella [documentazione del servizio app di Azure](../samples-cli.md).
