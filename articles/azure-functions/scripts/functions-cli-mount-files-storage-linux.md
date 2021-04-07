---
title: Montare una condivisione file in un'app per le funzioni Python - Interfaccia della riga di comando di Azure
description: Creare un'app per le funzioni Python serverless e montare una condivisione file esistente usando l'interfaccia della riga di comando di Azure.
ms.topic: sample
ms.date: 03/01/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 00bf0ecc9db3cf369fd75b427dcfba686aed0ed5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035122"
---
# <a name="mount-a-file-share-to-a-python-function-app-using-azure-cli"></a>Montare una condivisione file in un'app per le funzioni Python con l'interfaccia della riga di comando di Azure

Questo script di esempio di Funzioni di Azure crea un'app per le funzioni e una condivisione in File di Azure. Monta quindi la condivisione in modo che i dati siano accessibili dalle funzioni.  

>[!NOTE]
>L'app per le funzioni creata viene eseguita in Python versione 3.7. Funzioni di Azure [supporta anche Python versione 3.6 e 3.8](../functions-reference-python.md#python-version).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="sample-script"></a>Script di esempio

Questo script crea un'app per le funzioni in Funzioni di Azure usando il [piano a consumo](../consumption-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/functions-cli-mount-files-storage-linux/functions-cli-mount-files-storage-linux.sh "Create a function app on a Consumption plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Ogni comando della tabella include collegamenti alla documentazione specifica del comando. Questo script usa i comandi seguenti:

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Crea un account di Archiviazione di Azure. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Creare un'app per le funzioni. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Crea una condivisione di File di Azure nell'account di archiviazione. | 
| [az storage directory create](/cli/azure/storage/directory#az-storage-directory-create) | Crea una directory nella condivisione. |
| [az webapp config storage-account add](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-add) | Monta la condivisione nell'app per le funzioni. |
| [az webapp config storage-account list](/cli/azure/webapp/config/storage-account#az-webapp-config-storage-account-list) | Mostra le condivisioni file montate nell'app per le funzioni. | 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Funzioni di Azure sono disponibili nella [documentazione di Funzioni di Azure](../functions-cli-samples.md).
