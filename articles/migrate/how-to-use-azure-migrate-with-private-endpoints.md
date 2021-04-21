---
title: Uso Azure Migrate con endpoint privati
description: Usare Azure Migrate collegamento privato per individuare, valutare ed eseguire la migrazione usando il collegamento privato.
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/07/2020
ms.openlocfilehash: e4feaa8f1b30bfe31f4e645943f766b5736150b3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818369"
---
# <a name="using-azure-migrate-with-private-endpoints"></a>Uso Azure Migrate con endpoint privati  

Questo articolo descrive come usare Azure Migrate per individuare, valutare ed eseguire la migrazione di server in una rete privata usando il [collegamento privato di Azure.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) 

È possibile usare gli strumenti [Azure Migrate:](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-discovery-and-assessment-tool) Individuazione e valutazione e [Azure Migrate:](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) Migrazione server per connettersi privatamente e in modo sicuro al servizio Azure Migrate tramite un peering privato ExpressRoute o una connessione VPN da sito a sito usando il collegamento privato di Azure. 

Il metodo di connettività dell'endpoint privato è consigliato quando l'organizzazione deve accedere al servizio Azure Migrate e ad altre risorse di Azure senza attraversare le reti pubbliche. È anche possibile usare il supporto del collegamento privato per usare i circuiti di peering privato ExpressRoute esistenti per requisiti di larghezza di banda o latenza migliori. 

## <a name="support-requirements"></a>Requisiti per il supporto 

### <a name="required-permissions"></a>Autorizzazioni necessarie

**Autorizzazioni Collaboratore + Amministratore accesso utente** **o** Proprietario per la sottoscrizione. 

### <a name="supported-scenarios-and-tools"></a>Scenari e strumenti supportati

**Distribuzione** | **Dettagli** | **Strumenti** 
--- | --- | ---
**Individuazione e valutazione** | Eseguire un'individuazione e una valutazione senza agente e su larga scala dei server in esecuzione in qualsiasi piattaforma, ad esempio piattaforme hypervisor come [VMware vSphere](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware) o [Microsoft Hyper-V,](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v)cloud pubblici come [AWS](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws) o [GCP](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp)o persino bare metal [server](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical). | Azure Migrate: Individuazione e Valutazione  <br/> 
**Inventario software** | Individuare app, ruoli e funzionalità in esecuzione nelle macchine virtuali VMware. | Azure Migrate: Individuazione e Valutazione  
**Visualizzazione delle dipendenze** | Usare la funzionalità di analisi delle dipendenze per identificare e comprendere le dipendenze tra i server. <br/> [La visualizzazione delle dipendenze senza](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies-agentless) agente è supportata in modo nativo con Azure Migrate collegamento privato. <br/>[La visualizzazione delle dipendenze basata su agente](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) richiede la connettività Internet. [informazioni su](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) come usare endpoint privati per la visualizzazione delle dipendenze basate su agente. | Azure Migrate: Individuazione e Valutazione |
**Migrazione** | Eseguire migrazioni [Hyper-V](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) senza agente o usare l'approccio basato su agente per eseguire la migrazione di macchine virtuali [VMware,](./tutorial-migrate-vmware-agent.md)macchine virtuali [Hyper-V,](./tutorial-migrate-physical-virtual-machines.md)server [fisici,](./tutorial-migrate-physical-virtual-machines.md)macchine virtuali in esecuzione in [AWS,](./tutorial-migrate-aws-virtual-machines.md)macchine virtuali in esecuzione in [GCP](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)o macchine virtuali in esecuzione in un provider di virtualizzazione diverso. | Azure Migrate: Migrazione server
 
>[!Note]
>
> [Le migrazioni VMware](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) senza agente richiedono l'accesso a Internet o la connettività tramite il peering Microsoft ExperessRoute. <br/> [Informazioni su](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute) come usare gli endpoint privati per eseguire repliche tramite peering privato ExpressRoute o una connessione VPN da sito a sito(S2S).  <br/><br/> 
   
#### <a name="other-integrated-tools"></a>Altri strumenti integrati

Alcuni strumenti di migrazione potrebbero non essere in grado di caricare i dati di utilizzo nel progetto Azure Migrate se l'accesso alla rete pubblica è disabilitato. Il Azure Migrate deve essere configurato per consentire al traffico da tutte le reti di ricevere dati da altre offerte Microsoft o da fornitori di [software indipendenti esterni (ISV).](https://docs.microsoft.com/azure/migrate/migrate-services-overview#isv-integration) 


Per abilitare l'accesso alla rete pubblica per il  progetto Azure Migrate, passare alla pagina delle proprietà Azure Migrate nel portale di Azure, selezionare **No** e selezionare **Salva.**

![Diagramma che illustra come modificare la modalità di accesso alla rete.](./media/how-to-use-azure-migrate-with-private-endpoints/migration-project-properties.png)

### <a name="other-considerations"></a>Altre considerazioni   

**Considerazioni** | **Dettagli**
--- | --- 
**Prezzi** | Per informazioni sui prezzi, vedere Prezzi [dei BLOB di Azure e](https://azure.microsoft.com/pricing/details/storage/page-blobs/) Prezzi dei collegamenti privati di [Azure.](https://azure.microsoft.com/pricing/details/private-link/)  
**Requisiti della rete virtuale** | L'endpoint del gateway ExpressRoute/VPN deve risiedere nella rete virtuale selezionata o in una rete virtuale connessa. Potrebbero essere necessari circa 15 indirizzi IP nella rete virtuale.  

## <a name="create-a-project-with-private-endpoint-connectivity"></a>Creare un progetto con connettività dell'endpoint privato

Usare questo [articolo](https://docs.microsoft.com/azure/migrate/create-manage-projects#create-a-project-for-the-first-time) per configurare un nuovo Azure Migrate progetto. 

> [!Note]
> Non è possibile modificare il metodo di connettività in connettività dell'endpoint privato per i progetti Azure Migrate esistenti.

Nella sezione **Configurazione** avanzata specificare i dettagli seguenti per creare un endpoint privato per il Azure Migrate progetto.
- In **Metodo di connettività** scegliere **Endpoint privato.** 
- In **Disabilita l'accesso all'endpoint** pubblico mantenere l'impostazione **predefinita No.** Alcuni strumenti di migrazione potrebbero non essere in grado di caricare i dati di utilizzo nel progetto Azure Migrate se l'accesso alla rete pubblica è disabilitato. [Altre informazioni.](#other-integrated-tools)
- In **Sottoscrizione di rete virtuale** selezionare la sottoscrizione per la rete virtuale dell'endpoint privato. 
- In **Rete virtuale** selezionare la rete virtuale per l'endpoint privato. L Azure Migrate'appliance e altri componenti software che devono connettersi al progetto Azure Migrate devono essere in questa rete o in una rete virtuale connessa.
- In **Subnet** selezionare la subnet per l'endpoint privato. 

Selezionare **Crea**. Attendere alcuni minuti durante la distribuzione del progetto di Azure Migrate. Non chiudere questa pagina mentre è in corso la creazione del progetto.

![Crea progetto](./media/how-to-use-azure-migrate-with-private-endpoints/create-project.png)

    
Verrà creato un progetto di migrazione e verrà collegato un endpoint privato. 

## <a name="discover-and-assess-servers-for-migration-using-azure-private-link"></a>Individuare e valutare i server per la migrazione usando il collegamento privato di Azure 

### <a name="set-up-the-azure-migrate-appliance"></a>Configurare l'appliance di Azure Migrate 

1. In **Individua computer** Le macchine virtuali sono  >  **virtualizzate?** selezionare il tipo di server.
2. In **Genera Azure Migrate chiave di progetto** specificare un nome per l Azure Migrate appliance. 
3. Selezionare **Genera chiave** per creare le risorse di Azure necessarie. 

    > [!Important]
    > Non chiudere la pagina Individua macchine virtuali durante la creazione delle risorse.  
    - In questo passaggio Azure Migrate crea un insieme di credenziali delle chiavi, un account di archiviazione, un insieme di credenziali di Servizi di ripristino (solo per migrazioni VMware senza agente) e alcune risorse interne e collega un endpoint privato a ogni risorsa. Gli endpoint privati vengono creati nella rete virtuale selezionata durante la creazione del progetto.  
    - Dopo aver creato gli endpoint privati, i record di risorse DNS CNAME per le risorse Azure Migrate vengono aggiornati a un alias in un sottodominio con il prefisso "privatelink". Per impostazione predefinita, Azure Migrate crea anche una zona DNS privata corrispondente al sottodominio "privatelink" per ogni tipo di risorsa e inserisce record DNS A per gli endpoint privati associati. In questo modo Azure Migrate'appliance e altri componenti software che risiedono nella rete di origine per raggiungere gli endpoint delle risorse Azure Migrate sugli indirizzi IP privati.  
    - Azure Migrate abilita anche un'identità [gestita](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) per il progetto di migrazione e concede le autorizzazioni all'identità gestita per accedere in modo sicuro all'account di archiviazione.  

4. Dopo aver generato correttamente la chiave, copiare i dettagli della chiave per configurare e registrare l'appliance.   

#### <a name="download-the-appliance-installer-file"></a>Scaricare il file del programma di installazione dell'appliance  

> [!Note]
> Se si verificano problemi durante il download del file del programma di installazione dell'appliance, creare un caso di supporto.

Azure Migrate: l'individuazione e la valutazione usano un'appliance Azure Migrate leggera. L'appliance esegue l'individuazione del server e invia i metadati di configurazione e prestazioni del server Azure Migrate.

Per configurare l'appliance, scaricare il file compresso contenente lo script del programma di installazione dal portale. Copiare il file compresso nel server che ospiterà l'appliance. 

Assicurarsi che il server soddisfi i requisiti [hardware](https://docs.microsoft.com/azure/migrate/migrate-appliance) per lo scenario scelto (VMware/Hyper-V/Fisico o altro) e possa connettersi agli URL di Azure [necessari:](./migrate-appliance.md#public-cloud-urls-for-private-link-connectivity) [cloud](./migrate-appliance.md#government-cloud-urls-for-private-link-connectivity) pubblici e per enti pubblici.

Dopo aver scaricato il file compresso, eseguire lo script del programma di installazione per distribuire l'appliance.

#### <a name="run-the-script"></a>Eseguire lo script

1. Estrarre il file compresso in una cartella nel server che ospiterà l'appliance. 
2. Avviare PowerShell nel computer con privilegi di amministratore (con privilegi elevati).
3. Modificare la directory di PowerShell nella cartella contenente il contenuto estratto dal file compresso scaricato.
4. Eseguire lo script **AzureMigrateInstaller.ps1**, come indicato di seguito:

    ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1```
   
5. Al termine dell'esecuzione, lo script avvia Gestione configurazione appliance in modo che sia possibile configurare l'appliance. Se si verificano problemi, esaminare i log di script in C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log.

### <a name="configure-the-appliance-and-start-continuous-discovery"></a>Configurare l'appliance e avviare l'individuazione continua

Aprire un browser in qualsiasi computer in grado di connettersi al server dell'appliance e aprire l'URL di Gestione configurazione appliance: `https://appliance name or IP address: 44368` . In alternativa, è possibile aprire Gestione configurazione dal desktop del server dell'appliance selezionando il collegamento per gestione configurazione.

#### <a name="set-up-prerequisites"></a>Configurare i prerequisiti

1. Leggere le informazioni di terze parti e accettare le condizioni **di licenza**.    
 
2. In Configuration Manager > **Set up prerequisites**(Configura prerequisiti) eseguire le operazioni seguenti:
   - **Connettività:** l'appliance verifica l'accesso agli URL necessari. Se il server usa un proxy:
     - Selezionare **Configura proxy per specificare** l'indirizzo proxy o la porta di `http://ProxyIPAddress` `http://ProxyFQDN` ascolto.
     - Se il proxy richiede l'autenticazione, specificare le credenziali. È supportato solo il proxy HTTP.
     - Se si vuole, è possibile aggiungere un elenco di URL/indirizzi IP che devono ignorare il server proxy. Se si usa il peering privato ExpressRoute, assicurarsi di ignorare [questi URL.](https://docs.microsoft.com/azure/migrate/replicate-using-expressroute#configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations)
     - È necessario selezionare **Salva per** registrare la configurazione se sono stati aggiornati i dettagli del server proxy o sono stati aggiunti URL/indirizzi IP per ignorare il proxy.
     
        > [!Note]
        > Se viene visualizzato un errore con un collegamento aka.ms/* durante il controllo della connettività e non si vuole che l'appliance accedono a questo URL tramite Internet, è necessario disabilitare il servizio di aggiornamento automatico nell'appliance seguendo la procedura riportata di [**seguito.**](https://docs.microsoft.com/azure/migrate/migrate-appliance#turn-off-auto-update) Dopo la disabilitazione dell'aggiornamento automatico, il aka.ms/* di connettività URL verrà ignorato. 

   - **Time sync** (Sincronizzazione ora): Per il corretto funzionamento dell'individuazione delle macchine virtuali, l'ora dell'appliance deve essere sincronizzata con l'ora Internet.
   - **Install updates** (Installa aggiornamenti): l'appliance verifica che siano installati gli aggiornamenti più recenti. Al termine del controllo, è possibile selezionare Visualizza servizi **appliance** per visualizzare lo stato e le versioni dei servizi in esecuzione nel server appliance.
        > [!Note]
        > Se si è scelto di disabilitare il servizio di aggiornamento automatico nell'appliance, è possibile aggiornare i servizi dell'appliance manualmente per ottenere le versioni più recenti dei servizi seguendo la procedura [**riportata di seguito.**](https://docs.microsoft.com/azure/migrate/migrate-appliance#manually-update-an-older-version)
   - **Installare VDDK**: (necessario solo per _l'appliance VMware)_ L'appliance verifica che sia installato VMware vSphere Virtual Disk Development Kit (VDDK). Se non è già installato, scaricare VDDK 6.7 da VMware ed estrarre il contenuto dello ZIP scaricato nel percorso specificato nell'appliance, come indicato nelle **istruzioni di installazione**.

#### <a name="register-the-appliance-and-start-continuous-discovery"></a>Registrare l'appliance e avviare l'individuazione continua

Al termine del controllo dei prerequisiti, seguire questa procedura per registrare l'appliance e avviare l'individuazione continua per i rispettivi scenari: macchine virtuali [VMware,](https://docs.microsoft.com/azure/migrate/tutorial-discover-vmware#register-the-appliance-with-azure-migrate)macchine virtuali [Hyper-V,](https://docs.microsoft.com/azure/migrate/tutorial-discover-hyper-v#register-the-appliance-with-azure-migrate) [server](https://docs.microsoft.com/azure/migrate/tutorial-discover-physical#register-the-appliance-with-azure-migrate)fisici, macchine virtuali [AWS,](https://docs.microsoft.com/azure/migrate/tutorial-discover-aws#register-the-appliance-with-azure-migrate)macchine virtuali [GCP.](https://docs.microsoft.com/azure/migrate/tutorial-discover-gcp#register-the-appliance-with-azure-migrate)


>[!Note]
> Se si verificano problemi di risoluzione DNS durante la registrazione dell'appliance o al  momento dell'avvio dell'individuazione, assicurarsi che le risorse Azure Migrate create durante il passaggio Genera chiave nel portale siano raggiungibili dal server locale che ospita l'appliance Azure Migrate. [Altre informazioni su come verificare la connettività di rete.](#troubleshoot-network-connectivity)

### <a name="assess-your-servers-for-migration-to-azure"></a>Valutare i server per la migrazione ad Azure
Al termine dell'individuazione, valutare i server (macchine virtuali[VMware,](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm)macchine virtuali [Hyper-V,](https://docs.microsoft.com/azure/migrate/tutorial-assess-hyper-v)server [fisici,](https://docs.microsoft.com/azure/migrate/tutorial-assess-vmware-azure-vm) [VM AWS,](https://docs.microsoft.com/azure/migrate/tutorial-assess-aws)macchine virtuali [GCP)](https://docs.microsoft.com/azure/migrate/tutorial-assess-gcp)per la migrazione a macchine virtuali di Azure o a soluzione Azure VMware (AVS) usando lo strumento Azure Migrate: Individuazione e valutazione. 

È anche [possibile valutare](https://docs.microsoft.com/azure/migrate/tutorial-discover-import#prepare-the-csv) i computer locali con lo strumento Azure Migrate: Individuazione e valutazione usando un file con valori delimitati da virgole (CSV) importato.   

## <a name="migrate-servers-to-azure-using-azure-private-link"></a>Eseguire la migrazione di server ad Azure usando il collegamento privato di Azure

Le sezioni seguenti descrivono i passaggi necessari per usare Azure Migrate con [endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) per le migrazioni che usano il peering privato ExpressRoute o le connessioni VPN.  

Questo articolo illustra un percorso di distribuzione del modello di verifica per le repliche basate su agente per eseguire la migrazione di macchine virtuali [VMware,](./tutorial-migrate-vmware-agent.md)macchine virtuali [Hyper-V,](./tutorial-migrate-physical-virtual-machines.md)server [fisici,](./tutorial-migrate-physical-virtual-machines.md)macchine virtuali in esecuzione in [AWS,](./tutorial-migrate-aws-virtual-machines.md)macchine virtuali in esecuzione in [GCP](https://docs.microsoft.com/azure/migrate/tutorial-migrate-gcp-virtual-machines)o macchine virtuali in esecuzione in un provider di virtualizzazione diverso usando endpoint privati di Azure. È possibile usare un approccio simile per eseguire [migrazioni Hyper-V senza agente](https://docs.microsoft.com/azure/migrate/tutorial-migrate-hyper-v) tramite collegamento privato.

>[!Note]
>[Le migrazioni VMware](https://docs.microsoft.com/azure/migrate/tutorial-assess-physical) senza agente richiedono l'accesso a Internet o la connettività tramite il peering Microsoft ExperessRoute. 

### <a name="set-up-a-replication-appliance-for-migration"></a>Configurare un'appliance di replica per la migrazione 

Il diagramma seguente illustra il flusso di lavoro di replica basato su agente con endpoint privati che usano lo Azure Migrate: Server Migration Tool.  

![Architettura della replica](./media/how-to-use-azure-migrate-with-private-endpoints/replication-architecture.png)

Lo strumento usa un'appliance di replica per replicare i server in Azure. Usare questo articolo per [preparare e configurare un computer per l'appliance di replica. ](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#prepare-a-machine-for-the-replication-appliance)

Dopo aver configurato l'appliance di replica, usare le istruzioni seguenti per creare le risorse necessarie per la migrazione. 

1. In **Individua macchine virtuali** I computer sono  >  **virtualizzati?** selezionare Non **virtualizzato/Altro**.
2. In **Area di destinazione** selezionare e confermare l'area di Azure in cui si vuole eseguire la migrazione dei computer.
3. Selezionare **Crea risorse** per creare le risorse di Azure necessarie. Non chiudere la pagina durante la creazione delle risorse.   
    - Verrà creato un insieme di credenziali di Servizi di ripristino in background e verrà abilitata un'identità gestita per l'insieme di credenziali. Un insieme di credenziali di Servizi di ripristino è un'entità che contiene le informazioni di replica dei server e viene usata per attivare le operazioni di replica.  
    - Se il progetto Azure Migrate dispone di connettività dell'endpoint privato, viene creato un endpoint privato per l'insieme di credenziali di Servizi di ripristino. Vengono aggiunti cinque nomi privati completi (FQDN) all'endpoint privato, uno per ogni microservizio collegato all'insieme di credenziali di Servizi di ripristino.   
    - I cinque nomi di dominio sono formattati in questo modello: <br/> _{Vault-ID}-asr-pod01-{type}-. {target-geo-code}_. privatelink.siterecovery.windowsazure.com  
    - Per impostazione predefinita, Azure Migrate crea automaticamente una zona DNS privata e aggiunge record DNS A per i microservizi dell'insieme di credenziali di Servizi di ripristino. La zona DNS privata viene quindi collegata alla rete virtuale dell'endpoint privato. Ciò consente all'appliance di replica locale di risolvere i nomi di dominio completi nei relativi indirizzi IP privati.

4. Prima di registrare l'appliance di replica, assicurarsi che i fqdn di collegamento privato dell'insieme di credenziali siano raggiungibili dal computer che ospita l'appliance di replica. [Altre informazioni su come verificare la connettività di rete.](#troubleshoot-network-connectivity) 

5. Dopo aver verificato la connettività, scaricare il file di configurazione e chiave dell'appliance, eseguire il processo di installazione e registrare l'appliance Azure Migrate. Esaminare i [passaggi dettagliati qui.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#set-up-the-replication-appliance) Dopo aver configurato l'appliance di replica, seguire queste istruzioni per installare il servizio [Mobility](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#install-the-mobility-service) nei computer di cui si vuole eseguire la migrazione. 

### <a name="replicate-servers-to-azure-using-azure-private-link"></a>Replicare i server in Azure usando il collegamento privato di Azure 

A questo punto, [seguire questa procedura](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) per selezionare i server per la replica.  

In **Replica** impostazioni  >  **destinazione**  >  **Cache/Account** di archiviazione replica usare l'elenco a discesa per selezionare un account di archiviazione da replicare su un collegamento privato.  

Se il progetto Azure Migrate dispone di connettività [](#grant-access-permissions-to-the-recovery-services-vault) dell'endpoint privato, è necessario concedere le autorizzazioni all'identità gestita dell'insieme di credenziali di Servizi di ripristino per accedere all'account di archiviazione richiesto Azure Migrate.   

Inoltre, per abilitare le repliche su un collegamento privato, [creare un endpoint privato per l'account di archiviazione.](#create-a-private-endpoint-for-the-storage-account-optional)

#### <a name="grant-access-permissions-to-the-recovery-services-vault"></a>Concedere le autorizzazioni di accesso all'insieme di credenziali di Servizi di ripristino

L'identità gestita dell'insieme di credenziali di Servizi di ripristino richiede le autorizzazioni per l'accesso autenticato all'account di archiviazione della cache o della replica. 

Usare le indicazioni seguenti per identificare l'insieme di credenziali di Servizi di ripristino creato Azure Migrate e concedere le autorizzazioni necessarie. 

**_Identificare l'insieme di credenziali dei servizi di ripristino e l'ID oggetto identità gestita_**

È possibile trovare i dettagli dell'insieme di credenziali di Servizi di ripristino nella pagina Azure Migrate: Migrazione **del** server. 

1. Passare **all'hub Azure Migrate**, selezionare **Panoramica** nel riquadro Azure Migrate: Migrazione del server.

    ![Pagina Panoramica nell'hub Azure Migrate](./media/how-to-use-azure-migrate-with-private-endpoints/hub-overview.png)

2. Nel riquadro sinistro selezionare **Proprietà.** Prendere nota del nome dell'insieme di credenziali di Servizi di ripristino e dell'ID identità gestita. L'insieme di credenziali _avrà Un endpoint privato_ come tipo di **connettività e** _Altro_ come tipo **di replica**. Queste informazioni saranno necessarie per fornire l'accesso all'insieme di credenziali.
      
    ![Azure Migrate: Pagina delle proprietà Migrazione server](./media/how-to-use-azure-migrate-with-private-endpoints/vault-info.png)

**_Concedere le autorizzazioni necessarie per accedere all'account di archiviazione_**

 All'identità gestita dell'insieme di credenziali devono essere concesse le autorizzazioni del ruolo seguenti per l'account di archiviazione necessario per la replica.  In questo caso, è necessario creare in anticipo l'account di archiviazione.

>[!Note]
> Per eseguire la migrazione di macchine virtuali Hyper-V ad Azure tramite collegamento privato, è necessario concedere l'accesso sia all'account di archiviazione della replica che all'account di archiviazione della cache. 

Le autorizzazioni del ruolo variano a seconda del tipo di account di archiviazione.

- Resource Manager di archiviazione basati su account di archiviazione (tipo Standard):
  - [Collaboratore](../role-based-access-control/built-in-roles.md#contributor) _e_
  - [Collaboratore ai dati del BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Resource Manager di archiviazione basati su account di archiviazione (tipo Premium):
  - [Collaboratore](../role-based-access-control/built-in-roles.md#contributor) _e_
  - [Proprietario dei dati del BLOB di archiviazione](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)

1. Passare all'account di archiviazione replica/cache selezionato per la replica. Selezionare **Controllo di accesso (IAM)** nel riquadro sinistro. 

1. Nella sezione **Aggiungi un'assegnazione di ruolo** selezionare **Aggiungi**:

   ![Aggiungi un'assegnazione di ruolo](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment.png)


1. Nel campo **Ruolo della** pagina Aggiungi assegnazione **di** ruolo selezionare il ruolo appropriato nell'elenco di autorizzazioni indicato in precedenza. Immettere il nome dell'insieme di credenziali indicato in precedenza e selezionare **Salva**.

    ![Fornire l'accesso in base al ruolo](./media/how-to-use-azure-migrate-with-private-endpoints/storage-role-assignment-select-role.png)

4. Oltre a queste autorizzazioni, è necessario consentire anche l'accesso ai servizi attendibili Microsoft. Se l'accesso alla rete è limitato alle reti selezionate, selezionare Consenti  servizi Microsoft attendibili per accedere a questo **account** di archiviazione nella sezione Eccezioni della **scheda** Rete. 

![Consenti l'accesso servizi Microsoft per l'account di archiviazione](./media/how-to-use-azure-migrate-with-private-endpoints/exceptions.png)


### <a name="create-a-private-endpoint-for-the-storage-account-optional"></a>Creare un endpoint privato per l'account di archiviazione (facoltativo)

Per eseguire la replica usando ExpressRoute con peering privato, creare un [endpoint privato](https://docs.microsoft.com/azure/private-link/tutorial-private-endpoint-storage-portal#create-storage-account-with-a-private-endpoint) per gli account di archiviazione cache/replica (sottorisorsa di destinazione: **_BLOB_**). 

>[!Note]
>
> - È possibile creare endpoint privati solo in un account di archiviazione per utilizzo generico v2 (GPv2). Per informazioni sui prezzi, vedere [Prezzi dei BLOB di pagine di Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/) e Prezzi dei collegamenti privati di [Azure](https://azure.microsoft.com/pricing/details/private-link/)

L'endpoint privato per l'account di archiviazione deve essere creato nella stessa rete virtuale dell'endpoint privato del progetto Azure Migrate o in un'altra rete virtuale connessa a questa rete. 

Selezionare **Sì** ed eseguire l'integrazione con una zona DNS privata. La zona DNS privata consente di eseguire il routing delle connessioni dalla rete virtuale all'account di archiviazione tramite un collegamento privato. Se **si seleziona Sì,** la zona DNS viene collegata automaticamente alla rete virtuale e vengono aggiunti i record DNS per la risoluzione dei nuovi indirizzi IP e dei nomi di dominio completi creati. Altre informazioni sulle [zone DNS private.](https://docs.microsoft.com/azure/dns/private-dns-overview)

Se l'utente che crea l'endpoint privato è anche il proprietario dell'account di archiviazione, l'endpoint privato verrà approvato automaticamente. In caso contrario, il proprietario dell'account di archiviazione deve approvare l'endpoint privato per l'utilizzo. Per approvare o rifiutare una connessione all'endpoint privato richiesta, passare a **Connessioni endpoint privato** in **Rete** nella pagina dell'account di archiviazione.

Prima di procedere, esaminare lo stato della connessione all'endpoint privato. 

![Stato di approvazione dell'endpoint privato](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection-state.png)

Dopo aver creato l'endpoint privato, usare l'elenco a discesa **in** Replica impostazioni di destinazione Account di archiviazione della cache per selezionare l'account di archiviazione per la  >    >   replica tramite un collegamento privato.  

Assicurarsi che l'appliance di replica locale abbia la connettività di rete all'account di archiviazione nell'endpoint privato. [Altre informazioni su come verificare la connettività di rete.](#troubleshoot-network-connectivity)

>[!Note]
>
> - Per le migrazioni di macchine virtuali Hyper-V ad Azure, se l'account di archiviazione di replica è di tipo _Premium,_ è necessario selezionare un altro account di archiviazione di tipo _Standard_ per l'account di archiviazione della cache. In questo caso, è necessario creare endpoint privati sia per l'account di replica che per l'account di archiviazione della cache.  

Seguire quindi queste istruzioni per [esaminare e avviare la replica ed](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#replicate-machines) eseguire le [migrazioni](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#run-a-test-migration).  

## <a name="troubleshoot-network-connectivity"></a>Risolvere i problemi di connettività della rete 

### <a name="validate-private-endpoints-configuration"></a>Convalidare la configurazione degli endpoint privati 

Assicurarsi che l'endpoint privato sia uno stato approvato.  

1. Passare alla pagina Azure Migrate: Individuazione e valutazione e migrazione server.
2. La pagina delle proprietà contiene l'elenco di endpoint privati e FQDN di collegamento privato creati automaticamente da Azure Migrate.  

3. Selezionare l'endpoint privato che si vuole diagnosticare.  
    1. Verificare che lo stato della connessione sia Approvato.
    2. Se lo stato della connessione è In sospeso, è necessario ottenere l'approvazione.
    3. È anche possibile passare alla risorsa endpoint privato ed esaminare se la rete virtuale corrisponde alla rete virtuale dell'endpoint privato del progetto di migrazione. 

    ![Visualizzare la connessione all'endpoint privato](./media/how-to-use-azure-migrate-with-private-endpoints/private-endpoint-connection.png)

### <a name="verify-dns-resolution"></a>Verificare la risoluzione DNS 

L'appliance locale (o il provider di replica) accederà alle risorse Azure Migrate usando i nomi di dominio di collegamento privato completi (FQDN). Potrebbero essere necessarie impostazioni DNS aggiuntive per risolvere l'indirizzo IP privato degli endpoint privati dall'ambiente di origine. [Usare questo articolo per](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder) comprendere gli scenari di configurazione DNS che consentono di risolvere eventuali problemi di connettività di rete.  

Per convalidare la connessione di collegamento privato, eseguire una risoluzione DNS degli endpoint risorsa Azure Migrate (FQDN della risorsa collegamento privato) dal server locale che ospita l'appliance Migrate e assicurarsi che si risolva in un indirizzo IP privato. I dettagli dell'endpoint privato e le informazioni sugli FQDN delle risorse di collegamento privato sono disponibili nelle pagine delle proprietà Individuazione e Valutazione e Migrazione server. Selezionare **Scarica impostazioni DNS per** visualizzare l'elenco.   

 ![Azure Migrate: Proprietà di individuazione e valutazione](./media/how-to-use-azure-migrate-with-private-endpoints/server-assessment-properties.png)

 ![Azure Migrate: Proprietà di migrazione server](./media/how-to-use-azure-migrate-with-private-endpoints/azure-migrate-server-migration-properties.png)

Esempio illustrativo per la risoluzione DNS dell'FQDN del collegamento privato dell'account di archiviazione.  

- Immettere _nslookup<storage-account-name>_.blob.core.windows.net.  Sostituire <storage-account-name> con il nome dell'account di archiviazione usato per Azure Migrate.  

    Si riceverà un messaggio simile al seguente:  

   ![Esempio di risoluzione DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-resolution-example.png)

- Per l'account di archiviazione viene restituito un indirizzo IP privato 10.1.0.5. Questo indirizzo appartiene alla subnet di rete virtuale dell'endpoint privato.   

È possibile verificare la risoluzione DNS per altri Azure Migrate artefatti usando un approccio simile.   

Se la risoluzione DNS non è corretta, seguire questa procedura:  

- Se si usa un DNS personalizzato, esaminare le impostazioni DNS personalizzate e verificare che la configurazione DNS sia corretta. Per istruzioni, vedere [Panoramica dell'endpoint privato: Configurazione DNS.](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)
- Se si usano server DNS forniti da Azure, fare riferimento alla sezione seguente per altre informazioni sulla risoluzione dei problemi.  

> [!Tip]
> È possibile aggiornare manualmente i record DNS dell'ambiente di origine modificando il file host DNS nell'appliance locale con i nomi di dominio completo della risorsa collegamento privato e gli indirizzi IP privati associati. Questa opzione è consigliata solo per i test. <br/>  


### <a name="validate-the-private-dns-zone"></a>Convalidare la DNS privato locale   
Se la risoluzione DNS non funziona come descritto nella sezione precedente, potrebbe esserci un problema con la zona DNS privato rete.  

#### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Verificare che la risorsa DNS privato zona esista  
Per impostazione predefinita, Azure Migrate crea anche una zona DNS privata corrispondente al sottodominio "privatelink" per ogni tipo di risorsa. La zona DNS privato verrà creata nello stesso gruppo di risorse di Azure del gruppo di risorse dell'endpoint privato. Il gruppo di risorse di Azure deve contenere risorse di zona DNS privato con il formato seguente:
- privatelink.vaultcore.azure.net per l'insieme di credenziali delle chiavi 
- privatelink.blob.core.windows.net per l'account di archiviazione
- privatelink.siterecovery.windowsazure.com per l'insieme di credenziali dei servizi di ripristino (per le repliche basate su agente e Hyper-V)
- privatelink.prod.migration.windowsazure.com: eseguire la migrazione del progetto, del progetto di valutazione e del sito di individuazione.   

La zona DNS privata verrà creata automaticamente da Azure Migrate (ad eccezione dell'account di archiviazione della cache/replica selezionato dall'utente). È possibile individuare la zona DNS privato collegata passando alla pagina dell'endpoint privato e selezionando Configurazioni DNS. La zona DNS privato dovrebbe essere visualizzata nella sezione relativa all'integrazione DNS privato. 

![Screenshot della configurazione dns](./media/how-to-use-azure-migrate-with-private-endpoints/dns-configuration.png)  

Se la zona DNS non è presente (come illustrato di seguito), [creare una nuova risorsa DNS privato zona.](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal)  

![Creare una DNS privato locale](./media/how-to-use-azure-migrate-with-private-endpoints/create-dns-zone.png) 

#### <a name="confirm-that-the-private-dns-zone-is-linked-to-the-virtual-network"></a>Verificare che la DNS privato locale sia collegata alla rete virtuale  
La zona DNS privata deve essere collegata alla rete virtuale che contiene l'endpoint privato per la query DNS per risolvere l'indirizzo IP privato dell'endpoint della risorsa. Se la zona DNS privata non è collegata alla rete virtuale corretta, qualsiasi risoluzione DNS da tale rete virtuale ignorerà la zona DNS privata.   

Passare alla risorsa della zona DNS privata nel portale di Azure e selezionare i collegamenti di rete virtuale dal menu a sinistra. Dovrebbero essere disponibili le reti virtuali collegate.

![Visualizzare i collegamenti di rete virtuale](./media/how-to-use-azure-migrate-with-private-endpoints/virtual-network-links.png) 

Verrà visualizzato un elenco di collegamenti, ognuno con il nome di una rete virtuale nella sottoscrizione. La rete virtuale che contiene la risorsa Endpoint privato deve essere elencata qui. In caso [contrario, seguire questo articolo](https://docs.microsoft.com/azure/dns/private-dns-getstarted-portal#link-the-virtual-network) per collegare la zona DNS privata a una rete virtuale.    

Dopo che la zona DNS privata è collegata alla rete virtuale, le richieste DNS provenienti dalla rete virtuale cercaranno i record DNS nella zona DNS privata. Questa operazione è necessaria per la risoluzione corretta degli indirizzi nella rete virtuale in cui è stato creato l'endpoint privato.   

#### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-records"></a>Verificare che la zona DNS privata contenga i record A a destra 

Passare alla zona DNS privata di cui si vuole risolvere i problemi. La pagina Panoramica mostra tutti i record DNS per la zona DNS privata. Verificare che esista un record DNS A per la risorsa. Il valore del record A (l'indirizzo IP) deve essere l'indirizzo IP privato delle risorse. Se si trova il record A con l'indirizzo IP errato, è necessario rimuovere l'indirizzo IP errato e aggiungerne uno nuovo. È consigliabile rimuovere l'intero record A e aggiungerne uno nuovo ed eseguire uno scaricamento DNS nell'appliance di origine locale.   

Esempio illustrativo per il record DNS A dell'account di archiviazione nella zona DNS privata:

![Record DNS](./media/how-to-use-azure-migrate-with-private-endpoints/dns-a-records.png)   

Un esempio illustrativo per i microservizi dell'insieme di credenziali di Servizi di ripristino record DNS A nella zona DNS privato: 

![Record DNS per l'insieme di credenziali di Servizi di ripristino](./media/how-to-use-azure-migrate-with-private-endpoints/rsv-a-records.png)   

>[!Note]
> Quando si rimuove o si modifica un record A, il computer potrebbe comunque risolversi nell'indirizzo IP precedente perché il valore TTL (Time To Live) potrebbe non essere ancora scaduto.  

#### <a name="other-things-that-may-affect-private-link-connectivity"></a>Altri elementi che possono influire sulla connettività del collegamento privato  

Questo è un elenco non esaustivo di elementi disponibili in scenari avanzati o complessi: 

- Impostazioni del firewall, il Firewall di Azure connesso alla rete virtuale o una soluzione firewall personalizzata distribuita nel computer dell'appliance.  
- Peering di rete, che può influire sui server DNS usati e sulla modalità di instradamento del traffico.  
- Le soluzioni del gateway personalizzato (NAT) possono influire sul modo in cui viene instradato il traffico, incluso il traffico proveniente da query DNS.

Per altre informazioni, vedere la guida alla risoluzione [dei problemi di connettività degli endpoint privati.](https://docs.microsoft.com/azure/private-link/troubleshoot-private-endpoint-connectivity)  

## <a name="next-steps"></a>Passaggi successivi 
- [Completare il processo di migrazione](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#complete-the-migration) ed esaminare le procedure consigliate [post-migrazione.](https://docs.microsoft.com/azure/migrate/tutorial-migrate-physical-virtual-machines#post-migration-best-practices)