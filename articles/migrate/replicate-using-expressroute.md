---
title: Replicare i dati su ExpressRoute con Azure Migrate migrazione del server
description: Come usare Azure ExpressRoute per la replica con Azure Migrate migrazione del server
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: 5dd27e4502ac70ef10f2623ed6dfb2f62de37f06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102448829"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Replicare i dati su ExpressRoute con Azure Migrate: migrazione del server

Questo articolo illustra come configurare [Azure migrate: migrazione del server](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) per replicare i dati in un circuito ExpressRoute durante la migrazione dei server in Azure.

## <a name="understand-azure-expressroute-circuits"></a>Informazioni sui circuiti ExpressRoute di Azure
Un circuito ExpressRoute (ER) connette l'infrastruttura locale a Microsoft tramite un provider di connettività. I circuiti ExpressRoute possono essere configurati per l'uso del peering privato, del peering Microsoft o di entrambi. Vedere l'articolo sui [circuiti e il peering di ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare) per altre informazioni sulle diverse opzioni di peering disponibili con ExpressRoute.

Lo strumento di migrazione del server di Azure Migrate consente di eseguire la migrazione di server e server locali da altri cloud alle macchine virtuali di Azure. Lo strumento funziona impostando un flusso di replica in corso per replicare i dati dai server di cui eseguire la migrazione a Managed disks nella sottoscrizione di Azure. Quando si è pronti per eseguire la migrazione dei server, i dati replicati in Azure vengono usati per eseguire la migrazione dei server.

I dati replicati dai server locali possono essere configurati per l'invio alla sottoscrizione di Azure tramite Internet (tramite una connessione crittografata protetta) o tramite una connessione ExpressRoute. Quando si ha un numero elevato di server di cui eseguire la migrazione, l'uso di ExpressRoute per la replica consente di migrare i server in modo più efficiente usando la larghezza di banda con provisioning disponibile con il circuito ExpressRoute.

In questo articolo si apprenderà come
> [!div class="checklist"]
>
> * Come replicare i dati usando un circuito ExpressRoute con peering privato.
> * Come replicare i dati usando un circuito ExpressRoute con il peering Microsoft.

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>Replicare i dati usando un circuito ExpressRoute con peering privato

> [!NOTE]
> La replica in un circuito di peering privato è attualmente supportata solo per la [migrazione senza agenti di macchine virtuali VMware in Azure](./tutorial-migrate-vmware.md). Il supporto per gli endpoint privati per altri [metodi di replica](./migrate-services-overview.md#azure-migrate-server-migration-tool) sarà presto disponibile.

Nel metodo senza agente per la migrazione di macchine virtuali VMware in Azure, l'appliance di Azure Migrate carica prima di tutto i dati di replica in un account di archiviazione (account di archiviazione della cache) nella sottoscrizione. I dati replicati dall'account di archiviazione della cache vengono quindi spostati nei dischi gestiti dalla replica nella sottoscrizione da parte del servizio Azure Migrate. Per usare un circuito di peering privato per la replica, è necessario creare e alleghi un endpoint privato all'account di archiviazione della cache. Gli endpoint privati usano uno o più indirizzi IP privati dalla rete virtuale (VNet), portando in modo efficace l'account di archiviazione nella VNet di Azure. L'endpoint privato consente all'appliance Azure Migrate di connettersi all'account di archiviazione della cache usando il peering privato di ExpressRoute e trasferire i dati direttamente nell'indirizzo IP privato. <br/>  

![Processo di replica](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - Oltre ai dati di replica, l'appliance Azure Migrate comunica con il servizio Azure Migrate per le attività del piano di controllo, inclusa l'orchestrazione della replica. La comunicazione del piano di controllo tra l'appliance Azure Migrate e il servizio Azure Migrate continua a essere eseguita su Internet sull'endpoint pubblico di Azure Migrate Service.
> - L'endpoint privato dell'account di archiviazione deve essere accessibile dalla rete in cui è distribuita la Azure Migrate Appliance.
> - DNS deve essere configurato per la risoluzione delle query DNS da parte dell'appliance Azure Migrate per l'endpoint del servizio BLOB dell'account di archiviazione della cache nell'indirizzo IP privato dell'endpoint privato associato all'account di archiviazione della cache.
> - L'account di archiviazione della cache deve essere accessibile sul relativo endpoint pubblico. Il servizio Azure Migrate usa l'endpoint pubblico dell'account di archiviazione della cache per spostare i dati dall'account di archiviazione ai dischi gestiti di replica. 


### <a name="1-pre-requisites"></a>1. prerequisiti

L'utente di Azure che crea l'endpoint privato deve avere le autorizzazioni seguenti per il gruppo di risorse e la rete virtuale in cui verrà creato l'endpoint privato.

**Caso d'uso** | **Autorizzazioni** 
--- | --- 
 Creare e gestire endpoint privati. | Microsoft. Network/privateEndpoint/Write/Action<br/>Microsoft. Network/privateEndpoint/Read/Action  
|Alleghi un endpoint privato a una VNet/subnet.<br/>Questa operazione è necessaria per la rete virtuale in cui verrà creato l'endpoint privato.| Microsoft. Network/virtualNetworks/subnet/join/azione Microsoft. Network/virtualNetworks/join/Action
|Collegare l'endpoint privato a un account di archiviazione. <br/>| Microsoft. Microsoft. storage/storageAccounts/privateEndpointConnectionApproval/Action <br/> Microsoft. Microsoft. storage/storageAccounts/privateEndpointConnections/Read
|Creare un'interfaccia di rete e aggiungerla a un gruppo di sicurezza di rete. | Microsoft.Network/networkInterfaces/read <br/> Microsoft. Network/networkInterfaces/subnet/scrittura <br/> Microsoft. Network/networkInterfaces/Subnets/Read<br/> Microsoft. Network/networkSecurityGroups/join/Action (facoltativo)
Creare e gestire le zone DNS private.| Ruolo Collaboratore zona DNS privato <br/> _Or_ <br/> Microsoft. Network/privateDnsZones/A/* <br/>  Microsoft. Network/privateDnsZones/scrivere Microsoft. Network/privateDnsZones/Read <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/Write <br/> Microsoft. Network/privateEndpoints/privateDnsZoneGroups/Read <br/> Microsoft. Network/privateDnsZones/virtualNetworkLinks/Write <br/>  Microsoft. Network/privateDnsZones/virtualNetworkLinks/Read <br/> Microsoft. Network/virtualNetworks/join/azione 

### <a name="2-identify-the-cache-storage-account"></a>2. identificare l'account di archiviazione della cache 
 
Azure Migrate crea automaticamente un account di archiviazione della cache quando si configura la replica (usando l'esperienza portale di Azure) per una macchina virtuale per la prima volta in un progetto Azure Migrate. L'account di archiviazione viene creato nella stessa sottoscrizione e nel gruppo di risorse in cui è stato creato il progetto Azure Migrate.

Per creare e individuare l'account di archiviazione:

1. Usare l'esperienza portale di Azure per Azure Migrate per replicare una o più macchine virtuali nel progetto.
2. Passare al gruppo di risorse del progetto Azure Migrate.
3. Individuare l'account di archiviazione della cache identificando il prefisso **"LSA"** nel nome dell'account di archiviazione.

![Visualizzazione del gruppo di risorse](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
>  
> Se si dispone di più di un account di archiviazione con il prefisso **"LSA"** nel gruppo di risorse, è possibile verificare l'account di archiviazione passando al menu impostazioni di replica e configurazione di destinazione per le macchine virtuali di replica nel progetto. <br/> 
> ![Panoramica delle impostazioni di replica](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3. aggiornare l'account di archiviazione della cache alla per utilizzo generico V2 

È possibile creare endpoint privati solo in un account di archiviazione per utilizzo generico V2 (GPv2). Se l'account di archiviazione della cache non è un account di archiviazione GPv2, eseguirne l'aggiornamento a GPv2 attenendosi alla procedura seguente:

1. Passare all'account di archiviazione.
2. Selezionare **Configurazione**.
3. In **tipo di account** selezionare **Aggiorna**.
4. In **Conferma aggiornamento** Digitare il nome dell'account.
5. Selezionare **Aggiorna** nella parte inferiore della pagina.

![Aggiorna account di archiviazione](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4. creare un endpoint privato per l'account di archiviazione

1. Passare all'account di archiviazione, selezionare **rete** dal menu a sinistra e selezionare la scheda **connessioni endpoint privato** .  
2. Selezionare **+ Endpoint privato**.

    a. Nella finestra **Crea un endpoint privato** selezionare la **sottoscrizione** e il **gruppo di risorse**. Specificare un nome per l'endpoint privato e selezionare l'area dell'account di archiviazione.  
    ![Finestra di configurazione PE](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. Nella scheda **risorsa** specificare il nome della **sottoscrizione** in cui si trova l'account di archiviazione. Scegliere **Microsoft. storage/storageAccounts** come **tipo di risorsa**. In **risorsa** specificare il nome dell'account di archiviazione per la replica di tipo GPv2. Selezionare **BLOB** come **risorsa secondaria di destinazione**.  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    c. Nella scheda **configurazione** selezionare la **rete virtuale** e la **subnet** per l'endpoint privato dell'account di archiviazione.  

    > [!Note]
    > La rete virtuale deve contenere l'endpoint del gateway ExpressRoute o deve essere connessa alla rete virtuale con il gateway ExpressRoute. 

    Nella sezione **integrazione DNS privato** selezionare **Sì** e integrare con una zona DNS privata. Se **si seleziona Sì** , la zona DNS viene collegata automaticamente alla rete virtuale selezionata e vengono aggiunti i record DNS necessari per la risoluzione DNS dei nuovi indirizzi IP e i nomi di dominio completi creati per l'endpoint privato. Altre informazioni sulle [zone DNS private.](https://docs.microsoft.com/azure/dns/private-dns-overview)

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. È anche possibile aggiungere **tag** per l'endpoint privato.  

    e. Continua a **esaminare + crea** una volta completata l'immissione dei dettagli. Al termine della convalida, selezionare **Crea** per creare l'endpoint privato.

    > [!Note]
    > Se l'utente che crea l'endpoint privato è anche il proprietario dell'account di archiviazione, l'endpoint privato verrà approvato automaticamente. In caso contrario, il proprietario deve approvare l'endpoint privato per l'utilizzo. 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>Creare zone DNS private e aggiungere i record DNS manualmente (facoltativo)

Se non è stata selezionata l'opzione per l'integrazione con una zona DNS privata al momento della creazione dell'endpoint privato, seguire la procedura descritta in questa sezione per creare manualmente una zona DNS privata. 

> [!Note]
> Se è stata selezionata l'opzione **Sì** per l'integrazione con una zona DNS privata, è possibile ignorare questa sezione. 

1. Creare una zona DNS privata. 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a. Nella pagina **zone DNS privato** fare clic sul pulsante **+ Aggiungi** per avviare la creazione di una nuova zona.  
    b. Nella pagina **Crea zona DNS privata** immettere i dettagli richiesti. Immettere il nome della zona DNS privata come _privatelink_. blob.Core.Windows.NET.  
    c. Passare alla scheda **Rivedi e crea** per esaminare e creare la zona DNS.   

2. Collegare la zona DNS privata alla rete virtuale.  

    La zona DNS privata creata in precedenza deve essere collegata alla rete virtuale a cui è collegato l'endpoint privato.

    a. Passare alla zona DNS privata creata nel passaggio precedente e passare a collegamenti reti virtuali sul lato sinistro della pagina. Selezionare il pulsante **+ Aggiungi** .   
    b. Immettere i dettagli necessari. I campi **sottoscrizione** e **rete virtuale** devono essere riempiti con i dettagli corrispondenti della rete virtuale a cui è collegato l'endpoint privato. Gli altri campi possono essere lasciati invariati.

3. Il passaggio successivo consiste nell'aggiungere record DNS alla zona DNS. Aggiungere una voce per il nome di dominio completo dell'account di archiviazione nella zona DNS privata.

    a. Passare alla zona DNS privata e passare alla sezione **Panoramica** sul lato sinistro della pagina. Selezionare **+** set di record per iniziare ad aggiungere i record.  

    b. Nella pagina **Aggiungi set di record** aggiungere una voce per il nome di dominio completo e l'indirizzo IP privato come record di tipo.

> [!Important]
> Potrebbero essere necessarie altre impostazioni DNS per risolvere l'indirizzo IP privato dell'endpoint privato dell'account di archiviazione dall'ambiente di origine. [Vedere questo articolo](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) per informazioni sulla configurazione DNS necessaria.

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>Replicare i dati usando un circuito ExpressRoute con il peering Microsoft

È possibile usare il peering Microsoft o un dominio di peering pubblico esistente (deprecato per le nuove connessioni ExpressRoute) per instradare il traffico di replica tramite un circuito ExpressRoute, come illustrato nel diagramma riportato di seguito.
![replicationwithmicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

Anche con i dati di replica che passano attraverso il circuito con peering Microsoft, è comunque necessaria la connettività Internet dal sito locale per altre comunicazioni (piano di controllo) con il servizio Azure Migrate. Sono disponibili alcuni URL aggiuntivi, che non sono raggiungibili tramite ExpressRoute, che l'host di replica/Hyper-V deve accedere per orchestrare il processo di replica. È possibile esaminare i requisiti dell'URL in base allo scenario di migrazione, alle [migrazioni senza agenti VMware](https://docs.microsoft.com/azure/migrate/migrate-appliance#public-cloud-urls) o alle [migrazioni basate su agenti](https://docs.microsoft.com/azure/migrate/migrate-replication-appliance).  

Se si usa un proxy nel sito locale e si vuole usare ExpressRoute per il traffico di replica, è necessario configurare un bypass proxy per gli URL pertinenti nell'appliance locale. 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Configurare le regole di bypass del proxy nel dispositivo Azure Migrate (per le migrazioni senza agenti VMware)

1. Accedere (desktop remoto) al dispositivo Azure Migrate.   
2. Aprire il file C:/ProgramData/MicrosoftAzure/config/appliance.jsusando il blocco note.
3. Nel file modificare la riga "EnableProxyBypassList": "false", in "EnableProxyBypassList": "true". Salvare le modifiche e riavviare l'appliance.
4. Dopo il riavvio, quando si apre Gestione configurazione Appliance, sarà possibile visualizzare l'opzione proxy bypass nell'interfaccia utente dell'app Web. Aggiungere gli URL seguenti all'elenco proxy bypass.   
    - . *. vault.azure.net
    - . *. servicebus.windows.net
    - . *. discoverysrv.windowsazure.com
    - . *. migration.windowsazure.com
    - . *. hypervrecoverymanager.windowsazure.com
    - . *. blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>Configurare le regole di bypass del proxy nell'appliance di replica (per le migrazioni basate su agenti)

Attenersi alla procedura seguente per configurare l'elenco proxy bypass nel server di configurazione e nei server di elaborazione:

1. [Scaricare lo strumento PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) per accedere al contesto utente del sistema.
2. Aprire Internet Explorer nel contesto utente del sistema eseguendo la riga di comando seguente PsExec-s-i "%programfiles%\Internet Explorer\iexplore.exe"
3. Aggiungere le impostazioni proxy in IE.
4. Nell'elenco di bypass aggiungere l'URL di archiviazione di Azure. *. blob. Core. Windows. NET.  

Le regole di bypass sopra risulteranno sicure che il traffico di replica possa passare attraverso ExpressRoute mentre la comunicazione di gestione può attraversare il proxy per Internet.  

Inoltre, è necessario annunciare le route nel filtro di route per le seguenti community BGP per rendere il traffico di replica Azure Migrate attraversare un circuito ExpressRoute anziché Internet.  

- Community BGP regionale per l'area di Azure di origine (area del progetto Azure Migrate)
- Community BGP regionale per l'area di Azure di destinazione (area per la migrazione)
- Community BGP per Azure Active Directory (12076:5060)

Altre informazioni sui [filtri di route](https://docs.microsoft.com/azure/expressroute/how-to-routefilter-portal) e l'elenco di [community BGP per ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp). 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [circuiti di ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings).
- Altre informazioni sui [domini di routing di ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare).
- Altre informazioni sugli [endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview).