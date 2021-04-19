---
title: Supporto per la migrazione di Hyper-V in Azure Migrate
description: Informazioni sul supporto per la migrazione di Hyper-V con Azure Migrate.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 33e34e777a78e1c609d2eacdcb501c0bce1f5c9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714921"
---
# <a name="support-matrix-for-hyper-v-migration"></a>Matrice di supporto per la migrazione di Hyper-V

Questo articolo riepiloga le impostazioni di supporto e le limitazioni per la migrazione di macchine virtuali Hyper-V [con Azure Migrate: Migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) . Per informazioni sulla valutazione delle macchine virtuali Hyper-V per la migrazione ad Azure, esaminare la matrice [di supporto della valutazione](migrate-support-matrix-hyper-v.md).

## <a name="migration-limitations"></a>Limitazioni della migrazione

È possibile selezionare fino a 10 macchine virtuali contemporaneamente per la replica. Se si vuole eseguire la migrazione di più computer, eseguire la replica in gruppi di 10.


## <a name="hyper-v-host-requirements"></a>Requisiti dell'host Hyper-V

| **Supporto**                | **Dettagli**               
| :-------------------       | :------------------- |
| **Distribuzione**       | L'host Hyper-V può essere autonomo o distribuito in un cluster. <br/>Azure Migrate software di replica (provider di replica Hyper-V) viene installato sugli host Hyper-V.|
| **Autorizzazioni**           | Sono necessarie autorizzazioni di amministratore per l'host Hyper-V. |
| **Sistema operativo host** | Windows Server 2019, Windows Server 2016 o Windows Server 2012 R2 con gli aggiornamenti più recenti. Si noti che è supportata anche l'installazione server core di questi sistemi operativi. |
| **Altri requisiti software** | .NET Framework 4.7 o versione successiva |
| **Accesso alla porta** |  Connessioni in uscita sulla porta HTTPS 443 per inviare i dati di replica della macchina virtuale.
| **Spazio libero su disco (cache)** |  600 GB |
| **Spazio libero su disco (disco di conservazione)** |  600 GB |


## <a name="hyper-v-vms"></a>VM Hyper-V

| **Supporto**                  | **Dettagli**               
| :----------------------------- | :------------------- |
| **Sistema operativo** | Tutti i sistemi operativi [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](../virtual-machines/linux/endorsed-distros.md) supportati da Azure. |
**Windows Server 2003** | Per le macchine virtuali che eseguono Windows Server 2003, è necessario installare [Hyper-V](prepare-windows-server-2003-migration.md) Integration Services prima della migrazione. | 
**Macchine virtuali Linux in Azure** | Alcune macchine virtuali potrebbero richiedere alcune modifiche per la corretta esecuzione in Azure.<br/><br/> Per Linux, Azure Migrate automaticamente le modifiche per questi sistemi operativi:<br/> - Red Hat Enterprise Linux 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x<br/> - Cent OS 7.7, 7.6, 7.5, 7.4, 6.x</br> - SUSE Linux Enterprise Server 12 SP1+<br/> - SUSE Linux Enterprise Server 15 SP1 <br/>- Ubuntu 19.04, 19.10, 14.04LTS, 16.04LTS, 18.04LTS<br/> - Debian 7, 8 <br/> Oracle Linux 7.7, 7.7-CI<br/> Per altri sistemi operativi è necessario apportare [manualmente le modifiche](prepare-for-migration.md#verify-required-changes-before-migrating) necessarie.
| **Modifiche necessarie per Azure** | Alcune macchine virtuali potrebbero richiedere alcune modifiche per la corretta esecuzione in Azure. Apportare modifiche manualmente prima della migrazione. Gli articoli pertinenti contengono istruzioni su come eseguire questa operazione. |
| **Avvio di Linux**                 | Se /boot si trova in una partizione dedicata, deve risiedere nel disco del sistema operativo e non essere distribuito tra più dischi.<br/> Se /boot fa parte della partizione radice (/), la partizione '/' deve essere nel disco del sistema operativo e non estendersi su altri dischi. |
| **Avvio UEFI**                  | Supportata. Verrà eseguita la migrazione delle macchine virtuali basate su UEFI alle macchine virtuali di seconda generazione di Azure.  |
| **UEFI - Avvio protetto**         | Non supportato per la migrazione.|
| **Dimensioni disco**                  | 2 TB per il disco del sistema operativo (avvio del BIOS), 4 TB per il disco del sistema operativo (avvio UEFI), 4 TB per i dischi dati.|
| **Numero disco** | Un massimo di 16 dischi per macchina virtuale.|
| **Dischi/volumi crittografati**    | Non supportato per la migrazione.|
| **Dischi RDM/pass-through**      | Non supportato per la migrazione.|
| **Disco condiviso** | Le macchine virtuali che usano dischi condivisi non sono supportate per la migrazione.|
| **NFS**                        | I volumi NFS montati come volumi nelle macchine virtuali non verranno replicati.|
| **Iscsi**                      | Le macchine virtuali con destinazioni iSCSI non sono supportate per la migrazione.
| **Disco di destinazione**                | È possibile eseguire la migrazione alle macchine virtuali di Azure solo con dischi gestiti. |
| **IPv6** | Non supportata.|
| **Gruppo NIC** | Non supportata.|
| **Azure Site Recovery** | Non è possibile eseguire la replica usando Azure Migrate Server Migration se la macchina virtuale è abilitata per la replica con Azure Site Recovery.|
| **Ports** | Connessioni in uscita sulla porta HTTPS 443 per inviare i dati di replica della macchina virtuale.|

### <a name="url-access-public-cloud"></a>Accesso con URL (cloud pubblico)

Il software del provider di replica negli host Hyper-V dovrà accedere a questi URL.

**URL** | **Dettagli**
--- | ---
login.microsoftonline.com | Controllo di accesso e gestione delle identità tramite Active Directory.
backup.windowsazure.com | Trasferimento e coordinamento dei dati di replica.
*.hypervrecoverymanager.windowsazure.com | Utilizzato per la gestione della replica.
*.blob.core.windows.net | Caricare i dati in account di archiviazione. 
dc.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
time.windows.com | Verifica la sincronizzazione dell'ora tra l'ora di sistema e l'ora globale.

### <a name="url-access-azure-government"></a>Accesso con URL (Azure per enti pubblici)

Il software del provider di replica negli host Hyper-V dovrà accedere a questi URL.

**URL** | **Dettagli**
--- | ---
login.microsoftonline.us | Controllo di accesso e gestione delle identità tramite Active Directory.
backup.windowsazure.us | Trasferimento e coordinamento dei dati di replica.
*.hypervrecoverymanager.windowsazure.us | Utilizzato per la gestione della replica.
*.blob.core.usgovcloudapi.net | Caricare i dati in account di archiviazione.
dc.services.visualstudio.com | Caricare i log delle app usati per il monitoraggio interno.
time.nist.gov | Verifica la sincronizzazione dell'ora tra l'ora di sistema e l'ora globale.   

>[!Note]
>
> Se si esegue la migrazione del progetto con connettività **endpoint** privato, il software del provider di replica negli host Hyper-V dovrà accedere a questi URL per il supporto del collegamento privato. 
> - *.blob.core.windows.com: per accedere all'account di archiviazione che archivia i dati replicati. Questa operazione è facoltativa e non è necessaria se all'account di archiviazione è associato un endpoint privato. 
> - login.windows.net per il controllo di accesso e la gestione delle identità tramite Active Directory.

## <a name="azure-vm-requirements"></a>Requisiti per le VM di Azure

Tutte le macchine virtuali locali replicate in Azure devono soddisfare i requisiti delle macchine virtuali di Azure riepilogati in questa tabella.

**Componente** | **Requisiti** | **Dettagli**
--- | --- | ---
Dimensioni disco del sistema operativo | Fino a 2.048 GB. | Il controllo ha esito negativo se non supportato.
Conteggio dischi del sistema operativo | 1 | Il controllo ha esito negativo se non supportato.
Conteggio dischi dati | 16 o meno. | Il controllo ha esito negativo se non supportato.
Dimensioni del disco dati | Fino a 4.095 GB | Il controllo ha esito negativo se non supportato.
Schede di rete | Sono supportate più schede. |
VHD condiviso | Non supportata. | Il controllo ha esito negativo se non supportato.
Disco FC | Non supportata. | Il controllo ha esito negativo se non supportato.
BitLocker | Non supportata. | Prima di abilitare la replica per un computer, occorre disabilitare BitLocker.
Nome della VM. | Da 1 a 63 caratteri.<br/> Limitato a lettere, numeri e trattini.<br/><br/> Il nome del computer deve iniziare e terminare con una lettera o un numero. |  Aggiornare il valore nelle proprietà del computer in Site Recovery.
Connettersi dopo la migrazione- Windows | Per connettersi alle macchine virtuali di Azure che eseguono Windows dopo la migrazione:<br/><br/> - Prima della migrazione, abilitare RDP nella macchina virtuale locale. Assicurarsi che siano aggiunte regole TCP e UDP per il profilo **Pubblico** e che RDP sia consentito in **Windows Firewall** > **App consentite** per tutti i profili.<br/><br/> - Per l'accesso VPN da sito a sito, abilitare RDP e consentire RDP in **Windows Firewall** App e funzionalità consentite per le reti  ->   di dominio **e** private. Verificare inoltre che i criteri SAN del sistema operativo sia impostato su **OnlineAll**. [Altre informazioni](prepare-for-migration.md) |
Connettersi dopo la migrazione-Linux | Per connettersi alle macchine virtuali di Azure dopo la migrazione tramite SSH:<br/><br/> - Prima della migrazione, nel computer locale verificare che il servizio Secure Shell sia impostato su Start e che le regole del firewall consentano una connessione SSH.<br/><br/> - Dopo la migrazione, nella macchina virtuale di Azure consentire le connessioni in ingresso alla porta SSH per le regole del gruppo di sicurezza di rete nella macchina virtuale di cui è stato fatto il failed over e per la subnet di Azure a cui è connessa. Aggiungere anche un indirizzo IP pubblico per la macchina virtuale. |  

## <a name="next-steps"></a>Passaggi successivi

[Eseguire la migrazione di macchine virtuali Hyper-V](tutorial-migrate-hyper-v.md) per la migrazione.
