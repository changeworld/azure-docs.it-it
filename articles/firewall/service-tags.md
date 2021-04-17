---
title: Panoramica dei tag del servizio Firewall di Azure
description: Un tag di servizio rappresenta un gruppo di prefissi di indirizzo IP che consente di ridurre al minimo la complessità nella creazione di regole di sicurezza.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/5/2021
ms.author: victorh
ms.openlocfilehash: 3cc1e85a18eab1adb0a1dd8307a074cb43ba0c70
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529554"
---
# <a name="azure-firewall-service-tags"></a>Tag del servizio Firewall di Azure

Un tag di servizio rappresenta un gruppo di prefissi di indirizzo IP che consente di ridurre al minimo la complessità nella creazione di regole di sicurezza. Non è possibile creare tag di servizio personalizzati, né specificare gli indirizzi IP inclusi in un tag. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.

I tag del servizio Firewall di Azure possono essere usati nel campo di destinazione delle regole di rete. È possibile usarli al posto di indirizzi IP specifici.

## <a name="supported-service-tags"></a>Tag di servizio supportati

Per [un elenco dei](../virtual-network/service-tags-overview.md#available-service-tags) tag di servizio disponibili per l'uso nelle regole di rete del firewall di Azure, vedere Tag del servizio di rete virtuale.

## <a name="configuration"></a>Configurazione

Firewall di Azure supporta la configurazione dei tag di servizio tramite PowerShell, l'interfaccia della riga di comando di Azure o portale di Azure.

### <a name="configure-via-azure-powershell"></a>Configurare tramite Azure PowerShell

In questo esempio è necessario innanzitutto ottenere il contesto per l'istanza di Firewall di Azure creata in precedenza.

```Get the context to an existing Azure Firewall
$FirewallName = "AzureFirewall"
$ResourceGroup = "AzureFirewall-RG"
$azfirewall = Get-AzFirewall -Name $FirewallName -ResourceGroupName $ResourceGroup
```

Successivamente, è necessario creare una nuova regola.  Per Origine o Destinazione, è possibile specificare il valore di testo del tag di servizio da sfruttare, come indicato in precedenza in questo articolo.

````Create new Network Rules using Service Tags
$rule = New-AzFirewallNetworkRule -Name "AllowSQL" -Description "Allow access to Azure Database as a Service (SQL, MySQL, PostgreSQL, Datawarehouse)" -SourceAddress "10.0.0.0/16" -DestinationAddress Sql -DestinationPort 1433 -Protocol TCP
$ruleCollection = New-AzFirewallNetworkRuleCollection -Name "Data Collection" -Priority 1000 -Rule $rule -ActionType Allow
````

Successivamente, è necessario aggiornare la variabile contenente la definizione Firewall di Azure con le nuove regole di rete create.

````Merge the new rules into our existing Azure Firewall variable
$azFirewall.NetworkRuleCollections.add($ruleCollection)
`````

Infine, è necessario eseguire il commit delle modifiche della regola di rete nell'istanza Firewall di Azure in esecuzione.
````Commit the changes to Azure
Set-AzFirewall -AzureFirewall $azfirewall
````

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle regole di Firewall di Azure, vedere [Logica di elaborazione delle regole del Firewall di Azure](rule-processing.md).
