---
title: includere file
description: includere file
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: a550087f552238820ecff079ff1dc2523c4172d3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776809"
---
La funzionalità Crittografia dischi di Azure può essere abilitata e gestita tramite l'[interfaccia della riga di comando di Azure](/cli/azure) e [Azure PowerShell](/powershell/azure/new-azureps-module-az). A tale scopo, è necessario installare gli strumenti localmente e connettersi alla sottoscrizione di Azure.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'[interfaccia della riga di comando di Azure 2.0](/cli/azure) è uno strumento da riga di comando per la gestione delle risorse di Azure. L'interfaccia della riga di comando è progettata per eseguire query sui dati in modo flessibile, supportare operazioni a esecuzione prolungata come processi non bloccanti e semplificare la creazione di script. È possibile installarla in locale seguendo i passaggi descritti in [Installare l'interfaccia della riga di comando di Azure.](/cli/azure/install-azure-cli).

Per [accedere all'account di Azure con l'interfaccia della riga di comando di Azure](/cli/azure/authenticate-azure-cli), usare il comando [az login](/cli/azure/reference-index#az_login).

```azurecli
az login
```

Per selezionare un tenant per l'accesso, usare:
    
```azurecli
az login --tenant <tenant>
```

In caso di più sottoscrizioni, per specificarne una in particolare è necessario ottenere l'elenco di sottoscrizioni tramite [az account list](/cli/azure/account#az_account_list) (elenco account di accesso di AZ) e specificarla da [az account set](/cli/azure/account#az_account_set) (imposta account di accesso di AZ).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Per altre informazioni, vedere [Azure Service Fabric e interfaccia della riga di comando di Azure 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
Il [modulo az di Azure PowerShell](/powershell/azure/new-azureps-module-az) offre un set di cmdlet che usano il modello [Azure Resource Manager](../articles/azure-resource-manager/management/overview.md) per la gestione delle risorse di Azure. È possibile usarlo nel browser con [Azure Cloud Shell](../articles/cloud-shell/overview.md) oppure installarlo nel computer locale seguendo le istruzioni disponibili in [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps). 

Se è già installato in locale, assicurarsi di usare la versione più recente di Azure PowerShell SDK per configurare Crittografia dischi di Azure. Scaricare la versione più recente di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

Per [accedere all'account di Azure con Azure PowerShell](/powershell/azure/authenticate-azureps), usare il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

```powershell
Connect-AzAccount
```

Se si hanno più sottoscrizioni e si vuole specificarne una, usare il cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) per elencarle, seguito dal cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext):

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

L'esecuzione del cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) verifica la selezione della sottoscrizione corretta.

Per controllare che i cmdlet di Crittografia dischi di Azure siano installati, usare il cmdlet [Get-command](/powershell/module/microsoft.powershell.core/get-command):
     
```powershell
Get-command *diskencryption*
```
Per altre informazioni, vedere [Introduzione ad Azure PowerShell](/powershell/azure/get-started-azureps).