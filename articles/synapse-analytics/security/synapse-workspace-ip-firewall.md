---
title: Configurare regole del firewall per indirizzi IP
description: Articolo che illustra come configurare le regole del firewall IP in Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: b937dad6c3c8f5a5773ca7779493b41c905307b1
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99226506"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>Regola del firewall IP di Azure Synapse Analytics

Questo articolo illustra le regole del firewall IP e spiega come configurarle in Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>Regole del firewall IP

Le regole del firewall IP consentono di concedere o negare l'accesso all'area di lavoro di Synapse in base all'indirizzo IP di origine di ogni richiesta. È possibile configurare le regole del firewall IP per l'area di lavoro. Le regole del firewall IP configurate a livello di area di lavoro si applicano a tutti gli endpoint pubblici dell'area di lavoro (pool SQL dedicati, pool SQL serverless e sviluppo).

## <a name="create-and-manage-ip-firewall-rules"></a>Creare e gestire le regole del firewall IP

È possibile aggiungere regole del firewall IP a un'area di lavoro Synapse in due modi diversi. Per aggiungere un firewall IP all'area di lavoro, selezionare **Sicurezza + rete** e quindi **Consenti connessioni da tutti gli indirizzi IP** durante la creazione dell'area di lavoro.

![Screenshot che evidenzia il pulsante Sicurezza + rete.](./media/synpase-workspace-ip-firewall/ip-firewall-1.png)

![Configurazione IP dell'area di lavoro di Synapse nel portale di Azure.](./media/synpase-workspace-ip-firewall/ip-firewall-2.png)

È anche possibile aggiungere regole del firewall IP a un'area di lavoro Synapse dopo la creazione dell'area di lavoro. Selezionare **Firewall** in **Sicurezza** del portale di Azure. Per aggiungere una nuova regola del firewall IP, assegnarle un nome, un indirizzo IP iniziale e un indirizzo IP finale. Al termine, selezionare **Salva** .

![Configurazione IP dell'area di lavoro di Azure Synapse nel portale di Azure.](./media/synpase-workspace-ip-firewall/ip-firewall-3.png)

## <a name="connect-to-synapse-from-your-own-network"></a>Connettersi a Synapse dalla propria rete

Per connettersi all'area di lavoro di Synapse, usare Synapse Studio. È anche possibile usare SQL Server Management Studio (SSMS) per connettersi alle risorse SQL (pool SQL dedicati e pool SQL serverless) nell'area di lavoro.

Assicurarsi che il firewall nella rete e nel computer locale consenta la comunicazione TCP in uscita attraverso le porte TCP 80, 443 e 1443 per Synapse Studio.

È inoltre necessario consentire la comunicazione in uscita attraverso la porta UDP 53 per Synapse Studio. Per connettersi con strumenti quali SSMS e Power BI, è necessario consentire la comunicazione in uscita tramite la porta TCP 1433.

Il criterio di connessione SQL è impostato sul *valore predefinito* per l'area di lavoro. È possibile ottenere altre informazioni sugli indirizzi IP e sulle porte che i [client devono consentire le comunicazioni in uscita](../../azure-sql/database/connectivity-architecture.md#connection-policy).




## <a name="next-steps"></a>Passaggi successivi

Creare un'[area di lavoro di Azure Synapse](../quickstart-create-workspace.md)

Creare un'area di lavoro di Azure Synapse con una [rete virtuale dell'area di lavoro gestita](./synapse-workspace-managed-vnet.md)