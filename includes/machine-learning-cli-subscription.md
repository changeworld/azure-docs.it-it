---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 493c674fa161bf33436e560fdcbb9196410db931
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102209470"
---
> [!TIP]
> Dopo l'accesso, viene visualizzato un elenco di sottoscrizioni associate all'account Azure. Le informazioni della sottoscrizione con `isDefault: true` rappresentano la sottoscrizione attualmente attivata per i comandi dell’interfaccia della riga di comando di Azure. Questa sottoscrizione deve corrispondere a quella che contiene l'area di lavoro Azure Machine Learning. È possibile trovare l'ID sottoscrizione dal [portale di Azure](https://portal.azure.com) visitando la pagina Panoramica dell'area di lavoro. È anche possibile usare l'SDK per ottenere l'ID sottoscrizione dall'oggetto dell'area di lavoro. Ad esempio: `Workspace.from_config().subscription_id`.
> 
> Per selezionare un'altra sottoscrizione, usare il comando `az account set -s <subscription name or ID>` e specificare il nome o l'ID sottoscrizione a cui passare. Per altre informazioni sulla selezione delle sottoscrizioni, vedere [Usare più sottoscrizioni di Azure](/cli/azure/manage-azure-subscriptions-azure-cli).