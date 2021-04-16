---
title: Risolvere i problemi relativi al backup dello stato del sistema
description: Questo articolo illustra come risolvere i problemi in Backup dello stato del sistema per i server Windows locali.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 07e101fe87fb3f5db0bb716f0bc9ea6f8773aa3e
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518559"
---
# <a name="troubleshoot-system-state-backup"></a>Risolvere i problemi relativi al backup dello stato del sistema

Questo articolo descrive le soluzioni per i problemi che possono verificarsi durante l'uso del backup dello stato del sistema.

## <a name="basic-troubleshooting"></a>Risoluzione dei problemi di base

Prima di iniziare a risolvere i problemi relativi al backup dello stato del sistema, è consigliabile eseguire i passaggi di convalida seguenti:

- [Assicurarsi che l'agente di Servizi di ripristino di Microsoft Azure sia aggiornato](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Verificare la connettività di rete tra l'agente MARS e Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Verificare che i Servizi di ripristino di Microsoft Azure siano in esecuzione (nella console di Servizio). Se necessario, riavviare e ripetere l'operazione
- [Verificare che sia disponibile il 5-10% di volume libero nel percorso della cartella dei file temporanei](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Controllare se un altro processo o un software antivirus interferisce con Backup di Azure](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Il backup pianificato ha esito negativo, ma il backup manuale funziona](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- Verificare che nel sistema operativo siano installati gli aggiornamenti più recenti
- [Assicurarsi che le unità e i file non supportati con attributi non supportati siano esclusi dal backup](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Assicurarsi che l'**orologio** del sistema protetto sia configurato sul fuso orario corretto <br>
- [Assicurarsi che il server disponga almeno di .NET Framework versione 4.5.2 e versioni successive](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Se si sta tentando di registrare **nuovamente il server** in un insieme di credenziali, eseguire le seguenti operazione: <br>
  - Assicurarsi che l'agente sia disinstallato nel server e che sia stato eliminato dal portale <br>
  - Usare la stessa passphrase che è stata usata inizialmente per la registrazione del server <br>
- Se si tratta di un backup offline, assicurarsi che Azure PowerShell versione 3.7.0 sia installata nel computer di origine e di copia prima di iniziare l'operazione di backup offline
- [Considerazione quando l'agente di backup è in esecuzione in una macchina virtuale di Azure](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Limitazione

- Il ripristino in hardware diverso con il ripristino dello stato del sistema non è consigliato da Microsoft
- Il backup dello stato del sistema supporta attualmente server Windows "locali". Questa funzionalità non è disponibile per le macchine virtuali di Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di risolvere i problemi relativi al backup dello stato del Backup di Azure, eseguire il controllo dei prerequisiti seguente.  

### <a name="verify-windows-server-backup-is-installed"></a>Verificare Windows Server Backup installato

Assicurarsi Windows Server Backup installato e abilitato nel server. Per controllare lo stato di installazione, eseguire questo comando di PowerShell:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Se l'output visualizza Lo stato **di** installazione come **disponibile,** significa che la funzionalità di backup di Windows Server è disponibile per l'installazione ma non è installata nel server. Tuttavia, se Windows Server Backup non è installato, usare uno dei metodi seguenti per installarlo.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Metodo 1: Installare Windows Server Backup usando PowerShell

Per installare Windows Server Backup usando PowerShell, eseguire il comando seguente:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Metodo 2: Installare Windows Server Backup usando Server Manager

Per installare Windows Server Backup usando Server Manager, seguire questa procedura:

1. In **Server Manager selezionare** Aggiungi ruoli e **funzionalità.** Verrà **visualizzata la procedura guidata Aggiungi ruoli e** funzionalità.

    ![Dashboard](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Selezionare **Tipo di installazione** e quindi **Avanti.**

    ![Tipo di installazione](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Selezionare un server dal pool di server e selezionare **Avanti.** In Ruolo server lasciare la selezione predefinita e selezionare **Avanti.**
4. Selezionare **Windows Server Backup** nella **scheda Funzionalità** e selezionare **Avanti.**

    ![Finestra Seleziona funzionalità](./media/backup-azure-system-state-troubleshoot/features.png)

5. Nella scheda **Conferma** selezionare Installa **per avviare** il processo di installazione.
6. Nella scheda **Risultati** verrà visualizzata la Windows Server Backup funzionalità è stata installata correttamente in Windows Server.

    ![Risultati dell'installazione](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Autorizzazione per le informazioni sui volumi di sistema

Verificare che il sistema locale abbia il controllo completo sulla cartella **System Volume Information** che si trova nel volume in cui è installato Windows. In genere si tratta **di C:\System Volume Information**. Il backup di Windows Server può non riuscire se le autorizzazioni precedenti non sono impostate correttamente.

### <a name="dependent-services"></a>Servizi dipendenti

Verificare che i servizi seguenti siano in esecuzione:

**Nome servizio** | **Tipo di avvio**
--- | ---
Chiamata di procedura remota (RPC) | Automatico
Sistema di eventi COM+ (EventSystem) | Automatico
Servizio di notifica eventi di sistema (SENS) | Automatico
Copia shadow del volume (VSS) | Manuale
Provider di copia shadow del software Microsoft (SWPRV) | Manuale

### <a name="validate-windows-server-backup-status"></a>Convalidare Windows Server Backup stato

Per convalidare Windows Server Backup stato, seguire questa procedura:

- Assicurarsi che PowerShell WSB sia in esecuzione

  - Eseguire da un PowerShell con privilegi elevati e `Get-WBJob` assicurarsi che non restituirà l'errore seguente:

    > [!WARNING]
    > Get-WBJob: il termine 'Get-WBJob' non è riconosciuto come nome di un cmdlet, una funzione, un file script o un programma operabile. Verificare l'ortografia del nome, che il percorso sia incluso e corretto, quindi riprovare.

    - Se si verifica un errore con questo errore, reinstallare la Windows Server Backup nel computer server come indicato nel passaggio 1 dei prerequisiti.

  - Assicurarsi che il backup WSB funzioni correttamente eseguendo il comando seguente da un prompt dei comandi con privilegi elevati:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Sostituire X con la lettera di unità del volume in cui si vuole archiviare l'immagine di backup dello stato del sistema.

    - Controllare periodicamente lo stato del processo eseguendo `Get-WBJob` il comando da PowerShell con privilegi elevati
    - Al termine del processo di backup, controllare lo stato finale del processo eseguendo il `Get-WBJob -Previous 1` comando

Se il processo ha esito negativo, indica un problema WSB che comporterebbe un errore di backup dello stato del sistema dell'agente MARS.

## <a name="common-errors"></a>Errori comuni

### <a name="vss-writer-timeout-error"></a>Errore di timeout di VSS Writer

| Sintomo | Causa | Soluzione
| -- | -- | --
| - L'agente MARS ha esito negativo con il messaggio di errore:"Processo WSB non riuscito con errori vss. Controllare i log eventi di VSS per risolvere l'errore"<br/><br/> - Nei log eventi dell'applicazione VSS è presente il log degli errori seguente: "Un VSS writer ha rifiutato un evento con errore 0x800423f2, il timeout del writer è scaduto tra gli eventi Freeze e Thaw".| VSS writer non è possibile completare in tempo a causa della mancanza di risorse di CPU e memoria nel computer <br/><br/> Un altro software di backup sta già usando il VSS writer, di conseguenza non è stato possibile completare l'operazione di snapshot per questo backup | Attendere che cpu/memoria siano liberate nel sistema o interrompere i processi che estendono una quantità di memoria/CPU troppo grande e ripetere l'operazione. <br/><br/>  Attendere il completamento del backup in corso e provare l'operazione in un secondo momento quando non sono in esecuzione backup nel computer.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Spazio su disco insufficiente per aumentare le copie shadow

| Sintomo | Soluzione
| -- | --
| - L'agente MARS non riesce e viene visualizzato il messaggio di errore: Backup non riuscito perché non è stato possibile aumentare il volume della copia shadow a causa di spazio su disco insufficiente nei volumi contenenti file di sistema <br/><br/> - Il log degli errori o degli avvisi seguente è presente nei registri eventi di sistema volsnap: "Spazio su disco insufficiente nel volume C: per aumentare l'archiviazione delle copie shadow per le copie shadow di C: a causa di questo errore, tutte le copie shadow del volume C: sono a rischio di essere eliminate" | - Liberare spazio nel volume evidenziato nel registro eventi in modo che vi sia spazio sufficiente per aumentare le copie shadow mentre è in corso il backup <br/><br/> - Durante la configurazione dello spazio della copia shadow è possibile limitare la quantità di spazio usata per la copia shadow. Per altre informazioni, vedere questo [articolo](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>Partizione EFI bloccata

| Sintomo | Soluzione
| -- | --
| L'agente MARS ha esito negativo e viene visualizzato il messaggio di errore: "System state backup failed as the EFI system partition is locked. (Il backup dello stato del sistema non è riuscito perché la partizione di sistema EFI è bloccata. Ciò può essere dovuto all'accesso alle partizioni di sistema da parte di un software di sicurezza di terze parti o di backup". | - Se il problema è dovuto a un software di sicurezza di terze parti, è necessario contattare il fornitore dell'antivirus in modo che possa consentire l'agente MARS <br/><br/> - Se è in esecuzione un software di backup di terze parti, attendere il completamento e quindi riprovare a eseguire il backup

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sullo stato del sistema Windows Resource Manager distribuzione, vedere [Eseguire il backup dello stato del sistema Windows Server](backup-azure-system-state.md)
