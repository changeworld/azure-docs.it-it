---
title: Azure NetApp Files con soluzione Azure VMware
description: Usare Azure NetApp Files con soluzione Azure VMware per eseguire la migrazione e la sincronizzazione dei dati tra server locali, soluzione Azure VMware macchine virtuali e infrastrutture cloud.
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 09b9ba2ff6b95e12558b1ac49e401ce6dede4942
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870986"
---
# <a name="azure-netapp-files-with-azure-vmware-solution"></a>Azure NetApp Files con soluzione Azure VMware

In questo articolo verranno descritti i passaggi per l'integrazione di Azure NetApp Files con soluzione Azure VMware basati su server. Il sistema operativo guest verrà eseguito all'interno di macchine virtuali (VM) che accedono Azure NetApp Files volumi. 

## <a name="azure-netapp-files-overview"></a>Azure NetApp Files panoramica

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) è un servizio di Azure per la migrazione e l'esecuzione dei carichi di lavoro di file aziendali più impegnativi nel cloud: database, SAP e applicazioni di calcolo ad alte prestazioni, senza modifiche al codice.

### <a name="features"></a>Funzionalità
(Servizi in cui Azure NetApp Files vengono usati.

- **Connessioni di Active Directory:** Azure NetApp Files supporta Active Directory Domain Services [e Azure Active Directory Domain Services](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use).

- **Protocollo di** condivisione: Azure NetApp Files supporta i protocolli Server Message Block (SMB) e NFS (Network File System). Questo supporto significa che i volumi possono essere montati nel client Linux e possono essere mappati nel client Windows.

- **soluzione Azure VMware:** Azure NetApp Files le condivisioni possono essere montate da macchine virtuali create nell'ambiente soluzione Azure VMware locale.

Azure NetApp Files è disponibile in molte aree di Azure e supporta la replica tra aree. Per informazioni sui Azure NetApp Files di configurazione, vedere [Gerarchia di archiviazione Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

## <a name="reference-architecture"></a>Architettura di riferimento

Il diagramma seguente illustra una connessione tramite Azure ExpressRoute a un soluzione Azure VMware cloud privato. L soluzione Azure VMware aistrio accede alla condivisione Azure NetApp Files montata soluzione Azure VMware macchine virtuali.

![Diagramma che mostra NetApp Files per l soluzione Azure VMware app.](media/net-app-files/net-app-files-topology.png)

Questo articolo illustra le istruzioni per configurare, testare e verificare il volume Azure NetApp Files come condivisione file per soluzione Azure VMware macchine virtuali. In questo scenario è stato usato il protocollo NFS. Azure NetApp Files e soluzione Azure VMware vengono creati nella stessa area di Azure.

## <a name="prerequisites"></a>Prerequisiti 

> [!div class="checklist"]
> * Sottoscrizione di Azure con Azure NetApp Files abilitata
> * Subnet per Azure NetApp Files
> * Macchina virtuale Linux in soluzione Azure VMware
> * Macchine virtuali Windows in soluzione Azure VMware

## <a name="regions-supported"></a>Aree supportate

L'elenco delle aree supportate è disponibile in [Prodotti di Azure per area.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all)

## <a name="verify-pre-configured-azure-netapp-files"></a>Verificare la configurazione Azure NetApp Files 

Seguire le istruzioni dettagliate negli articoli seguenti per creare e montare volumi Azure NetApp Files in soluzione Azure VMware virtuali.

- [Creare un account di NetApp](../azure-netapp-files/azure-netapp-files-create-netapp-account.md)
- [Configurare un pool di capacità](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)
- [Creare un volume SMB per Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)
- [Creare un volume NFS per Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md)
- [Delegare una subnet ad Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

I passaggi seguenti includono la verifica del livello di servizio Azure NetApp Files preconfigurato creato in Azure Azure NetApp Files di servizio Premium.

1. Nel portale di Azure, in **ARCHIVIAZIONE,** selezionare **Azure NetApp Files**. Verrà visualizzato un elenco delle Azure NetApp Files configurate. 

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Screenshot che mostra l'elenco delle impostazioni preconfigur Azure NetApp Files."::: 

2. Selezionare un account NetApp Files configurato per visualizzarne le impostazioni. Ad esempio, selezionare **Contoso-anf2**. 

3. Selezionare **Pool di** capacità per verificare il pool configurato. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Screenshot che mostra le opzioni per visualizzare i pool di capacità e i volumi di un account NetApp Files configurato.":::

    Verrà visualizzata la pagina Pool di capacità con la capacità e il livello di servizio. In questo esempio il pool di archiviazione è configurato come 4 TiB con un livello di servizio Premium.

4. Selezionare **Volumi** per visualizzare i volumi creati nel pool di capacità. Vedere lo screenshot precedente.

5. Selezionare un volume per visualizzarne la configurazione.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Screenshot che mostra i volumi creati nel pool di capacità.":::

    Verrà visualizzata una finestra che mostra i dettagli di configurazione del volume.

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Screenshot che mostra i dettagli di configurazione di un volume.":::

    È possibile vedere che anfvolume ha dimensioni di 200 GiB ed è nel pool di capacità anfpool1. Viene esportata come condivisione file NFS tramite 10.22.3.4:/ANFVOLUME. È stato creato un indirizzo IP privato dalla rete virtuale di Azure per Azure NetApp Files percorso NFS da montare nella macchina virtuale.

    Per informazioni sulle prestazioni Azure NetApp Files volume in base alle dimensioni o alla "quota", vedere Considerazioni sulle prestazioni [per](../azure-netapp-files/azure-netapp-files-performance-considerations.md)Azure NetApp Files . 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Verificare il mapping di condivisione soluzione Azure VMware macchina virtuale preconfigurato

Per rendere la condivisione Azure NetApp Files accessibile alla macchina virtuale soluzione Azure VMware, è necessario comprendere il mapping di condivisione SMB e NFS. Solo dopo aver configurato i volumi SMB o NFS, è possibile montarli come documentato qui.

- Condivisione SMB: creare una connessione Active Directory prima di distribuire un volume SMB. I controller di dominio specificati devono essere accessibili dalla subnet delegata di Azure NetApp Files per una connessione riuscita. Dopo che Active Directory è stato configurato nell'account Azure NetApp Files, verrà visualizzato come elemento selezionabile durante la creazione di volumi SMB.

- Condivisione NFS: Azure NetApp Files contribuisce alla creazione dei volumi usando NFS o dual protocol (NFS e SMB). L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool. È possibile montare NFS nel server Linux usando le righe di comando o le voci /etc/fstab.

## <a name="use-cases-of-azure-netapp-files-with-azure-vmware-solution"></a>Casi d'uso Azure NetApp Files con soluzione Azure VMware

Di seguito sono riportati solo alcuni casi d'uso Azure NetApp Files accattivanti. 
- Gestione del profilo Horizon
- Gestione dei profili Citrix
- Servizi Desktop remoto gestione dei profili
- Condivisioni file in soluzione Azure VMware

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata trattata l'integrazione Azure NetApp Files con i carichi di lavoro soluzione Azure VMware, è possibile ottenere informazioni su:

- [Limitazioni delle risorse per Azure NetApp Files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)
- [Linee guida per la pianificazione della rete per Azure NetApp Files](../azure-netapp-files/azure-netapp-files-network-topologies.md)
- [Replica tra aree di Azure NetApp Files volumi](../azure-netapp-files/cross-region-replication-introduction.md) 
- [Domande frequenti su Azure NetApp Files](../azure-netapp-files/azure-netapp-files-faqs.md)
