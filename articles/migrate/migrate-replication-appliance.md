---
title: Appliance di replica Azure Migrate
description: Informazioni sull'appliance Azure Migrate di replica per la migrazione VMware basata su agente.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 5f63b033c3995932662fc9b68c1397bf57b0326e
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714975"
---
# <a name="replication-appliance"></a>Appliance di replica

Questo articolo descrive l'appliance di replica usata da [Azure Migrate: Strumento](migrate-services-overview.md#azure-migrate-server-migration-tool) di migrazione server durante la migrazione di macchine virtuali VMware, computer fisici e macchine virtuali cloud private/pubbliche in Azure, usando la migrazione basata su agente. 


## <a name="overview"></a>Panoramica

L'appliance di replica viene distribuita quando si configura la migrazione basata su agente di macchine virtuali VMware o server fisici. Viene distribuito come singolo computer locale, come macchina virtuale VMware o come server fisico. Viene eseguito:

- **Appliance di replica:** l'appliance di replica coordina le comunicazioni e gestisce la replica dei dati per le macchine virtuali VMware locali e i server fisici che esere replicano in Azure.
- **Server di elaborazione:** il server di elaborazione, installato per impostazione predefinita nell'appliance di replica, esegue le operazioni seguenti:
    - **Gateway di replica:** funge da gateway di replica. Riceve i dati di replica dai computer abilitati per la replica. Ottimizza i dati di replica con memorizzazione nella cache, compressione e crittografia e li invia ad Azure.
    - **Programma di installazione** dell'agente: esegue un'installazione push del servizio Mobility. Questo servizio deve essere installato e in esecuzione in ogni computer locale che si vuole replicare per la migrazione.

## <a name="appliance-deployment"></a>Distribuzione dell'appliance

**Usata per** | **Dettagli**
--- |  ---
**Migrazione basata su agente di macchine virtuali VMware** | Scaricare il modello OVA dall'hub Azure Migrate e importare in server vCenter per creare la macchina virtuale dell'appliance.
**Migrazione basata su agente del computer fisico** | Se non si ha un'infrastruttura VMware o non è possibile creare una macchina virtuale VMware usando un modello OVA, scaricare un programma di installazione software dall'hub Azure Migrate ed eseguirlo per configurare il computer dell'appliance.

> [!NOTE]
> Se si esegue la distribuzione in Azure per enti pubblici, usare il file di installazione per distribuire l'appliance di replica.

## <a name="appliance-requirements"></a>Requisiti dell'appliance

Quando si configura l'appliance di replica usando il modello OVA fornito nell'hub Azure Migrate, l'appliance esegue Windows Server 2016 e rispetta i requisiti di supporto. Se l'appliance di replica è stata impostata manualmente in un server fisico, assicurarsi che sia conforme ai requisiti.

**Componente** | **Requisito**
--- | ---
 | **Appliance vm VMware**
PowerCLI | [PowerCLI versione 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve essere installato se l'appliance di replica è in esecuzione in una macchina virtuale VMware.
Tipo di scheda di interfaccia di rete | VMXNET3 (se l'appliance è una macchina virtuale VMware)
 | **Impostazioni hardware**
Core CPU | 8
RAM | 16 GB
Numero di dischi | Tre: il disco del sistema operativo, il disco della cache del server di elaborazione e l'unità di conservazione.
Spazio libero su disco (cache) | 600 GB
Spazio libero su disco (disco di conservazione) | 600 GB
**Impostazioni software** |
Sistema operativo | Windows Server 2016 o Windows Server 2012 R2
Licenza | L'appliance viene fornita con una licenza di valutazione di Windows Server 2016, valida per 180 giorni.<br/><br/> Se il periodo di valutazione sta per terminare, è consigliabile scaricare e distribuire una nuova appliance oppure attivare la licenza del sistema operativo della macchina virtuale dell'appliance.
Impostazioni locali del sistema operativo | Inglese (en-us)
TLS | TLS 1.2 deve essere abilitato.
.NET Framework | .NET Framework 4.6 o versione successiva deve essere installato nel computer (con la crittografia forte abilitata.
MySQL | MySQL deve essere installato nell'appliance.<br/> MySQL deve essere installato. È possibile eseguire l'installazione manualmente o Site Recovery durante la distribuzione dell'appliance.
Altre app | Non eseguire altre app nell'appliance di replica.
Ruoli di Windows Server | Non abilitare questi ruoli: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V
Criteri di gruppo | Non abilitare questi criteri di gruppo: <br> - Impedisci accesso al prompt dei comandi <br> - Impedisci accesso agli strumenti di modifica del Registro di sistema <br> - Logica di attendibilità per file allegati <br> - Attiva l'esecuzione di script <br> [Scopri di più](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Nessun sito Web predefinito preesistente <br> - Nessun sito Web o applicazione preesistente in ascolto sulla porta 443 <br>- Abilitare l'[autenticazione anonima](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Abilitare l'impostazione di [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))
**Impostazioni di rete** |
Tipo di indirizzo IP | Statico
Porte | 443 (orchestrazione del canale di controllo)<br>9443 (trasporto dei dati)
Tipo di scheda di interfaccia di rete | VMXNET3

## <a name="mysql-installation"></a>Installazione di MySQL 

MySQL deve essere installato nel computer dell'appliance di replica. Può essere installato usando uno di questi metodi.

**Metodo** | **Dettagli**
--- | ---
Scaricare e installare manualmente | Scaricare l'applicazione MySQL & posizionarla nella cartella C:\Temp\ASRSetup, quindi eseguire l'installazione manualmente.<br/> Quando si configura l'appliance, MySQL viene visualizzato come già installato.
Senza download online | Inserire l'applicazione di installazione MySQL nella cartella C:\Temp\ASRSetup. Quando si installa l'appliance e si seleziona scarica e si installa MySQL, il programma di installazione verrà utilizzato dal programma di installazione aggiunto.
Scaricare e installare in Azure Migrate | Quando si installa l'appliance e viene richiesto di immettere MySQL, selezionare **Scarica e installa**.

## <a name="url-access"></a>accesso con URL

L'appliance di replica deve accedere a questi URL nel cloud pubblico di Azure.

**URL** | **Dettagli**
--- | ---
\*.backup.windowsazure.com | Usato per il coordinamento e il trasferimento dei dati replicati
\*.store.core.windows.net | Usato per il coordinamento e il trasferimento dei dati replicati
\*.blob.core.windows.net | Usato per accedere all'account di archiviazione in cui sono archiviati i dati replicati
\*.hypervrecoverymanager.windowsazure.com | Usato per il coordinamento e le operazioni di gestione della replica
https:\//management.azure.com | Usato per il coordinamento e le operazioni di gestione della replica
*.services.visualstudio.com | Usato a scopo di registrazione (facoltativo)
time.windows.com | Usati per controllare la sincronizzazione tra ora di sistema e ora globale.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | La configurazione dell'appliance deve accedere a questi URL. Vengono usati per la gestione di identità e controllo di accesso da Azure Active Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Per completare il download di MySQL. In alcune aree il download potrebbe essere reindirizzato all'URL della rete CDN. Assicurarsi che anche l'URL della rete CDN sia consentito, se necessario.


## <a name="azure-government-url-access"></a>Azure per enti pubblici'accesso all'URL

L'appliance di replica deve accedere a questi URL in Azure per enti pubblici.

**URL** | **Dettagli**
--- | ---
\*.backup.windowsazure.us | Usato per il coordinamento e il trasferimento dei dati replicati
\*.store.core.windows.net | Usato per il coordinamento e il trasferimento dei dati replicati
\*.blob.core.windows.net | Usato per accedere all'account di archiviazione in cui sono archiviati i dati replicati
\*.hypervrecoverymanager.windowsazure.us | Usato per il coordinamento e le operazioni di gestione della replica
https:\//management.usgovcloudapi.net | Usato per il coordinamento e le operazioni di gestione della replica
*.services.visualstudio.com | Usato a scopo di registrazione (facoltativo)
time.nist.gov | Usati per controllare la sincronizzazione tra ora di sistema e ora globale.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | La configurazione dell'appliance con OVA deve accedere a questi URL. Vengono usati per il controllo di accesso e la gestione delle identità Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Per completare il download di MySQL. In alcune aree il download potrebbe essere reindirizzato all'URL della rete CDN. Assicurarsi che anche l'URL della rete CDN sia consentito, se necessario.  

>[!Note]
>
> Se si esegue la migrazione del progetto con connettività dell'endpoint privato, sarà necessario accedere agli URL seguenti oltre all'accesso al collegamento privato:   
> - *.blob.core.windows.com: per accedere all'account di archiviazione che archivia i dati replicati. Questa opzione è facoltativa e non è necessaria se l'account di archiviazione ha un endpoint privato collegato. 
> - https: \/ /management.azure.com per le operazioni di gestione della replica e il coordinamento. 
>- https:\//login.microsoftonline.com <br/>https:\//login.windows.net <br/> https: \/ /www.live.com _e_ <br/> https: \/ /www.microsoft.com per il controllo di accesso e la gestione delle identità Azure Active Directory

## <a name="port-access"></a>Accesso alla porta

**Dispositivo** | **Connection**
--- | ---
VM | Il servizio di mobilità in esecuzione nelle macchine virtuali comunica con l'appliance di replica locale (server di configurazione) sulla porta HTTPS 443 in ingresso, per la gestione della replica.<br/><br/> Le macchine virtuali inviano i dati della replica al server di elaborazione (in esecuzione sul computer del server di configurazione) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
Appliance di replica | L'appliance di replica orchestra la replica con Azure sulla porta HTTPS 443 in uscita.
Server di elaborazione | Il server di elaborazione riceve i dati di replica, li ottimizza e li crittografa e li invia all'archiviazione di Azure sulla porta 443 in uscita.<br/> Per impostazione predefinita, il server di elaborazione viene eseguito nell'appliance di replica.


## <a name="replication-process"></a>Processo di replica

1. Quando si abilita la replica per una macchina virtuale, viene avviata la replica iniziale nell'archiviazione di Azure con i criteri di replica specificati. 
2. Il traffico viene replicato negli endpoint pubblici di archiviazione di Azure, tramite Internet. La replica del traffico tramite una VPN da sito a sito da un sito locale ad Azure non è supportata.
3. Al termine della replica iniziale, viene avviata la replica differenziale. Le modifiche rilevate per un computer vengono registrate.
4. Le comunicazioni avvengono nel modo seguente:
    - Le macchine virtuali comunicano con l'appliance di replica sulla porta HTTPS 443 in ingresso, per la gestione della replica.
    - L'appliance di replica orchestra la replica con Azure sulla porta HTTPS 443 in uscita.
    - Le macchine virtuali inviano i dati di replica al server di elaborazione (in esecuzione nell'appliance di replica) sulla porta HTTPS 9443 in ingresso. La porta può essere modificata.
    - Il server di elaborazione riceve i dati di replica, li ottimizza e li crittografa e li invia all'archiviazione di Azure sulla porta 443 in uscita.
5. I log dei dati di replica vengono prima di tutto memorizzati in un account di archiviazione della cache in Azure. Questi log vengono elaborati e i dati vengono archiviati in un disco gestito di Azure.

![Il diagramma mostra l'architettura del processo di replica.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Aggiornamenti dell'appliance

L'appliance viene aggiornata manualmente dall'hub Azure Migrate dispositivo. È consigliabile eseguire sempre la versione più recente.

1. In Azure Migrate > Server > Azure Migrate: Valutazione server, Server infrastruttura selezionare **Server di configurazione.**
2. In **Server di configurazione** viene visualizzato un collegamento in Versione **agente** quando è disponibile una nuova versione dell'appliance di replica. 
3. Scaricare il programma di installazione nel computer dell'appliance di replica e installare l'aggiornamento. Il programma di installazione rileva la versione corrente in esecuzione nell'appliance.
 
## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) configurare l'appliance di replica per la migrazione di macchine virtuali VMware basate su agente.
- [Informazioni su come](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) configurare l'appliance di replica per i server fisici.
