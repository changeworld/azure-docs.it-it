---
title: Introduzione alla visualizzazione Regole di sicurezza effettive in Azure Network Watcher | Microsoft Docs
description: Questa pagina offre una panoramica della funzionalità di visualizzazione Network Watcher - Regole di sicurezza effettive
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: 54f1ef8f135c16b841df55094327c6bc5be521be
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778607"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Introduzione alla visualizzazione Regole di sicurezza effettive in Azure Network Watcher

I gruppi di sicurezza di rete sono associati a un livello di subnet o a un livello di scheda di interfaccia di rete. Se associato a livello di subnet, si applica a tutte le istanze delle VM della subnet. La visualizzazione delle regole di sicurezza effettive restituisce tutti i gruppi di sicurezza di rete configurati e le regole associate a livello di scheda di interfaccia di rete e subnet per una macchina virtuale, fornendo informazioni dettagliate sulla configurazione. Vengono anche restituite le regole di sicurezza effettive per ogni scheda di interfaccia di rete in una VM. Usando la visualizzazione Regole di sicurezza effettive, è possibile valutare una macchina virtuale per le vulnerabilità di rete, ad esempio le porte aperte. È anche possibile verificare se il gruppo di sicurezza di rete funziona come previsto [confrontando le regole di sicurezza configurate e quelle approvate](network-watcher-nsg-auditing-powershell.md).

Un caso d'uso più esteso include la conformità alla sicurezza e il controllo della sicurezza. È possibile definire un set prescrittivo di regole di sicurezza come modello per la governance della sicurezza nell'organizzazione. Un controllo della conformità periodico può essere implementato in modo programmatico confrontando le regole prescrittive con le regole effettive per ogni VM della rete.

Nel portale le regole vengono visualizzate per ogni interfaccia di rete e raggruppate per connessioni in ingresso e in uscita. Si ottiene così una semplice visualizzazione delle regole applicate a una macchina virtuale. È disponibile un pulsante per il download per scaricare facilmente tutte le regole di sicurezza indipendentemente dalla scheda in un file CSV.

![Visualizzazione di un gruppo di sicurezza][1]

Le regole possono essere selezionate e si apre un nuovo pannello che visualizza il gruppo di sicurezza di rete e i prefissi di origine e di destinazione. Da questo pannello è possibile passare direttamente alla risorsa del gruppo di sicurezza di rete.

![Drill-down][2]

### <a name="next-steps"></a>Passaggi successivi

È anche possibile usare la *funzionalità Gruppi di sicurezza* effettivi tramite altri metodi elencati di seguito:
* [REST API](/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [PowerShell](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)
* [Interfaccia della riga di comando di Azure](/cli/azure/network/nic#az_network_nic_list_effective_nsg)

Per informazioni su come controllare le impostazioni del gruppo di sicurezza di rete, vedere [Audit Network Security Group settings with PowerShell](network-watcher-nsg-auditing-powershell.md) (Controllare le impostazioni di un gruppo di sicurezza di rete con PowerShell)

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png