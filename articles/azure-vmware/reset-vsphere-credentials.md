---
title: Reimposta le credenziali vSphere per la soluzione VMware di Azure
description: Informazioni su come reimpostare le credenziali vSphere per il cloud privato della soluzione VMware di Azure e verificare che HCX Connector disponga delle credenziali di vSphere più recenti.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: e5a15caa98a46b0ae75b68ee7b568dabdbf1956c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603196"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Reimposta le credenziali vSphere per la soluzione VMware di Azure

Questo articolo illustra i passaggi per reimpostare le credenziali di vSphere per il cloud privato della soluzione VMware di Azure. Ciò consentirà di verificare che il connettore HCX disponga delle credenziali vSphere più recenti.

## <a name="reset-your-vsphere-credentials"></a>Reimposta le credenziali di vSphere

 Per prima cosa, è necessario reimpostare le credenziali di vSphere. Le credenziali di amministratore di vCenter CloudAdmin e NSX-T non scadono; Tuttavia, è possibile seguire questa procedura per generare nuove password per questi account.

> [!NOTE]
> Se si usano le credenziali di CloudAdmin per servizi connessi come HCX, vCenter Orchestrator, vCloud Director o vRealize, le connessioni smetteranno di funzionare dopo l'aggiornamento della password.  Questi servizi devono essere arrestati prima di avviare la rotazione della password.  In caso contrario, è possibile che si verifichino blocchi temporanei negli account di amministratore vCenter CloudAdmin e NSX-T, in quanto questi servizi chiameranno continuamente usando le credenziali precedenti.  Per ulteriori informazioni sulla configurazione di account distinti per i servizi connessi, vedere [concetti relativi all'accesso e all'identità](https://docs.microsoft.com/azure/azure-vmware/concepts-identity).

1. Nel portale delle soluzioni VMware di Azure aprire una riga di comando.

2. Eseguire il comando seguente per aggiornare la password di vCenter CloudAdmin.  Sarà necessario sostituire {SubscriptionID}, {ResourceGroup} e {PrivateCloudName} con i valori effettivi del cloud privato a cui appartiene l'account CloudAdmin.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Eseguire il comando seguente per aggiornare la password di amministratore di NSX-T. Sarà necessario sostituire {SubscriptionID}, {ResourceGroup} e {PrivateCloudName} con i valori effettivi del cloud privato a cui appartiene l'account di amministratore NSX-T.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vsphere-credentials"></a>Verificare che HCX Connector abbia le credenziali vSphere più recenti

Ora che sono state reimpostate le credenziali, seguire questa procedura per assicurarsi che HCX Connector disponga delle credenziali aggiornate.

1. Dopo aver modificato la password, passare all'interfaccia Web di HCX Connector locale usando https://{IP dell'appliance connettore HCX}: 443. Assicurarsi di usare la porta 443. Accedere con le nuove credenziali.

2. Nel dashboard VMware HCX selezionare **associazione sito**.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Screenshot del dashboard di VMware HCX con l'associazione dei siti evidenziata.":::
 
3. Selezionare la connessione corretta ad AVS (se ne è presente più di una) e selezionare **Modifica connessione**.
 
4. Fornire le nuove credenziali vSphere e selezionare **Edit (modifica**) per salvare le credenziali. Il salvataggio dovrebbe essere visualizzato correttamente.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver reimpostato le credenziali vSphere per la soluzione VMware di Azure, è possibile ottenere informazioni su:

- [Configurazione dei componenti di rete NSX nella soluzione VMware di Azure](configure-nsx-network-components-azure-portal.md).
- [Gestione del ciclo di vita delle macchine virtuali della soluzione VMware di Azure](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Distribuzione del ripristino di emergenza di macchine virtuali con la soluzione VMware di Azure](disaster-recovery-for-virtual-machines.md).
