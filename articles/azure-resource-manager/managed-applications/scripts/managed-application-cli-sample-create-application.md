---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Distribuire un'applicazione gestita
description: Questo articolo include uno script di esempio dell'interfaccia della riga di comando di Azure che distribuisce una definizione di applicazione gestita di Azure alla sottoscrizione.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3ea2baa897efb5c1a01b32e92e76a69c9d1f231c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775494"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Distribuire un'applicazione gestita per il catalogo di servizi con l'interfaccia della riga di comando di Azure

Questo script distribuisce una definizione di applicazione gestita dal catalogo di servizi. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa il comando seguente per distribuire l'applicazione gestita. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az managedapp create](/cli/azure/managedapp#az_managedapp_create) | Crea un'applicazione gestita. Fornire l'ID della definizione e i parametri per il modello. |


## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](../overview.md).
* Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
