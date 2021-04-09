---
title: Reimposta le credenziali vSphere per la soluzione VMware di Azure
description: Informazioni su come reimpostare le credenziali vSphere per il cloud privato della soluzione VMware di Azure e verificare che HCX Connector disponga delle credenziali di vSphere più recenti.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: 1376b6322250da506d32b8ced0a62ddbf60ba9f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587628"
---
# <a name="reset-vsphere-credentials-for-azure-vmware-solution"></a>Reimposta le credenziali vSphere per la soluzione VMware di Azure

Questo articolo illustra i passaggi per reimpostare le credenziali di server vCenter e NSX-T Manager per il cloud privato della soluzione VMware di Azure. Ciò consentirà di verificare che HCX Connector disponga delle credenziali server vCenter più recenti.

## <a name="reset-your-azure-vmware-solution-credentials"></a>Reimposta le credenziali della soluzione VMware di Azure

 Per prima cosa, è necessario reimpostare le credenziali dei componenti della soluzione VMare di Azure. Le credenziali di amministratore server vCenter CloudAdmin e NSX-T non scadono; Tuttavia, è possibile seguire questa procedura per generare nuove password per questi account.

> [!NOTE]
> Se si usano le credenziali di CloudAdmin per servizi connessi come HCX, vRealize agente di orchestrazione, vRealizae Operations Manager o VMware Horizon, le connessioni smetteranno di funzionare dopo l'aggiornamento della password.  Questi servizi devono essere arrestati prima di avviare la rotazione della password.  In caso contrario, è possibile che si verifichino blocchi temporanei negli account di amministratore vCenter CloudAdmin e NSX-T, in quanto questi servizi chiameranno continuamente usando le credenziali precedenti.  Per ulteriori informazioni sulla configurazione di account distinti per i servizi connessi, vedere [concetti relativi all'accesso e all'identità](https://docs.microsoft.com/azure/azure-vmware/concepts-identity).

1. Dal portale di Azure aprire una sessione di Azure Cloud Shell.

2. Eseguire il comando seguente per aggiornare la password di vCenter CloudAdmin.  Sarà necessario sostituire {SubscriptionID}, {ResourceGroup} e {PrivateCloudName} con i valori effettivi del cloud privato a cui appartiene l'account CloudAdmin.

```
az resource invoke-action --action rotateVcenterPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```
          
3. Eseguire il comando seguente per aggiornare la password di amministratore di NSX-T. Sarà necessario sostituire {SubscriptionID}, {ResourceGroup} e {PrivateCloudName} con i valori effettivi del cloud privato a cui appartiene l'account di amministratore NSX-T.

```
az resource invoke-action --action rotateNSXTPassword --ids "/subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroup}/providers/Microsoft.AVS/privateClouds/{PrivateCloudName}" --api-version "2020-07-17-preview"
```

## <a name="ensure-the-hcx-connector-has-your-latest-vcenter-server-credentials"></a>Verificare che HCX Connector disponga delle credenziali server vCenter più recenti

Ora che sono state reimpostate le credenziali, seguire questa procedura per assicurarsi che HCX Connector disponga delle credenziali aggiornate.

1. Dopo aver modificato la password, passare all'interfaccia Web di HCX Connector locale usando https://{IP dell'appliance connettore HCX}: 443. Assicurarsi di usare la porta 443. Accedere con le nuove credenziali.

2. Nel dashboard VMware HCX selezionare **associazione sito**.
    
    :::image type="content" source="media/reset-vsphere-credentials/hcx-site-pairing.png" alt-text="Screenshot del dashboard di VMware HCX con l'associazione dei siti evidenziata.":::
 
3. Selezionare la connessione corretta alla soluzione VMware di Azure (se ne è presente più di una) e selezionare **Modifica connessione**.
 
4. Fornire le nuove credenziali utente server vCenter CloudAdmin e selezionare **modifica**, che consente di salvare le credenziali. Il salvataggio dovrebbe essere visualizzato correttamente.

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver reimpostato le credenziali server vCenter e NSX-T Manager per la soluzione VMware di Azure, è possibile ottenere informazioni su:

- [Configurazione dei componenti di rete NSX nella soluzione VMware di Azure](configure-nsx-network-components-azure-portal.md).
- [Gestione del ciclo di vita delle macchine virtuali della soluzione VMware di Azure](lifecycle-management-of-azure-vmware-solution-vms.md).
- [Distribuzione del ripristino di emergenza di macchine virtuali con la soluzione VMware di Azure](disaster-recovery-for-virtual-machines.md).
