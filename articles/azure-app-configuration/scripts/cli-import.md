---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Eseguire l'importazione in un archivio di Configurazione app
titleSuffix: Azure App Configuration
description: Usare lo script dell'interfaccia della riga di comando di Azure per importare la configurazione in Configurazione app di Azure
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5a80acd1261211fe3efaefa6a4b6025a321e2d65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774180"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importare in un archivio di Configurazione app di Azure

Questo script di esempio importa le impostazioni di coppie chiave-valore in un archivio di Configurazione app di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="sample-script"></a>Script di esempio

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --format json --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per importare un archivio di Configurazione app. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az appconfig kv import](/cli/azure/appconfig/kv#az_appconfig_kv_import) | Importa una risorsa archivio di Configurazione app. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script sono disponibili negli [esempi dell'interfaccia della riga di comando di Configurazione app di Azure](../cli-samples.md).
