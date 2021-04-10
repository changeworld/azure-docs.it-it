---
title: Connettersi a un account di archiviazione protetto dall'area di lavoro della sinapsi di Azure
description: Questo articolo illustra come connettersi a un account di archiviazione sicuro dall'area di lavoro di Azure sinapsi
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5d43d6f56b48a34fa34baf727508ad8f1c151aa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674323"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Connettersi a un account di archiviazione di Azure sicuro dall'area di lavoro sinapsi

Questo articolo illustra come connettersi a un account di archiviazione di Azure sicuro dall'area di lavoro sinapsi di Azure. È possibile collegare un account di archiviazione di Azure all'area di lavoro sinapsi quando si crea l'area di lavoro. È possibile collegare più account di archiviazione dopo aver creato l'area di lavoro.


## <a name="secured-azure-storage-accounts"></a>Account di archiviazione di Azure protetti
Archiviazione di Azure offre un modello di sicurezza a più livelli che consente di proteggere e controllare l'accesso agli account di archiviazione. È possibile configurare le regole del firewall IP per concedere il traffico dagli intervalli di indirizzi IP pubblici selezionati per l'accesso all'account di archiviazione. È anche possibile configurare le regole di rete per concedere il traffico dalle reti virtuali selezionate per l'accesso all'account di archiviazione. È possibile combinare le regole del firewall IP che consentono l'accesso da intervalli di indirizzi IP selezionati e da regole di rete che concedono l'accesso da reti virtuali selezionate nello stesso account di archiviazione. Queste regole si applicano all'endpoint pubblico di un account di archiviazione. Non sono necessarie regole di accesso per consentire il traffico da endpoint privati gestiti creati nell'area di lavoro a un account di archiviazione. Le regole del firewall di archiviazione possono essere applicate agli account di archiviazione esistenti o ai nuovi account di archiviazione al momento della creazione. Per altre informazioni sulla protezione dell'account di archiviazione, vedere [qui](../../storage/common/storage-network-security.md).

## <a name="synapse-workspaces-and-virtual-networks"></a>Aree di lavoro sinapsi e reti virtuali
Quando si crea un'area di lavoro sinapsi, è possibile scegliere di abilitare l'associazione di una rete virtuale gestita. Se non si Abilita la rete virtuale gestita per l'area di lavoro durante la creazione, l'area di lavoro si trova in una rete virtuale condivisa insieme ad altre aree di lavoro sinapsi a cui non è associata una rete virtuale gestita. Se è stata abilitata la rete virtuale gestita quando è stata creata l'area di lavoro, l'area di lavoro è associata a una rete virtuale dedicata gestita da sinapsi di Azure. Queste reti virtuali non vengono create nella sottoscrizione del cliente. Non sarà quindi possibile concedere il traffico da tali reti virtuali all'account di archiviazione protetto usando le regole di rete descritte in precedenza.  

## <a name="access-a-secured-storage-account"></a>Accedere a un account di archiviazione protetto
Sinapsi opera da reti che non possono essere incluse nelle regole di rete. Per abilitare l'accesso dall'area di lavoro all'account di archiviazione protetto, è necessario eseguire le operazioni seguenti.

* Creare un'area di lavoro di Azure sinapsi con una rete virtuale gestita associata e creare endpoint privati gestiti dall'account di archiviazione protetto
* Concedere all'area di lavoro di Azure sinapsi l'accesso all'account di archiviazione sicuro come servizio di Azure attendibile. Come servizio attendibile, le sinapsi di Azure utilizzeranno l'autenticazione avanzata per connettersi in modo sicuro all'account di archiviazione.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>Creare un'area di lavoro sinapsi con una rete virtuale gestita e creare endpoint privati gestiti nell'account di archiviazione
È possibile seguire [questa procedura](./synapse-workspace-managed-vnet.md) per creare un'area di lavoro sinapsi a cui è associata una rete virtuale gestita. Una volta creata l'area di lavoro con una rete virtuale gestita associata, è possibile creare un endpoint privato gestito nell'account di archiviazione protetto attenendosi alla procedura riportata [qui](./how-to-create-managed-private-endpoints.md). 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Concedere all'area di lavoro di Azure sinapsi l'accesso all'account di archiviazione sicuro come servizio di Azure attendibile
Le funzionalità analitiche come il pool SQL dedicato e il pool SQL senza server usano un'infrastruttura multi-tenant non distribuita nella rete virtuale gestita. Per consentire al traffico di queste funzionalità di accedere all'account di archiviazione protetto, è necessario configurare l'accesso all'account di archiviazione in base all'identità gestita assegnata dal sistema dell'area di lavoro attenendosi alla procedura riportata di seguito.

In portale di Azure passare all'account di archiviazione protetto. Selezionare **rete** dal riquadro di spostamento a sinistra. Nella sezione **istanze di risorse** selezionare *Microsoft. sinapsi/Workspaces* come tipo di **risorsa** e immettere il nome dell'area di lavoro per il **nome dell'istanza**. Selezionare **Salva**.

![Configurazione di rete dell'account di archiviazione.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

A questo punto dovrebbe essere possibile accedere all'account di archiviazione protetto dall'area di lavoro.


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [rete virtuale dell'area di lavoro gestita](./synapse-workspace-managed-vnet.md).

Altre informazioni sugli [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md).