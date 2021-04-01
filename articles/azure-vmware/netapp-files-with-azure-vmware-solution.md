---
title: Azure NetApp Files con la soluzione VMware di Azure
description: Usare Azure NetApp Files con le macchine virtuali della soluzione VMware di Azure per eseguire la migrazione e sincronizzare i dati tra i server locali, le macchine virtuali della soluzione VMware di Azure e le infrastrutture cloud.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 2f2e8fdeb777e7e4b2b4e89c1bb36b51c3083257
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575439"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files con la soluzione VMware di Azure

Questo articolo illustra i passaggi per l'integrazione di Azure NetApp Files con i carichi di lavoro basati su soluzioni VMware di Azure. Il sistema operativo guest viene eseguito all'interno di macchine virtuali (VM) che accedono a volumi Azure NetApp Files. 

## <a name="azure-netapp-files-overview"></a>Panoramica di Azure NetApp Files

[Azure NetApp files](../azure-netapp-files/azure-netapp-files-introduction.md) è un servizio di Azure per la migrazione e l'esecuzione di carichi di lavoro di file aziendali più complessi nel cloud: database, SAP e applicazioni di calcolo ad alte prestazioni, senza modifiche al codice.

### <a name="features"></a>Funzionalità
(Servizi in cui vengono utilizzati Azure NetApp Files).

- **Connessioni Active Directory**: Azure NetApp Files supporta [Active Directory Domain Services e Azure Active Directory Domain Services](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use).

- **Protocollo di condivisione**: Azure NetApp Files supporta i protocolli SMB (Server Message Block) e NFS (Network File System). Questo supporto indica che i volumi possono essere montati nel client Linux ed è possibile eseguirne il mapping nel client Windows.

- **Soluzione VMware di Azure**: le condivisioni di Azure NetApp files possono essere montate dalle macchine virtuali create nell'ambiente della soluzione VMware di Azure.

Azure NetApp Files è disponibile in molte aree di Azure e supporta la replica tra aree. Per informazioni sui metodi di configurazione di Azure NetApp Files, vedere [gerarchia di archiviazione di Azure NetApp files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Architettura di riferimento

Il diagramma seguente illustra una connessione tramite Azure ExpressRoute a un cloud privato della soluzione VMware di Azure. L'ambiente della soluzione VMware di Azure accede alla condivisione Azure NetApp Files montata nelle VM della soluzione VMware di Azure.

![Diagramma che illustra i file NetApp per l'architettura della soluzione VMware di Azure.](media/net-app-files/net-app-files-topology.png)

Questo articolo illustra le istruzioni per la configurazione, il test e la verifica del volume Azure NetApp Files come una condivisione file per le macchine virtuali della soluzione VMware di Azure. In questo scenario è stato usato il protocollo NFS. Azure NetApp Files e la soluzione VMware di Azure vengono creati nella stessa area di Azure.

## <a name="prerequisites"></a>Prerequisiti 

> [!div class="checklist"]
> * Sottoscrizione di Azure con Azure NetApp Files abilitata
> * Subnet per Azure NetApp Files
> * VM Linux in una soluzione VMware di Azure
> * VM Windows in una soluzione VMware di Azure

## <a name="regions-supported"></a>Aree supportate

L'elenco delle aree supportate è reperibile in [prodotti di Azure in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all).

## <a name="verify-pre-configured-azure-netapp-files"></a>Verifica Azure NetApp Files preconfigurata 

Seguire le istruzioni dettagliate riportate negli articoli seguenti per creare e montare volumi Azure NetApp Files nelle VM della soluzione VMware di Azure.

- [Creare un account di NetApp](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Configurare un pool di capacità](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Creare un volume SMB per Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Creare un volume NFS per Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Delegare una subnet ad Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

La procedura seguente include la verifica della Azure NetApp Files preconfigurata creata in Azure Azure NetApp Files livello di servizio Premium.

1. Nel portale di Azure, in **archiviazione** selezionare **Azure NetApp files**. Viene visualizzato un elenco dei Azure NetApp Files configurati. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Screenshot che mostra l'elenco di Azure NetApp Files preconfigurati."::: 

2. Selezionare un account di file NetApp configurato per visualizzarne le impostazioni. Ad esempio, selezionare **Contoso-ANF2**. 

3. Selezionare **pool di capacità** per verificare il pool configurato. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Screenshot che illustra le opzioni per visualizzare i pool di capacità e i volumi di un account di file NetApp configurato.":::

    Viene visualizzata la pagina pool di capacità che mostra la capacità e il livello di servizio. In questo esempio, il pool di archiviazione è configurato come 4 TiB con un livello di servizio Premium.

4. Selezionare i **volumi** per visualizzare i volumi creati nel pool di capacità. (Vedere la schermata precedente).

5. Selezionare un volume per visualizzarne la configurazione.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Screenshot che mostra i volumi creati nel pool di capacità.":::

    Viene visualizzata una finestra che mostra i dettagli di configurazione del volume.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Screenshot che mostra i dettagli di configurazione di un volume.":::

    Si può notare che anfvolume ha una dimensione di 200 GiB e si trova nel pool di capacità anfpool1. Viene esportata come condivisione file NFS tramite 10.22.3.4:/ANFVOLUME. Un indirizzo IP privato della rete virtuale di Azure (VNet) è stato creato per Azure NetApp Files e il percorso NFS da montare nella macchina virtuale.

    Per informazioni sulle prestazioni del volume Azure NetApp Files per dimensione o "quota", vedere [considerazioni sulle prestazioni per Azure NetApp files](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Verificare il mapping delle condivisioni VM della soluzione VMware di Azure preconfigurata

Per rendere la condivisione di Azure NetApp Files accessibile alla VM della soluzione VMware di Azure, è necessario conoscere il mapping delle condivisioni SMB e NFS. Solo dopo la configurazione dei volumi SMB o NFS, è possibile montarli come descritto qui.

- Condivisione SMB: creare una connessione Active Directory prima di distribuire un volume SMB. I controller di dominio specificati devono essere accessibili dalla subnet delegata di Azure NetApp Files per una connessione corretta. Una volta configurato l'Active Directory all'interno dell'account Azure NetApp Files, questo verrà visualizzato come elemento selezionabile durante la creazione dei volumi SMB.

- Condivisione NFS: Azure NetApp Files contribuisce alla creazione dei volumi tramite NFS o il protocollo Dual (NFS e SMB). L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool. È possibile montare NFS sul server Linux usando le righe di comando o le voci/etc/fstab.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Casi d'uso di Azure NetApp Files con la soluzione VMware di Azure

Di seguito sono riportate alcune interessanti Azure NetApp Files casi d'uso. 
- Gestione dei profili Horizon
- Gestione dei profili Citrix
- Gestione dei profili Servizi Desktop remoto
- Condivisioni file in una soluzione VMware di Azure

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata analizzata l'integrazione di Azure NetApp Files con i carichi di lavoro della soluzione VMware di Azure, è possibile ottenere informazioni su:

- [Limiti delle risorse per Azure NetApp files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits).
- [Linee guida per la pianificazione di Azure NetApp files rete](../azure-netapp-files/azure-netapp-files-network-topologies.md).
- [Replica tra aree di volumi Azure NetApp files](../azure-netapp-files/cross-region-replication-introduction.md). 
- [Domande frequenti su Azure NetApp files](../azure-netapp-files/azure-netapp-files-faqs.md).
