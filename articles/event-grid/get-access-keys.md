---
title: Ottenere la chiave di accesso per una risorsa griglia di eventi
description: Questo articolo descrive come ottenere la chiave di accesso per un argomento o un dominio di Griglia di eventi
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: cd60777b2e28b82d72f8f2bf93fe0be301e9e280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775224"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Ottenere le chiavi di accesso per le risorse di Griglia di eventi (argomenti o domini)
Le chiavi di accesso vengono usate per autenticare gli eventi di pubblicazione di un'applicazione Griglia di eventi di Azure risorse (argomenti e domini). È consigliabile rigenerare regolarmente le chiavi e archiviarle in modo sicuro. Vengono fornite due chiavi di accesso per poter mantenere le connessioni usando una chiave durante la rigenerazione dell'altra.

Questo articolo descrive come ottenere le chiavi di accesso per una risorsa di Griglia di eventi (argomento o dominio) usando portale di Azure, PowerShell o l'interfaccia della riga di comando. 

## <a name="azure-portal"></a>Portale di Azure
Nel riquadro portale di Azure passare alla scheda **Chiavi** di accesso della **pagina** Argomento di Griglia di eventi o Dominio di **Griglia** di eventi per l'argomento o il dominio.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Pagina Chiavi di accesso":::

## <a name="azure-powershell"></a>Azure PowerShell
Usare il [comando Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey) per ottenere le chiavi di accesso per gli argomenti. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Usare [il comando Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey) per ottenere le chiavi di accesso per i domini. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Usare [l'elenco di chiavi dell'argomento az eventgrid](/cli/azure/eventgrid/topic/key#az_eventgrid_topic_key_list) per ottenere le chiavi di accesso per gli argomenti. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Usare [az eventgrid domain key list](/cli/azure/eventgrid/domain/key#az_eventgrid_domain_key_list) per ottenere le chiavi di accesso per i domini. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo seguente: [Autenticare i client di pubblicazione.](security-authenticate-publishing-clients.md) 
