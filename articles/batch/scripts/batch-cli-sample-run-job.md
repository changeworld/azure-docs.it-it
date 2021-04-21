---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Eseguire un processo Batch
description: Questo script crea un processo Batch e aggiunge una serie di attività al processo. Dimostra inoltre come monitorare un processo e le relative attività.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e51fa96370b46c9a5a5d5ed6fd7593a06e45430
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768166"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Esempio di interfaccia della riga di comando: eseguire un processo e attività con Azure Batch

Questo script crea un processo Batch e aggiunge una serie di attività al processo. Dimostra inoltre come monitorare un processo e le relative attività. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Per questa esercitazione è necessaria la versione 2.0.20 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. 

## <a name="example-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Crea l'account Batch. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Effettua l'autenticazione con l'account Batch specificato per un'ulteriore interazione con l'interfaccia della riga di comando.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Crea un pool di nodi di calcolo.  |
| [az batch job create](/cli/azure/batch/job#az_batch_job_create) | Crea un processo Batch.  |
| [az batch task create](/cli/azure/batch/task#az_batch_task_create) | Aggiunge un'attività al processo Batch specificato.  |
| [az batch job set](/cli/azure/batch/job#az_batch_job_set) | Aggiorna le proprietà di un processo Batch.  |
| [az batch job show](/cli/azure/batch/job#az_batch_job_show) | Recupera i dettagli di un processo Batch specificato.  |
| [az batch task show](/cli/azure/batch/task#az_batch_task_show) | Recupera i dettagli di un'attività dal processo Batch specificato.  |
| [az group delete](/cli/azure/group#az_group_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
