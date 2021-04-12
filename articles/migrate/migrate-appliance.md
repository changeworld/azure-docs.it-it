---
title: Appliance Azure Migrate
description: Fornisce un riepilogo del supporto per Azure Migrate Appliance.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: b10d2e10f95470cadf67af762a0d7320bc09b7e0
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075696"
---
# <a name="azure-migrate-appliance"></a>Appliance Azure Migrate

Questo articolo riepiloga i prerequisiti e i requisiti di supporto per l'appliance di Azure Migrate.

## <a name="deployment-scenarios"></a>Scenari di distribuzione

L'appliance di Azure Migrate viene usata negli scenari seguenti.

**Scenario** | **Strumento** | **Utilizzato per**
--- | --- | ---
**Individuazione e valutazione dei server in esecuzione nell'ambiente VMware** | Azure Migrate: individuazione e valutazione | Individuare i server in esecuzione nell'ambiente VMware<br/><br/> Eseguire l'individuazione dell'inventario software installato, l'analisi delle dipendenze senza agenti e individuare SQL Server istanze e database.<br/><br/> Raccolta dei metadati delle prestazioni e della configurazione del server per le valutazioni.
**Migrazione senza agenti di server in esecuzione nell'ambiente VMware** | Azure Migrate: Migrazione del server | Individuare i server in esecuzione nell'ambiente VMware. <br/><br/> Replicare i server senza installare agenti su di essi.
**Individuazione e valutazione dei server in esecuzione nell'ambiente Hyper-V** | Azure Migrate: individuazione e valutazione | Individuare i server in esecuzione nell'ambiente Hyper-V.<br/><br/> Raccolta dei metadati delle prestazioni e della configurazione del server per le valutazioni.
**Individuazione e valutazione dei server fisici o virtualizzati in locale** |  Azure Migrate: individuazione e valutazione |  Individuare i server fisici o virtualizzati locali.<br/><br/> Raccolta dei metadati delle prestazioni e della configurazione del server per le valutazioni.

## <a name="deployment-methods"></a>Metodi di distribuzione

L'appliance può essere distribuita usando due metodi:

- Il dispositivo può essere distribuito usando un modello per i server in esecuzione nell'ambiente VMware o Hyper-V ([modello OVA per VMware](how-to-set-up-appliance-vmware.md) o [VHD per Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Se non si vuole usare un modello, è possibile distribuire l'appliance per l'ambiente VMware o Hyper-V usando uno [script del programma di installazione di PowerShell](deploy-appliance-script.md).
- In Azure per enti pubblici è necessario distribuire l'appliance usando uno script del programma di installazione di PowerShell. Vedere i passaggi della distribuzione [qui](deploy-appliance-script-government.md).
- Per i server fisici o virtualizzati in locale o in qualsiasi altro cloud, è sempre necessario distribuire l'appliance usando uno script del programma di installazione di PowerShell. Vedere i passaggi della distribuzione [qui](how-to-set-up-appliance-physical.md).
- I collegamenti di download sono disponibili nelle tabelle seguenti.

## <a name="appliance---vmware"></a>Appliance - VMware

La tabella seguente riepiloga i requisiti dell'appliance di Azure Migrate per VMware.

> [!Note]
> L'individuazione e la valutazione di SQL Server istanze e database in esecuzione nell'ambiente VMware sono ora in anteprima. Per provare questa funzionalità, usare [**questo collegamento**](https://aka.ms/AzureMigrate/SQL) per creare un progetto nell'area **Australia orientale**. Se si dispone già di un progetto nell'Australia orientale e si vuole provare questa funzionalità, assicurarsi di aver completato questi [**prerequisiti**](how-to-discover-sql-existing-project.md) nel portale.

**Requisito** | **VMware**
--- | ---
**Autorizzazioni** | Per accedere a Gestione configurazione Appliance localmente o in remoto, è necessario disporre di un account utente locale o di dominio con privilegi amministrativi nel server appliance.
**Servizi Appliance** | L'Appliance dispone dei servizi seguenti:<br/><br/> - **Gestione configurazione Appliance**: si tratta di un'applicazione Web che può essere configurata con i dettagli dell'origine per avviare l'individuazione e la valutazione dei server.<br/> - **Agente di individuazione VMware**: l'agente raccoglie i metadati di configurazione del server che possono essere usati per creare come valutazioni locali.<br/>- **Agente di valutazione VMware**: l'agente raccoglie i metadati delle prestazioni del server che possono essere usati per creare valutazioni basate sulle prestazioni.<br/>- **Servizio di aggiornamento automatico**: il servizio mantiene aggiornati tutti gli agenti in esecuzione nell'appliance. Viene eseguito automaticamente una volta ogni 24 ore.<br/>- **Agente DRA**: Orchestra la replica del server e coordina la comunicazione tra i server replicati e Azure. Usato solo quando si esegue la replica di server in Azure usando la migrazione senza agente.<br/>- **Gateway**: invia i dati replicati ad Azure. Usato solo quando si esegue la replica di server in Azure usando la migrazione senza agente.<br/>- **SQL Discovery and Assessment Agent**: Invia i metadati di configurazione e delle prestazioni di SQL Server istanze e database in Azure.
**Limiti di progetto** |  Un appliance può essere registrato solo con un singolo progetto.<br/> Un singolo progetto può avere più appliance registrate.
**Limiti dell'individuazione** | Un'appliance può individuare fino a 10.000 server in esecuzione in un server vCenter.<br/> Un'appliance può connettersi a un solo server vCenter.
**Distribuzione supportata** | Distribuire come nuovo server in esecuzione in server vCenter usando il modello OVA.<br/><br/> Distribuire in un server esistente che esegue Windows Server 2016 usando lo script del programma di installazione di PowerShell.
**Modello OVA** | Scarica da progetto o da [qui](https://go.microsoft.com/fwlink/?linkid=2140333)<br/><br/> Le dimensioni del download sono di 11,9 GB.<br/><br/> Il modello di appliance scaricato include una licenza di valutazione di Windows Server 2016 valida per 180 giorni.<br/>Se il periodo di valutazione è prossimo alla scadenza, è consigliabile scaricare e distribuire una nuova appliance usando il modello OVA oppure attivare la licenza del sistema operativo del server appliance.
**Verifica OVA** | [Verificare](tutorial-discover-vmware.md#verify-security) il modello OVA scaricato dal progetto controllando i valori hash.
**Script di PowerShell** | Vedere questo [articolo](./deploy-appliance-script.md#set-up-the-appliance-for-vmware) su come distribuire un dispositivo usando lo script del programma di installazione di PowerShell.<br/><br/> 
**Requisiti hardware e di rete** |  Il dispositivo deve essere eseguito in un server con Windows Server 2016, 32 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco e un Commuter virtuale esterno.<br/> L'appliance richiede l'accesso a Internet, direttamente o tramite un proxy.<br/><br/> Se si distribuisce l'appliance usando il modello OVA, è necessario disporre di risorse sufficienti nel server vCenter per creare un server che soddisfi i requisiti hardware.<br/><br/> Se si esegue l'appliance in un server esistente, assicurarsi che sia in esecuzione Windows Server 2016 e soddisfi i requisiti hardware.<br/>_Attualmente la distribuzione di appliance è supportata solo in Windows Server 2016._
**Requisiti di VMware** | Se si distribuisce l'appliance come server in server vCenter, è necessario distribuirla in un server vCenter che esegue 5,5, 6,0, 6,5 o 6,7 e un host ESXi che esegue la versione 5,5 o successiva.<br/><br/> 
**VDDK (migrazione senza agente)** | Per sfruttare l'appliance per la migrazione senza agenti dei server, è necessario installare il VMware vSphere VDDK nel server appliance.

## <a name="appliance---hyper-v"></a>Appliance - Hyper-V

**Requisito** | **Hyper-V**
--- | ---
**Autorizzazioni** | Per accedere a Gestione configurazione Appliance localmente o in remoto, è necessario disporre di un account utente locale o di dominio con privilegi amministrativi nel server appliance.
**Servizi Appliance** | L'Appliance dispone dei servizi seguenti:<br/><br/> - **Gestione configurazione Appliance**: si tratta di un'applicazione Web che può essere configurata con i dettagli dell'origine per avviare l'individuazione e la valutazione dei server.<br/> - **Agente di individuazione**: l'agente raccoglie i metadati di configurazione del server che possono essere usati per creare come valutazioni locali.<br/>- **Agente di valutazione**: l'agente raccoglie i metadati delle prestazioni del server che possono essere usati per creare valutazioni basate sulle prestazioni.<br/>- **Servizio di aggiornamento automatico**: il servizio mantiene aggiornati tutti gli agenti in esecuzione nell'appliance. Viene eseguito automaticamente una volta ogni 24 ore.
**Limiti di progetto** |  Un appliance può essere registrato solo con un singolo progetto.<br/> Un singolo progetto può avere più appliance registrate.
**Limiti dell'individuazione** | Un'appliance può individuare fino a 5000 server in esecuzione nell'ambiente Hyper-V.<br/> Un'appliance può connettersi al massimo con 300 host Hyper-V.
**Distribuzione supportata** | Distribuire come server in esecuzione in un host Hyper-V usando un modello VHD.<br/><br/> Distribuire in un server esistente che esegue Windows Server 2016 usando lo script del programma di installazione di PowerShell.
**Modello di disco rigido virtuale** | File zip che include un disco rigido virtuale. Scaricare dal progetto o da [qui](https://go.microsoft.com/fwlink/?linkid=2140422).<br/><br/> Le dimensioni del download sono di 8,91 GB.<br/><br/> Il modello di appliance scaricato include una licenza di valutazione di Windows Server 2016 valida per 180 giorni.<br/> Se il periodo di valutazione è prossimo alla scadenza, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo del server del dispositivo.
**Verifica del disco rigido virtuale** | [Verificare](tutorial-discover-hyper-v.md#verify-security) il modello VHD scaricato dal progetto controllando i valori hash.
**Script di PowerShell** | Vedere questo [articolo](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v) su come distribuire un dispositivo usando lo script del programma di installazione di PowerShell.<br/>
**Requisiti hardware e di rete**  |  Il dispositivo deve essere eseguito in un server con Windows Server 2016, 16 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco e un Commuter virtuale esterno.<br/> L'appliance deve avere un indirizzo IP statico o dinamico e richiede l'accesso a Internet, direttamente o tramite un proxy.<br/><br/> Se si esegue l'appliance come server in esecuzione in un host Hyper-V, è necessario disporre di risorse sufficienti nell'host per creare un server che soddisfi i requisiti hardware.<br/><br/> Se si esegue l'appliance in un server esistente, assicurarsi che sia in esecuzione Windows Server 2016 e soddisfi i requisiti hardware.<br/>_Attualmente la distribuzione di appliance è supportata solo in Windows Server 2016._
**Requisiti di Hyper-V** | Se si distribuisce il dispositivo con il modello VHD, il dispositivo fornito da Azure Migrate è la macchina virtuale Hyper-V versione 5,0.<br/><br/> L'host Hyper-V deve eseguire Windows Server 2012 R2 o una versione successiva.

## <a name="appliance---physical"></a>Appliance - Computer fisico

**Requisito** | **Computer fisico**
--- | ---
**Autorizzazioni** | Per accedere a Gestione configurazione Appliance localmente o in remoto, è necessario disporre di un account utente locale o di dominio con privilegi amministrativi nel server appliance.
**Servizi Appliance** | L'Appliance dispone dei servizi seguenti:<br/><br/> - **Gestione configurazione Appliance**: si tratta di un'applicazione Web che può essere configurata con i dettagli dell'origine per avviare l'individuazione e la valutazione dei server.<br/> - **Agente di individuazione**: l'agente raccoglie i metadati di configurazione del server che possono essere usati per creare come valutazioni locali.<br/>- **Agente di valutazione**: l'agente raccoglie i metadati delle prestazioni del server che possono essere usati per creare valutazioni basate sulle prestazioni.<br/>- **Servizio di aggiornamento automatico**: il servizio mantiene aggiornati tutti gli agenti in esecuzione nell'appliance. Viene eseguito automaticamente una volta ogni 24 ore.
**Limiti di progetto** |  Un appliance può essere registrato solo con un singolo progetto.<br/> Un singolo progetto può avere più appliance registrate.<br/>
**Limiti dell'individuazione** | Un'appliance può individuare fino a 1000 di server fisici.
**Distribuzione supportata** | Distribuire in un server esistente che esegue Windows Server 2016 usando lo script del programma di installazione di PowerShell.
**Script di PowerShell** | Scaricare lo script (AzureMigrateInstaller.ps1) in un file zip dal progetto o da [qui](https://go.microsoft.com/fwlink/?linkid=2140334). [Altre informazioni](tutorial-discover-physical.md)<br/><br/> Le dimensioni del download sono pari a 85,8 MB.
**Verifica script** | [Verificare](tutorial-discover-physical.md#verify-security) lo script del programma di installazione di PowerShell scaricato dal progetto controllando i valori hash.
**Requisiti hardware e di rete** |  Il dispositivo deve essere eseguito in un server con Windows Server 2016, 16 GB di RAM, 8 vCPU, circa 80 GB di spazio di archiviazione su disco.<br/> L'appliance deve avere un indirizzo IP statico o dinamico e richiede l'accesso a Internet, direttamente o tramite un proxy.<br/><br/> Se si esegue l'appliance in un server esistente, assicurarsi che sia in esecuzione Windows Server 2016 e soddisfi i requisiti hardware.<br/>_Attualmente la distribuzione di appliance è supportata solo in Windows Server 2016._

## <a name="url-access"></a>accesso con URL

L'appliance di Azure Migrate richiede la connettività a Internet.

- Quando si distribuisce l'appliance, Azure Migrate esegue un controllo della connettività agli URL richiesti.
- È necessario consentire l'accesso a tutti gli URL nell'elenco. Se si esegue solo la valutazione, è possibile ignorare gli URL contrassegnati come richiesti per la migrazione senza agenti di VMware.
- Se si usa un proxy basato su URL per eseguire la connessione a Internet, verificare che il proxy risolva tutti i record CNAME ricevuti durante la ricerca degli URL.

### <a name="public-cloud-urls"></a>URL del cloud pubblico

**URL** | **Dettagli**  
--- | --- |
*.portal.azure.com  | Passare al portale di Azure.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *. office.com | Accedere alla sottoscrizione di Azure.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Creare app Azure Active Directory (AD) affinché l'appliance comunichi con Azure Migrate.
management.azure.com | Creare app Azure AD per l'appliance per comunicare con l'Azure Migrate.
*.services.visualstudio.com | Caricare i log degli appliance usati per il monitoraggio interno.
*.vault.azure.net | Gestire i segreti in Azure Key Vault.<br/> Nota: assicurarsi che i server di cui eseguire la replica abbiano accesso a questo.
aka.ms/* | Consentire l'accesso a collegamenti aka; consente di scaricare e installare gli aggiornamenti più recenti per i servizi Appliance.
download.microsoft.com/download | Consenti i download dall'area download Microsoft.
*.servicebus.windows.net | Comunicazione tra l'appliance e il servizio Azure Migrate.
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Connettersi agli URL del servizio Azure Migrate.
*.hypervrecoverymanager.windowsazure.com | **Usato per la migrazione senza agente VMware**<br/><br/> Connettersi agli URL del servizio Azure Migrate.
*.blob.core.windows.net |  **Usato per la migrazione senza agente VMware**<br/><br/>Caricare i dati nella risorsa di archiviazione per la migrazione.

### <a name="government-cloud-urls"></a>URL del cloud per enti pubblici

**URL** | **Dettagli**  
--- | --- |
*.portal.azure.us  | Passare al portale di Azure.
graph.windows.net | Accedere alla sottoscrizione di Azure.
login.microsoftonline.us  | Creare app Azure Active Directory (AD) affinché l'appliance comunichi con Azure Migrate.
management.usgovcloudapi.net | Creare app Azure AD affinché l'appliance comunichi con il servizio Azure Migrate.
*.services.visualstudio.com | Caricare i log degli appliance usati per il monitoraggio interno.
*.vault.usgovcloudapi.net | Gestire i segreti in Azure Key Vault.
aka.ms/* | Consentire l'accesso a collegamenti aka; consente di scaricare e installare gli aggiornamenti più recenti per i servizi Appliance.
download.microsoft.com/download | Consenti i download dall'area download Microsoft.
*.servicebus.usgovcloudapi.net  | Comunicazione tra l'appliance e il servizio Azure Migrate.
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Connettersi agli URL del servizio Azure Migrate.
*.hypervrecoverymanager.windowsazure.us | **Usato per la migrazione senza agente VMware**<br/><br/> Connettersi agli URL del servizio Azure Migrate.
*.blob.core.usgovcloudapi.net  |  **Usato per la migrazione senza agente VMware**<br/><br/>Caricare i dati nella risorsa di archiviazione per la migrazione.
*.applicationinsights.us | Caricare i log degli appliance usati per il monitoraggio interno.

## <a name="collected-data---vmware"></a>Dati raccolti - VMware

L'appliance raccoglie i metadati di configurazione, i metadati delle prestazioni e i dati sulle dipendenze del server (se si usa l' [analisi delle dipendenze](concepts-dependency-visualization.md) senza agenti).

### <a name="metadata"></a>Metadati

I metadati individuati dal dispositivo Azure Migrate consentono di determinare se i server sono pronti per la migrazione ad Azure, i server di dimensioni appropriate, i costi dei piani e analizzare le dipendenze dell'applicazione. Microsoft non usa questi dati nei controlli di conformità delle licenze.

Ecco l'elenco completo dei metadati del server che il dispositivo raccoglie e Invia ad Azure.

**DATI** | **CONTATORE**
--- | ---
**Dettagli del server** |
ID del server | vm.Config.InstanceUuid
Nome server | vm.Config.Name
ID server vCenter | VMwareClient.Instance.Uuid
Descrizione server | vm.Summary.Config.Annotation
Nome prodotto con licenza | vm.Client.ServiceContent.About.LicenseProductName
Tipo di sistema operativo | vm.SummaryConfig.GuestFullName
Tipo di avvio | vm.Config.Firmware
Numero di core | vm.Config.Hardware.NumCPU
Memoria (MB) | vm.Config.Hardware.MemoryMB
Numero di dischi | vm.Config.Hardware.Device.ToList().FindAll(x => è VirtualDisk).count
Elenco dimensioni disco | vm.Config.Hardware.Device.ToList().FindAll(x => è VirtualDisk)
Elenco schede di rete | vm.Config.Hardware.Device.ToList().FindAll(x => è VirtualEthernet).count
Uso della CPU | cpu.usage.average
Utilizzo della memoria |mem.usage.average
**Dettagli del disco** |
Valore chiave disco | disk.Key
Numero di unità del disco | disk.UnitNumber
Valore chiave controller del disco | disk.ControllerKey.Value
Gigabyte con provisioning | virtualDisk.DeviceInfo.Summary
Nome del disco | Valore generato usando disk.UnitNumber, disk.Key, disk.ControllerKey.VAlue
Operazioni di lettura per secondo | virtualDisk.numberReadAveraged.average
Operazioni di scrittura per secondo | virtualDisk.numberWriteAveraged.average
Velocità effettiva di lettura (MB al secondo) | virtualDisk.read.average
Velocità effettiva di scrittura (MB al secondo) | virtualDisk.write.average
**Dettagli della scheda di interfaccia di rete** |
Nome scheda di rete | nic.Key
Indirizzo MAC | ((VirtualEthernetCard)nic).MacAddress
Indirizzi IPv4 | vm.Guest.Net
Indirizzi IPv6 | vm.Guest.Net
Velocità effettiva di lettura (MB al secondo) | net.received.average
Velocità effettiva di scrittura (MB al secondo) | net.transmitted.average
**Dettagli percorso di inventario** |
Nome | container.GetType().Name
Tipo di oggetto figlio | container.ChildType
Informazioni di riferimento | container.MoRef
Dettagli elemento padre | Container.Parent
Dettagli cartella per server | ((Folder)container).ChildEntity.Type
Dettagli Data Center per server | ((Datacenter)container).VmFolder
Dettagli del data center per ogni cartella host | ((Datacenter)container).HostFolder
Dettagli del cluster per host | ((ClusterComputeResource)container).Host
Dettagli host per server | ((HostSystem)container).VM

### <a name="performance-data"></a>Dati sulle prestazioni

Ecco i dati sulle prestazioni raccolti da un'appliance per un server in esecuzione su VMware e che invia ad Azure.

**Dati** | **Contatore** | **Impatto sulla valutazione**
--- | --- | ---
Uso della CPU | cpu.usage.average | Dimensioni/costi del server consigliati
Utilizzo della memoria | mem.usage.average | Dimensioni/costi del server consigliati
Velocità effettiva di lettura del disco (MB al secondo) | virtualDisk.read.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni del server
Velocità effettiva di scrittura del disco (MB al secondo) | virtualDisk.write.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni del server
Operazioni di lettura da disco al secondo | virtualDisk.numberReadAveraged.average | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni del server
Operazioni di scrittura su disco al secondo | virtualDisk.numberWriteAveraged.average  | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni del server
Velocità effettiva di lettura della scheda di interfaccia di rete (MB al secondo) | net.received.average | Calcolo delle dimensioni del server
Velocità effettiva di scrittura della scheda di interfaccia di rete (MB al secondo) | net.transmitted.average  |Calcolo delle dimensioni del server

### <a name="installed-software-inventory"></a>Inventario software installato

Il dispositivo raccoglie i dati sull'inventario software installato nei server.

#### <a name="windows-server-software-inventory-data"></a>Dati di inventario software di Windows Server

Ecco i dati di inventario software raccolti dal dispositivo da ogni server Windows individuato nell'ambiente VMware.

**Dati** | **Percorso del registro** | **Chiave**
--- | --- | ---
Nome dell'applicazione  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Versione  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion
Provider  | HKLM: \ Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM: \ Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Publisher

#### <a name="windows-server-features-data"></a>Dati delle funzionalità di Windows Server

Ecco i dati sulle funzionalità raccolti dal dispositivo da ogni server Windows individuato nell'ambiente VMware.

**Dati**  | **Cmdlet di PowerShell** | **Proprietà**
--- | --- | ---
Nome  | Get-WindowsFeature  | Nome
Feature Type | Get-WindowsFeature  | FeatureType
Padre  | Get-WindowsFeature  | Padre

#### <a name="sql-server-metadata"></a>Metadati di SQL Server

Ecco i dati SQL Server raccolti dal dispositivo da ogni server Windows individuato nell'ambiente VMware.

**Dati**  | **Percorso del registro**  | **Chiave**
--- | --- | ---
Nome  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL server\istanza Names\SQL  | installedInstance
Edizione  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Edizione
Service Pack  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | SP
Versione  | HKLM: \ SOFTWARE\Microsoft\Microsoft SQL Server \\ \<InstanceName> \setup  | Versione

#### <a name="windows-server-operating-system-data"></a>Dati del sistema operativo Windows Server

Ecco i dati del sistema operativo raccolti dal dispositivo da ogni server Windows individuato nell'ambiente VMware.

**Dati**  | **Classe WMI**  | **Proprietà della classe WMI**
--- | --- | ---
Nome  | Win32_operatingsystem  | Didascalia
Versione  | Win32_operatingsystem  | Versione
Architettura  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-server-software-inventory-data"></a>Dati inventario software server Linux

Ecco i dati di inventario software raccolti dal dispositivo da ciascun server Linux individuato nell'ambiente VMware. In base al sistema operativo del server, vengono eseguiti uno o più comandi.

**Dati**  | **Comandi**
--- | ---
Nome | rpm, dpkg-query, snap
Versione | rpm, dpkg-query, snap
Provider | rpm, dpkg-query, snap

#### <a name="linux-server-operating-system-data"></a>Dati del sistema operativo del server Linux

Ecco i dati del sistema operativo raccolti dal dispositivo da ciascun server Linux individuato nell'ambiente VMware.

**Dati**  | **Comandi**
--- | ---
Nome <br/> version | Raccolto da uno o più dei seguenti file:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version
Architettura | uname

### <a name="sql-server-instances-and-databases-data"></a>SQL Server le istanze e i dati dei database

Appliance raccoglie i dati in SQL Server istanze e database.

> [!Note]
> L'individuazione e la valutazione di SQL Server istanze e database in esecuzione nell'ambiente VMware sono ora in anteprima. Per provare questa funzionalità, usare [**questo collegamento**](https://aka.ms/AzureMigrate/SQL) per creare un progetto nell'area **Australia orientale**. Se si dispone già di un progetto nell'Australia orientale e si vuole provare questa funzionalità, assicurarsi di aver completato questi [**prerequisiti**](how-to-discover-sql-existing-project.md) nel portale.

#### <a name="sql-database-metadata"></a>Metadati del database SQL

**Metadati del database** | **Proprietà views/SQL Server**
--- | ---
Identificatore univoco del database | sys.databases
ID database definito dal server | sys.databases
Nome del database | sys.databases
Livello di compatibilità del database | sys.databases
Nome delle regole di confronto del database | sys.databases
Stato del database | sys.databases
Dimensioni del database (in MB) | sys.master_files
Lettera di unità del percorso contenente i file di dati | SERVERPROPERTY e Software\Microsoft\MSSQLServer\MSSQLServer
Elenco di file di database | sys. databases, sys.master_files
Service Broker è abilitato o meno | sys.databases
Il database è abilitato per la Change Data Capture | sys.databases

#### <a name="sql-server-metadata"></a>Metadati di SQL Server

**Metadati del server** | **Proprietà views/SQL Server**
--- | ---
Nome server |SERVERPROPERTY
Nome di dominio completo | Stringa di connessione derivata dall'individuazione delle applicazioni installate
ID installazione | sys.dm_server_registry
Versione del server | SERVERPROPERTY
Edizione server | SERVERPROPERTY
Piattaforma host server (Windows/Linux) | SERVERPROPERTY
Livello di prodotto del server (RTM SP CTP) | SERVERPROPERTY
Percorso di backup predefinito | SERVERPROPERTY
Percorso predefinito dei file di dati | SERVERPROPERTY e Software\Microsoft\MSSQLServer\MSSQLServer
Percorso predefinito dei file di log | SERVERPROPERTY e Software\Microsoft\MSSQLServer\MSSQLServer
No. di core nel server | sys.dm_os_schedulers, sys.dm_os_sys_info
Nome delle regole di confronto del server | SERVERPROPERTY
No. di core nel server con lo stato ONLINE visibile | sys.dm_os_schedulers
ID server univoco | sys.dm_server_registry
HA abilitato o meno | SERVERPROPERTY
Estensione pool di buffer abilitata o meno | sys.dm_os_buffer_pool_extension_configuration
Cluster di failover configurato o meno | SERVERPROPERTY
Server che usa solo la modalità di autenticazione di Windows | SERVERPROPERTY
Il server installa la polibase | SERVERPROPERTY
No. di CPU logiche nel sistema | sys.dm_server_registry, sys.dm_os_sys_info
Rapporto tra il numero di core logici o fisici esposti da un pacchetto del processore fisico | sys.dm_os_schedulers, sys.dm_os_sys_info
Nessuna CPU fisica nel sistema | sys.dm_os_schedulers, sys.dm_os_sys_info
Data e ora dell'ultimo avvio del server | sys.dm_server_registry
Uso massimo della memoria del server (in MB) | sys.dm_os_process_memory
Totale No. di utenti in tutti i database | sys. databases, sys. Logins
Dimensioni totali di tutti i database utente | sys.databases
Dimensioni del database temporaneo | sys.master_files, sys.configurations, sys.dm_os_sys_info
No. di account di accesso | sys. Logins
Elenco di server collegati | sys.servers
Elenco di processi di Agent | [msdb]. [dbo]. [sysjobs], [sys]. [syslogins], [msdb]. [dbo]. syscategories

### <a name="performance-metadata"></a>Metadati delle prestazioni

**Prestazioni** | **Proprietà views/SQL Server** | **Impatto valutazione**
--- | --- | ---
SQL Server utilizzo CPU| sys.dm_os_ring_buffers| Dimensioni SKU consigliate (dimensione CPU)
Conteggio CPU logiche SQL| sys.dm_os_sys_info| Dimensioni SKU consigliate (dimensione CPU)
Memoria fisica SQL in uso| sys.dm_os_process_memory| Non utilizzato
Percentuale di utilizzo della memoria SQL| sys.dm_os_process_memory | Non utilizzato
Utilizzo CPU database| sys.dm_exec_query_stats, sys.dm_exec_plan_attributes| Dimensioni SKU consigliate (dimensione CPU)
Memoria del database in uso (pool di buffer)| sys.dm_os_buffer_descriptors| Dimensioni SKU consigliate (dimensione memoria)
IO lettura/scrittura file| sys.dm_io_virtual_file_stats, sys.master_files| Dimensioni SKU consigliate (dimensione IO)
Numero di file di letture/scritture| sys.dm_io_virtual_file_stats, sys.master_files| Dimensioni SKU consigliate (dimensione velocità effettiva)
I/o file di blocco (MS)| sys.dm_io_virtual_file_stats, sys.master_files| Dimensioni SKU consigliate (dimensione latenza IO)
Dimensione del file| sys.master_files| Dimensioni SKU consigliate (dimensione di archiviazione)


### <a name="application-dependency-data"></a>Dati sulle dipendenze dell'applicazione

L'analisi delle dipendenze senza agenti raccoglie la connessione ed elabora i dati.

#### <a name="windows-server-dependencies-data"></a>Dati sulle dipendenze di Windows Server

Ecco i dati di connessione raccolti dal dispositivo da ogni server Windows, abilitati per l'analisi delle dipendenze senza agenti.

**Dati** | **Comandi**
--- | ---
Porta locale | netstat
Indirizzo IP locale | netstat
Porta remota | netstat
Indirizzo IP remoto | netstat
Stato di connessione TCP | netstat
ID di processo | netstat
Numero di connessioni attive | netstat

Ecco i dati di connessione raccolti dal dispositivo da ogni server Windows, abilitati per l'analisi delle dipendenze senza agenti.

**Dati** | **Classe WMI** | **Proprietà della classe WMI**
--- | --- | ---
Nome del processo | Win32_Process | ExecutablePath
Argomenti del processo | Win32_Process | CommandLine
Nome applicazione | Win32_Process | Parametro VersionInfo.ProductName della proprietà ExecutablePath

#### <a name="linux-server-dependencies-data"></a>Dati sulle dipendenze del server Linux

Ecco i dati di connessione raccolti dal dispositivo da ciascun server Linux, abilitati per l'analisi delle dipendenze senza agenti.

**Dati** | **Comandi**
--- | ---
Porta locale | netstat
Indirizzo IP locale | netstat
Porta remota | netstat
Indirizzo IP remoto | netstat
Stato di connessione TCP | netstat
Numero di connessioni attive | netstat
ID di processo  | netstat
Nome del processo | ps
Argomenti del processo | ps
Nome applicazione | dpkg o rpm

## <a name="collected-data---hyper-v"></a>Dati raccolti - Hyper-V

Il dispositivo raccoglie i metadati di configurazione e delle prestazioni dai server in esecuzione nell'ambiente Hyper-V.

### <a name="metadata"></a>Metadati
I metadati individuati dal dispositivo Azure Migrate consentono di determinare se i server sono pronti per la migrazione ad Azure, ai server di dimensioni appropriate e ai costi dei piani. Microsoft non usa questi dati nei controlli di conformità delle licenze.

Ecco l'elenco completo dei metadati del server che il dispositivo raccoglie e Invia ad Azure.

**Dati** | **Classe WMI** | **Proprietà della classe WMI**
--- | --- | ---
**Dettagli del server** | 
Numero di serie del BIOS | Msvm_BIOSElement | BIOSSerialNumber
Tipo di server (gen 1 o 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Nome visualizzato del server | Msvm_VirtualSystemSettingData | ElementName
Versione del server | Msvm_ProcessorSettingData | VirtualQuantity
Memoria (byte) | Msvm_MemorySettingData | VirtualQuantity
Memoria massima che può essere utilizzata dal server | Msvm_MemorySettingData | Limite
Memoria dinamica abilitata | Msvm_MemorySettingData | DynamicMemoryEnabled
Nome/versione/FQDN del sistema operativo | Msvm_KvpExchangeComponent | Dati nome GuestIntrinsicExchangeItems
Stato di alimentazione del server | Msvm_ComputerSystem | EnabledState
**Dettagli del disco** |
Identificatore del disco | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Tipo di disco rigido virtuale | Msvm_VirtualHardDiskSettingData | Type
Dimensioni del disco rigido virtuale | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Disco rigido virtuale padre | Msvm_VirtualHardDiskSettingData | ParentPath
**Dettagli della scheda di interfaccia di rete** |
Indirizzi IP (schede di interfaccia di rete sintetiche) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP abilitato (schede di interfaccia di rete sintetiche) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
ID scheda di interfaccia di rete (schede di interfaccia di rete sintetiche) | Msvm_SyntheticEthernetPortSettingData | InstanceID
Indirizzo MAC scheda di interfaccia di rete (schede di interfaccia di rete sintetiche) | Msvm_SyntheticEthernetPortSettingData | Indirizzo
ID scheda di interfaccia di rete (schede di interfaccia di rete legacy) | Dati MsvmEmulatedEthernetPortSetting | InstanceID
ID MAC scheda di interfaccia di rete (schede di interfaccia di rete legacy) | Dati MsvmEmulatedEthernetPortSetting | Indirizzo

### <a name="performance-data"></a>Dati sulle prestazioni

Ecco i dati sulle prestazioni del server che il dispositivo raccoglie e Invia ad Azure.

**Classe del contatore delle prestazioni** | **Contatore** | **Impatto sulla valutazione**
--- | --- | ---
Processore virtuale hypervisor Hyper-V | % tempo di esecuzione guest | Dimensioni/costi del server consigliati
Server memoria dinamica Hyper-V | Utilizzo elevato corrente (%)<br/> Memoria fisica visibile guest (MB) | Dimensioni/costi del server consigliati
Dispositivo di archiviazione virtuale Hyper-V | Byte letti al secondo | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni del server
Dispositivo di archiviazione virtuale Hyper-V | Byte scritti al secondo | Calcolo delle dimensioni del disco, dei costi di archiviazione e delle dimensioni del server
Scheda di rete virtuale Hyper-V | Byte ricevuti al secondo | Calcolo delle dimensioni del server
Scheda di rete virtuale Hyper-V | Byte inviati al secondo | Calcolo delle dimensioni del server

- L'utilizzo della CPU è la somma di tutti i dati di utilizzo, per tutti i processori virtuali collegati a un server.
- L'utilizzo della memoria è dato da (utilizzo elevato corrente * memoria fisica visibile guest)/100.
- I valori di utilizzo del disco e della rete vengono raccolti dai contatori delle prestazioni di Hyper-V elencati.

## <a name="collected-data---physical"></a>Dati raccolti - Computer fisico

Il dispositivo raccoglie i metadati di configurazione e delle prestazioni da server fisici o virtuali in esecuzione in locale.

### <a name="metadata"></a>Metadati

I metadati individuati dal dispositivo Azure Migrate consentono di determinare se i server sono pronti per la migrazione ad Azure, ai server di dimensioni appropriate e ai costi dei piani. Microsoft non usa questi dati nei controlli di conformità delle licenze.

### <a name="windows-server-metadata"></a>Metadati di Windows Server

Di seguito è riportato l'elenco completo dei metadati del server Windows raccolti dall'appliance e inviati ad Azure.

**Dati** | **Classe WMI** | **Proprietà della classe WMI**
--- | --- | ---
Nome di dominio completo | Win32_ComputerSystem | Domain, Name, PartOfDomain
Numero di core del processore | Win32_Processor | NumberOfCores
Memoria allocata | Win32_ComputerSystem | TotalPhysicalMemory
Numero di serie del BIOS | Win32_ComputerSystemProduct | IdentifyingNumber
GUID BIOS | Win32_ComputerSystemProduct | UUID
Tipo di avvio | Win32_DiskPartition | Verificare la partizione con Type = **GPT:System** per EFI/BIOS
Nome del sistema operativo | Win32_OperatingSystem | Sottotitolo
Versione del sistema operativo |Win32_OperatingSystem | Versione
Architettura del sistema operativo | Win32_OperatingSystem | OSArchitecture
Numero di dischi | Win32_DiskDrive | Model, Size, DeviceID, MediaType, Name
Dimensioni disco | Win32_DiskDrive | Dimensione
Elenco delle schede di interfaccia di rete | Win32_NetworkAdapterConfiguration | Description, Index
Indirizzo IP della scheda di interfaccia di rete | Win32_NetworkAdapterConfiguration | IPAddress
Indirizzo MAC della scheda di interfaccia di rete | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-server-metadata"></a>Metadati del server Linux

Di seguito è riportato l'elenco completo dei metadati del server Linux raccolti dall'appliance e inviati ad Azure.

**Dati** | **Comandi**
--- | ---
Nome di dominio completo | cat /proc/sys/kernel/hostname, hostname -f
Numero di core del processore |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
Memoria allocata | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
Numero di serie del BIOS | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
GUID BIOS | cat /sys/class/dmi/id/product_uuid
Tipo di avvio | [ -d /sys/firmware/efi ] && echo EFI \|\| echo BIOS
Nome/versione del sistema operativo | Per la versione e il nome del sistema operativo si accede a questi file:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Architettura del sistema operativo | Uname -m
Numero di dischi | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Disco di avvio | df /boot \| sed -n 2p \| awk '{print $1}'
Dimensioni disco | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
Elenco delle schede di interfaccia di rete | ip -o -4 addr show \| awk '{print $2}'
Indirizzo IP della scheda di interfaccia di rete | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
Indirizzo MAC della scheda di interfaccia di rete | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Dati sulle prestazioni di Windows

Di seguito sono riportati i dati sulle prestazioni del server Windows raccolti dall'appliance e inviati ad Azure.

**Dati** | **Classe WMI** | **Proprietà della classe WMI**
--- | --- | ---
Utilizzo di CPU | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Utilizzo della memoria | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
Numero di schede di interfaccia di rete | Win32_PerfFormattedData_Tcpip_NetworkInterface | Ottenere il numero di dispositivi di rete.
Dati ricevuti per ogni scheda di interfaccia di rete | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Dati trasmessi per ogni scheda di interfaccia di rete | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Numero di dischi | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Numero di dischi
Dettagli del disco | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Dati sulle prestazioni di Linux

Di seguito sono riportati i dati sulle prestazioni del server Linux raccolti dall'appliance e inviati ad Azure.

| **Dati** | **Comandi** |
| --- | --- |
| Utilizzo della CPU | /proc/stat cat/proc/stat/ \| grep ' CPU ' |
| Utilizzo della memoria | free \| grep Mem \| awk '{print $3/$2 * 100.0}' |
| Numero di schede di interfaccia di rete | lshw -class network \| grep eth[0-60] \| wc -l |
| Dati ricevuti per ogni scheda di interfaccia di rete | cat /sys/class/net/eth$nic/statistics/rx_bytes |
| Dati trasmessi per ogni scheda di interfaccia di rete | cat /sys/class/net/eth$nic/statistics/tx_bytes |
| Numero di dischi | Disco di fdisk-l \| egrep ". \* byte ' \| awk ' {print $2}' \| Cut-F1-d':' |
| Dettagli del disco | cat /proc/diskstats |

## <a name="appliance-upgrades"></a>Aggiornamenti dell'appliance

Il dispositivo viene aggiornato quando vengono aggiornati i servizi Azure Migrate in esecuzione nell'appliance. Questa operazione viene eseguita in modo automatico, perché l'aggiornamento automatico è abilitato per impostazione predefinita nell'appliance. È possibile modificare questa impostazione predefinita per aggiornare manualmente i servizi dell'appliance.

### <a name="turn-off-auto-update"></a>Disattivare l'aggiornamento automatico

1. Nel server che esegue l'appliance, aprire l'editor del registro di sistema.
2. Passare a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Per disabilitare l'aggiornamento automatico, creare una chiave del Registro di sistema **AutoUpdate** impostando il valore DWORD su 0.

    ![Impostare la chiave del Registro di sistema](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Attivare l'aggiornamento automatico

È possibile attivare l'aggiornamento automatico usando uno di questi metodi:

- Eliminando la chiave del Registro di sistema AutoUpdate da HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance.
- Fare clic su **Visualizza servizi Appliance** dall'ultimo controllo aggiornamenti nel pannello **Configura prerequisiti** per attivare l'aggiornamento automatico.

Per eliminare la chiave del Registro di sistema:

1. Nel server che esegue l'appliance, aprire l'editor del registro di sistema.
2. Passare a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Eliminare la chiave del Registro di sistema **AutoUpdate**, creata in precedenza per disattivare l'aggiornamento automatico.

Per attivare l'aggiornamento automatico nel Configuration Manager dell'appliance, al termine dell'individuazione:

1. In Gestione configurazione Appliance passare a Configura il pannello dei **prerequisiti**
2. Nel controllo aggiornamenti più recenti fare clic su **Visualizza servizi Appliance** e fare clic sul collegamento per attivare l'aggiornamento automatico.

    ![Attivare l'aggiornamento automatico](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Verificare la versione dei servizi dell'appliance

È possibile verificare la versione dei servizi dell'appliance usando uno di questi metodi:

- In Gestione configurazione Appliance passare a **Configura il** pannello dei prerequisiti.
- Nel dispositivo, nel pannello di **controllo**  >  **programmi e funzionalità**.

Per archiviare Gestione configurazione Appliance:

1. In Gestione configurazione Appliance passare a Configura il pannello dei **prerequisiti**
2. Nel controllo aggiornamenti più recenti fare clic su **Visualizza servizi Appliance**.

    ![Controllare la versione](./media/migrate-appliance/versions.png)

Per verificare nel Pannello di controllo:

1. Nell'appliance fare clic su **Start** > **Pannello di controllo** > **Programmi e funzionalità**
2. Verificare le versioni dei servizi dell'appliance nell'elenco.

    ![Verificare la versione nel Pannello di controllo](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Aggiornare manualmente una versione precedente

Se si esegue una versione precedente per uno dei servizi, è necessario disinstallare il servizio ed eseguire manualmente l'aggiornamento alla versione più recente.

1. Per verificare le versioni del servizio dell'appliance più recenti, [scaricare](https://aka.ms/latestapplianceservices) il file LatestComponents.json.
2. Dopo il download, aprire il file LatestComponents.json nel Blocco note.
3. Individuare la versione del servizio più recente nel file e il relativo collegamento di download. Ad esempio:

    "Nome": "ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Versione": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4. Scaricare la versione più recente di un servizio obsoleto usando il collegamento di download nel file.
5. Dopo il download, eseguire il comando seguente in una finestra di comando dell'amministratore per verificare l'integrità del file MSI.

    ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ``` Ad esempio:  C:\>CertUtil -HashFile C:\Utenti\public\download\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Verificare che l'output del comando corrisponda alla voce del valore hash per il servizio nel file, ad esempio, al valore hash MD5 precedente.
6. Eseguire ora il file MSI per installare il servizio. Si tratta di un'installazione invisibile; al termine la finestra di installazione si chiude.
7. Al termine dell'installazione, verificare la versione del servizio nel **Pannello di controllo** > **Programmi e funzionalità**. La versione del servizio dovrebbe ora essere aggiornata alla versione più recente visualizzata nel file JSON.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come](how-to-set-up-appliance-vmware.md) configurare l'appliance per VMware.
- [Informazioni su come](how-to-set-up-appliance-hyper-v.md) configurare l'appliance per Hyper-V.
- [Informazioni su come](how-to-set-up-appliance-physical.md) configurare l'appliance per i server fisici.
