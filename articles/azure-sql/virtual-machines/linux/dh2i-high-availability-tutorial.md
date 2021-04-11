---
title: Configurare Always On gruppo di disponibilità con DH2i DxEnterprise in esecuzione su macchine virtuali di Azure basate su Linux
description: Usare DH2i DxEnterprise come gestione cluster per ottenere la disponibilità elevata con un gruppo di disponibilità in SQL Server in Linux macchine virtuali di Azure
ms.date: 03/04/2021
ms.service: virtual-machines-sql
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 56002aaa977b94b0fabee4f17343f483706eb77d
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449427"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>Esercitazione: configurare un gruppo di disponibilità a tre nodi Always On con DH2i DxEnterprise in esecuzione su macchine virtuali di Azure basate su Linux

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questa esercitazione illustra come configurare SQL Server gruppo di disponibilità Always On con DH2i DxEnterprise in esecuzione su macchine virtuali di Azure (VM) basate su Linux. 

Per ulteriori informazioni su DxEnterprise, vedere [DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/).

> [!NOTE]
> Microsoft offre supporto per lo spostamento dei dati, il gruppo di disponibilità e i componenti di SQL Server. Contattare DH2i per assistenza relativa alla documentazione del cluster DxEnterprise di DH2i per la gestione del cluster e del quorum.
 

L'esercitazione è costituita dai passaggi seguenti:

> [!div class="checklist"]
> * Installare SQL Server in tutte le macchine virtuali (VM) di Azure che faranno parte del gruppo di disponibilità.
> * Installare DxEnterprise in tutte le macchine virtuali e configurare il cluster DxEnterprise.
> * Creare gli host virtuali per fornire supporto per il failover e disponibilità elevata, aggiungere un gruppo di disponibilità e un database al gruppo di disponibilità.
> * Creare il servizio di bilanciamento del carico interno di Azure per il listener del gruppo di disponibilità (facoltativo).
> * Eseguire un failover manuale o automatico.

In questa esercitazione verrà configurato un cluster DxEnterprise usando l' [interfaccia utente del client DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/). Facoltativamente, è anche possibile configurare il cluster usando l'interfaccia della riga di comando [DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/) . Per questo esempio sono state usate quattro VM. Tre di queste macchine virtuali eseguono Ubuntu 18,04 e fanno parte del cluster a tre nodi. La quarta macchina virtuale esegue Windows 10 con lo strumento DxAdmin per gestire e configurare il cluster.

## <a name="prerequisites"></a>Prerequisiti

- Creare quattro macchine virtuali in Azure. Seguire la [Guida introduttiva: creare una macchina virtuale Linux in portale di Azure](../../../virtual-machines/linux/quick-create-portal.md) articolo per creare macchine virtuali basate su Linux. Analogamente, per creare la macchina virtuale basata su Windows, seguire la [Guida introduttiva: creare una macchina virtuale Windows nell'articolo portale di Azure](../../../virtual-machines/windows/quick-create-portal.md) .
- Installare .NET 3,1 in tutte le macchine virtuali basate su Linux che faranno parte del cluster. Seguire le istruzioni descritte in questo articolo [in base al](/dotnet/core/install/linux) sistema operativo Linux scelto.
- Sarà necessaria una licenza DxEnterprise valida con le funzionalità di gestione del gruppo di disponibilità abilitata. Per altre informazioni, vedere la [versione di valutazione gratuita di DxEnterprise](https://dh2i.com/trial/) su come ottenere una versione di valutazione gratuita.

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>Installare SQL Server in tutte le macchine virtuali di Azure che faranno parte del gruppo di disponibilità

In questa esercitazione viene configurato un cluster basato su Linux a tre nodi che esegue il gruppo di disponibilità. Seguire la documentazione per l' [installazione di SQL Server in Linux](/sql/linux/sql-server-linux-overview#install) in base alla scelta della piattaforma Linux. Si consiglia inoltre di installare gli [strumenti di SQL Server](/sql/linux/sql-server-linux-setup-tools) per questa esercitazione.
 
> [!NOTE]
> Verificare che il sistema operativo Linux scelto sia una distribuzione comune supportata da entrambi [DH2i DxEnterprise (vedere la sezione requisiti di sistema minimi)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) e [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms).
>
> In questo esempio si usa Ubuntu 18,04, che è supportato da DH2i DxEnterprise e Microsoft SQL Server.

Per questa esercitazione non verrà installato SQL Server nella macchina virtuale Windows, perché questo nodo non fa parte del cluster e viene usato solo per gestire il cluster con DxAdmin.

Dopo aver completato questo passaggio, è necessario disporre di SQL Server e [strumenti di SQL Server](/sql/linux/sql-server-linux-setup-tools) (facoltativamente) installati in tutte e tre le macchine virtuali basate su Linux che parteciperanno al gruppo di disponibilità.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>Installare DxEnterprise in tutte le VM e configurare il cluster

In questo passaggio verrà installato DH2i DxEnterprise per Linux nelle tre VM Linux. Nella tabella seguente viene descritto il ruolo svolto da ogni server nel cluster:

| Numero di macchine virtuali | Ruolo DxEnterprise DH2i | Ruolo della replica del gruppo di disponibilità Microsoft SQL Server |
|--|--|--|
| 1 | Nodo cluster-basato su Linux | Principale |
| 1 | Nodo cluster-basato su Linux | Commit sincrono secondario |
| 1 | Nodo cluster-basato su Linux | Commit sincrono secondario |
| 1 | Client DxAdmin | N/D |


Per installare DxEnterprise nei tre nodi basati su Linux, seguire la documentazione di DH2i DxEnterprise in base al sistema operativo Linux scelto. Installare DxEnterprise usando uno dei metodi elencati di seguito.

- **Ubuntu**
    - [Guida introduttiva di installazione del repository](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [Guida introduttiva estensione](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Guida introduttiva immagine del Marketplace](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [Guida introduttiva di installazione del repository](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [Guida introduttiva estensione](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Guida introduttiva immagine del Marketplace](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Per installare solo lo strumento client DxAdmin nella macchina virtuale Windows, seguire l' [interfaccia utente del client DxAdmin Guida introduttiva](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/).

Dopo questo passaggio, è necessario che il cluster DxEnterprise sia stato creato nelle VM Linux e che il client DxAdmin sia installato nel computer client Windows. 

> [!NOTE]
> È anche possibile creare un cluster a tre nodi in cui uno del nodo viene aggiunto come *modalità di sola configurazione*, come descritto [qui](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes) per abilitare il failover automatico. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>Creare gli host virtuali per fornire supporto per il failover e disponibilità elevata

In questo passaggio verrà creato un host virtuale, un gruppo di disponibilità e quindi verrà aggiunto un database usando l'interfaccia utente di DxAdmin.   

> [!NOTE]
> Durante questo passaggio, le istanze di SQL Server verranno riavviate per abilitare l'Always On. 

Connettersi al computer client Windows che esegue DxAdmin per connettersi al cluster creato nel passaggio precedente. Per abilitare Always On e creare l'host virtuale e il gruppo di disponibilità, seguire i passaggi descritti in [MSSQL Availability Groups with DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/) . 

> [!TIP]
> Prima di aggiungere i database, assicurarsi che il database venga creato e sottoposto a backup nell'istanza primaria di SQL Server.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>Creare il servizio di bilanciamento del carico interno di Azure per il listener (facoltativo)

In questo passaggio facoltativo è possibile creare e configurare il servizio di bilanciamento del carico di Azure che include gli indirizzi IP per i listener del gruppo di disponibilità. Per ulteriori informazioni su Azure Load Balancer, vedere [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md). Per configurare il listener del servizio di bilanciamento del carico di Azure e del gruppo di disponibilità usando DxAdmin, seguire la [Guida introduttiva Azure Load Balancer](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/)di DxEnterprise.

Dopo questo passaggio, è necessario avere un listener del gruppo di disponibilità creato e mappato al servizio di bilanciamento del carico interno di Azure.

## <a name="test-manual-or-automatic-failover"></a>Testare il failover manuale o automatico

Per il test di failover automatico, è possibile procedere e arrestare la replica primaria (spegnere la macchina virtuale dal portale di Azure). Questa operazione replica la mancata disponibilità improvvisa del nodo primario. Questo è il comportamento previsto:
- La gestione cluster alza di livello una delle repliche secondarie nel gruppo di disponibilità a replica primaria.
- La replica primaria in errore viene aggiunta automaticamente al cluster dopo il backup. La gestione cluster la alza di livello a replica secondaria.

 
È anche possibile eseguire un failover manuale attenendosi ai passaggi indicati di seguito:

1. Connettersi al cluster tramite DxAdmin   
1. Espandere l'host virtuale per il gruppo di disponibilità
1. Fare clic con il pulsante destro del mouse sul nodo di destinazione/replica secondaria e scegliere **Avvia hosting sul membro** per avviare il failover 

Per altre informazioni su altre operazioni all'interno di DxEnterprise, accedere alla [Guida dell'amministratore di DxEnterprise](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) e alla guida a [DxEnterprise DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [gruppi di disponibilità in Linux](/sql/linux/sql-server-linux-availability-group-overview)
- [Guida introduttiva: creare una macchina virtuale Linux in portale di Azure](../../../virtual-machines/linux/quick-create-portal.md)
- [Guida introduttiva: Creare una macchina virtuale di Windows nel portale di Azure](../../../virtual-machines/windows/quick-create-portal.md)
- [Piattaforme supportate per SQL Server 2019 in Linux](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)