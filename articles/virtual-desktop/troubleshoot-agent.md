---
title: Risolvere i problemi dell'agente desktop virtuale di Windows-Azure
description: Come risolvere i problemi comuni relativi all'agente e alla connettività.
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 1500a635d5177ed8899cdc3f1364e57a8525892c
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099949"
---
# <a name="troubleshoot-common-windows-virtual-desktop-agent-issues"></a>Risolvere i problemi comuni relativi all'agente desktop virtuale di Windows

L'agente desktop virtuale di Windows può causare problemi di connessione a causa di più fattori:
   - Errore nel broker che impedisce all'agente di arrestare il servizio.
   - Problemi con gli aggiornamenti.
   - Problemi di installazione durante l'installazione dell'agente, che interrompe la connessione all'host sessione.

In questo articolo vengono illustrate le soluzioni a questi scenari comuni e viene illustrato come risolvere i problemi di connessione.

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>Errore: l'esecuzione di RDAgentBootLoader e/o Desktop remoto Loader Agent è stata arrestata

Se si verifica uno dei problemi seguenti, significa che il caricatore di avvio, che carica l'agente, non è stato in grado di installare correttamente l'agente e il servizio Agent non è in esecuzione:
- **RDAgentBootLoader** è stato interrotto o non è in esecuzione.
- Non esiste alcuno stato per il **caricatore di desktop remoto Agent**.

Per risolvere questo problema, avviare il caricatore di avvio di RDAgent:

1. Nella finestra Servizi fare clic con il pulsante destro del mouse su **Desktop remoto caricatore agenti**.
2. Selezionare **Inizio**. Se questa opzione è disattivata, non si dispone delle autorizzazioni di amministratore e sarà necessario avviarlo per avviare il servizio.
3. Attendere 10 secondi, quindi fare clic con il pulsante destro del mouse su **Desktop remoto caricatore agenti**.
4. Selezionare **Aggiorna**.
5. Se il servizio si arresta dopo l'avvio e l'aggiornamento, è possibile che si verifichi un errore di registrazione. Per ulteriori informazioni, vedere [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token).

## <a name="error-invalid_registration_token"></a>Errore: INVALID_REGISTRATION_TOKEN

Passare a **Visualizzatore eventi**  >  **applicazione registri di Windows**  >  . Se viene visualizzato un evento con ID 3277, che indica **INVALID_REGISTRATION_TOKEN** nella descrizione, il token di registrazione non è riconosciuto come valido.

Per risolvere questo problema, creare un token di registrazione valido:

1. Per creare un nuovo token di registrazione, seguire la procedura descritta nella sezione [generare una nuova chiave di registrazione per la macchina virtuale](#step-3-generate-a-new-registration-key-for-the-vm) .
2. Aprire l'editor del Registro di sistema. 
3. Passare a **HKEY_LOCAL_MACHINE**  >  **software**  >  **Microsoft**  >  **RDInfraAgent**.
4. Selezionare la **registrazione**. 
5. Nella casella di immissione **valore dati:** Digitare **0** e selezionare **OK**. 
6. Selezionare **RegistrationToken**. 
7. Nella casella di immissione **valore dati:** incollare il token di registrazione del passaggio 1. 

   > [!div class="mx-imgBorder"]
   > ![Schermata di registrazione 0](media/isregistered-token.png)

8. Aprire un prompt dei comandi come amministratore.
9. Immettere **net stop RDAgentBootLoader**. 
10. Immettere **net start RDAgentBootLoader**. 
11. Aprire l'editor del Registro di sistema.
12. Passare a **HKEY_LOCAL_MACHINE**  >  **software**  >  **Microsoft**  >  **RDInfraAgent**.
13. Verificare che la **registrazione** sia stata impostata su 1 e che nella colonna di dati per **RegistrationToken** non sia presente alcun elemento. 

   > [!div class="mx-imgBorder"]
   > ![Schermata di registrazione 1](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form-or-not_found-url"></a>Errore: l'agente non è in grado di connettersi al broker con INVALID_FORM o NOT_FOUND. URL

Passare a **Visualizzatore eventi**  >  **applicazione registri di Windows**  >  . Se viene visualizzato un evento con ID 3277, **INVALID_FORM** o **NOT_FOUND. URL** nella descrizione. si è verificato un errore durante la comunicazione tra l'agente e il broker. L'agente non è in grado di connettersi al broker e non è in grado di raggiungere un particolare URL. Questo problema può essere dovuto alle impostazioni del firewall o DNS.

Per risolvere questo problema, verificare che sia possibile raggiungere BrokerURI e BrokerURIGlobal:
1. Aprire l'editor del registro di sistema. 
2. Passare a **HKEY_LOCAL_MACHINE**  >  **software**  >  **Microsoft**  >  **RDInfraAgent**. 
3. Prendere nota dei valori di **BrokerURI** e **BrokerURIGlobal**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot di URI broker e URI broker globali](media/broker-uri.png)

 
4. Aprire un browser e passare a *\<BrokerURI\> API/integrità*. 
   - Assicurarsi di usare il valore del passaggio 3 in **BrokerURI**. In questa sezione, ad esempio, sarebbe <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health> .
5. Aprire un'altra scheda nel browser e passare a *\<BrokerURIGlobal\> API/integrità*. 
   - Assicurarsi di usare il valore del passaggio 3 nel collegamento **BrokerURIGlobal** . In questa sezione, ad esempio, sarebbe <https://rdbroker.wvd.microsoft.com/api/health> .
6. Se la rete non blocca la connessione del broker, entrambe le pagine vengono caricate correttamente e viene visualizzato un messaggio che indica che **"gestore Rd è integro"** , come illustrato nelle schermate seguenti. 

   > [!div class="mx-imgBorder"]
   > ![Screenshot dell'accesso dell'URI broker caricato correttamente](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![Screenshot dell'accesso URI globale di Service Broker completato](media/broker-global.png)
 

7. Se la rete blocca la connessione del broker, le pagine non vengono caricate, come illustrato nello screenshot seguente. 

   > [!div class="mx-imgBorder"]
   > ![Screenshot dell'accesso al broker caricato non riuscito](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![Screenshot dell'accesso globale broker caricato non riuscito](media/unsuccessful-broker-global.png)

8. Se la rete sta bloccando questi URL, sarà necessario sbloccare gli URL richiesti. Per ulteriori informazioni, vedere [elenco degli URL richiesti](safe-url-list.md).
9. Se il problema persiste, assicurarsi che non siano presenti criteri di gruppo con crittografie che impediscano la connessione dell'agente alla connessione broker. Desktop virtuale Windows usa le stesse crittografie TLS 1,2 come [sportello anteriore di Azure](../frontdoor/front-door-faq.MD#what-are-the-current-cipher-suites-supported-by-azure-front-door). Per ulteriori informazioni, vedere [sicurezza della connessione](network-connectivity.md#connection-security).

## <a name="error-3703-or-3019"></a>Errore: 3703 o 3019

Passare a **Visualizzatore eventi**  >  **applicazione registri di Windows**  >  . Se viene visualizzato un evento con ID 3703, che indica l' **URL del Gateway Desktop remoto: non è accessibile** o qualsiasi evento con ID 3019 nella descrizione, l'agente non è in grado di raggiungere gli URL del gateway o gli URL di trasporto del socket Web. Per connettersi all'host sessione e consentire al traffico di rete a questi endpoint di ignorare le restrizioni, è necessario sbloccare gli URL dall'elenco di [URL richiesto](safe-url-list.md). Assicurarsi inoltre che le impostazioni del firewall o del proxy non blocchino questi URL. Lo sblocco di questi URL è necessario per usare desktop virtuale di Windows.

Per risolvere questo problema, verificare che le impostazioni del firewall e/o DNS non blocchino questi URL:
1. [Usare il firewall di Azure per proteggere le distribuzioni di desktop virtuali Windows.](../firewall/protect-windows-virtual-desktop.md)
2. Configurare le [impostazioni DNS del firewall di Azure](../firewall/dns-settings.md).

## <a name="error-installmsiexception"></a>Errore: InstallMsiException

Passare a **Visualizzatore eventi**  >  **applicazione registri di Windows**  >  . Se viene visualizzato un evento con ID 3277, ovvero **InstallMsiException** nella descrizione, il programma di installazione è già in esecuzione per un'altra applicazione mentre si sta tentando di installare l'agente o un criterio impedisce l'esecuzione del programma msiexec.exe.

Per risolvere questo problema, disabilitare i criteri seguenti:
   - Disattiva Windows Installer  
      - Percorso Categoria: Configurazione computer\Modelli Amministrativi\componenti di Windows\windows programma di installazione
   
>[!NOTE]
>Questo non è un elenco completo dei criteri, solo quelli attualmente a conoscenza.

Per disabilitare un criterio:
1. Aprire un prompt dei comandi come amministratore.
2. Immettere ed eseguire **RSoP. msc**.
3. Nella finestra del **gruppo di criteri risultante** visualizzata passare al percorso della categoria.
4. Selezionare il criterio.
5. Selezionare **Disabilitato**.
6. Selezionare **Applica**.   

   > [!div class="mx-imgBorder"]
   > ![Screenshot dei criteri di Windows Installer nel set di criteri risultante](media/gpo-policy.png)

## <a name="error-win32exception"></a>Errore: Win32exception

Passare a **Visualizzatore eventi**  >  **applicazione registri di Windows**  >  . Se viene visualizzato un evento con ID 3277, che indica **InstallMsiException** nella descrizione, un criterio blocca cmd.exe dall'avvio. Bloccando questo programma è possibile evitare l'esecuzione della finestra della console, ovvero ciò che è necessario utilizzare per riavviare il servizio ogni volta che l'agente viene aggiornato.

Per risolvere questo problema, disabilitare i criteri seguenti:
   - Impedisci accesso al prompt dei comandi   
      - Percorso Categoria: Configurazione computer\Modelli Amministrativi\sistema
    
>[!NOTE]
>Questo non è un elenco completo dei criteri, solo quelli attualmente a conoscenza.

Per disabilitare un criterio:
1. Aprire un prompt dei comandi come amministratore.
2. Immettere ed eseguire **RSoP. msc**.
3. Nella finestra del **gruppo di criteri risultante** visualizzata passare al percorso della categoria.
4. Selezionare il criterio.
5. Selezionare **Disabilitato**.
6. Selezionare **Applica**.   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>Errore: il listener dello stack non funziona nella macchina virtuale Windows 10 2004

Eseguire **qwinsta** nel prompt dei comandi e prendere nota del numero di versione visualizzato accanto a **RDP-SxS**. Se non si visualizzano i componenti **RDP-TCP** e **RDP-SxS** , ad **ascoltare** accanto a essi o se non vengono visualizzati dopo l'esecuzione di **qwinsta**, significa che si è verificato un problema dello stack. Gli aggiornamenti dello stack vengono installati insieme agli aggiornamenti degli agenti e, quando l'installazione non viene completata, il listener del desktop virtuale di Windows non funzionerà.

Per risolvere il problema:
1. Aprire l'editor del Registro di sistema.
2. Passare a **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations**.
3. In **WinStations** è possibile visualizzare diverse cartelle per diverse versioni dello stack, selezionare la cartella che corrisponde alle informazioni sulla versione visualizzate durante l'esecuzione di **qwinsta** nel prompt dei comandi.
4. Trovare **fReverseConnectMode** e verificare che il valore dei dati sia **1**. Verificare inoltre che **fEnableWinStation** sia impostato su **1**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot di fReverseConnectMode](media/fenable-2.png)

5. Se **fReverseConnectMode** non è impostato su **1**, selezionare **fReverseConnectMode** e immettere **1** nel campo relativo al valore. 
6. Se **fEnableWinStation** non è impostato su **1**, selezionare **fEnableWinStation** e immettere **1** nel campo relativo al valore.
7. Riavviare la VM. 

>[!NOTE]
>Per modificare la modalità **fReverseConnectMode** o **FEnableWinStation** per più macchine virtuali alla volta, è possibile eseguire una delle due operazioni seguenti:
>
>- Esportare la chiave del registro di sistema dal computer già in uso e importarla in tutti gli altri computer che necessitano di questa modifica.
>- Creare un oggetto Criteri generale (GPO) che imposta il valore della chiave del registro di sistema per i computer che necessitano della modifica.

7. Passare a **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **ClusterSettings**.
8. In **ClusterSettings** trovare **SessionDirectoryListener** e verificare che il valore dei dati sia **RDP-SxS...**.
9. Se **SessionDirectoryListener** non è impostato su **RDP-SxS...**, è necessario seguire la procedura descritta nella sezione [disinstallare l'agente e il caricatore di avvio](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs) per disinstallare prima i componenti agente, caricatore di avvio e stack, quindi [reinstallare l'agente e il caricatore di avvio](#step-4-reinstall-the-agent-and-boot-loader). Verrà reinstallato lo stack affiancato.

## <a name="error-users-keep-getting-disconnected-from-session-hosts"></a>Errore: gli utenti continuano a essere disconnessi dagli host della sessione

Passare a **Visualizzatore eventi**  >  **applicazione registri di Windows**  >  . Se viene visualizzato un evento con ID 0, ovvero **CheckSessionHostDomainIsReachableAsync** nella descrizione e/o gli utenti continuano a essere disconnessi dagli host della sessione, il server non preleva un heartbeat dal servizio desktop virtuale di Windows.

Per risolvere questo problema, modificare la soglia di heartbeat:
1. Aprire il prompt dei comandi come amministratore.
2. Immettere il comando **qwinsta** ed eseguirlo.
3. Dovrebbero essere visualizzati due componenti dello stack: **RDP-TCP** e **RDP-SxS**. 
   - A seconda della versione del sistema operativo in uso, **RDP-SxS** può essere seguito dal numero di Build. In caso contrario, assicurarsi di scrivere questo numero per un momento successivo.
4. Aprire l'editor del Registro di sistema.
5. Passare a **HKEY_LOCAL_MACHINE**  >  **System**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations**.
6. In **WinStations** è possibile visualizzare diverse cartelle per diverse versioni dello stack. Selezionare la cartella corrispondente al numero di versione del passaggio 3.
7. Per creare un nuovo DWORD del registro di sistema, fare clic con il pulsante destro del mouse sull'editor del registro di sistema, quindi scegliere **nuovo**  >  **valore DWORD (32 bit)**. Quando si crea il valore DWORD, immettere i valori seguenti:
   - HeartbeatInterval: 10000
   - HeartbeatWarnCount: 30 
   - HeartbeatDropCount: 60 
8. Riavviare la VM.

## <a name="error-downloadmsiexception"></a>Errore: DownloadMsiException

Passare a **Visualizzatore eventi**  >  **applicazione registri di Windows**  >  . Se viene visualizzato un evento con ID 3277, che indica **DownloadMsiException** nella descrizione, lo spazio sul disco non è sufficiente per il RDAgent.

Per risolvere questo problema, creare spazio sul disco:
   - Eliminazione di file che non sono più presenti nell'utente
   - Aumento della capacità di archiviazione della macchina virtuale

## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>Errore: le macchine virtuali sono bloccate in stato non disponibile o in stato di aggiornamento

Aprire una finestra di PowerShell come amministratore ed eseguire il cmdlet seguente:

```powershell
Get-AzWvdSessionHost -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> | Select-Object *
```

Se lo stato elencato per l'host della sessione o gli host nel pool di host è sempre non **disponibile** o in corso di **aggiornamento**, l'installazione dell'agente o dello stack potrebbe non riuscire

Per risolvere questo problema, reinstallare lo stack affiancato:
1. Aprire un prompt dei comandi come amministratore.
2. Immettere **net stop RDAgentBootLoader**. 
3. Passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità**.
4. Disinstallare la versione più recente dello **stack di rete Servizi Desktop remoto SxS** o la versione elencata in **HKEY_LOCAL_MACHINE**  >    >  **controllo CurrentControlSet** di sistema  >    >  **Terminal Server**  >  **WinStations** in **ReverseConnectListener**.
5. Aprire una finestra della console come amministratore e passare a **programmi**  >  **Microsoft RDInfra**.
6. Selezionare il componente **SxSStack** o eseguire il comando **msiexec/i SxSStack- <version> . msi** per installare il file MSI.
8. Riavviare la VM.
9. Tornare al prompt dei comandi ed eseguire il comando **qwinsta** .
10. Verificare che il componente dello stack installato nel passaggio 6 indichi l' **ascolto** accanto.
   - In tal caso, immettere **net start RDAgentBootLoader** nel prompt dei comandi e riavviare la macchina virtuale.
   - In caso contrario, sarà necessario [registrare di nuovo la macchina virtuale e reinstallare il componente dell'agente](#your-issue-isnt-listed-here-or-wasnt-resolved) .

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>Errore: Impossibile trovare la connessione: RDAgent non dispone di una connessione attiva al broker

Le macchine virtuali potrebbero essere al limite della connessione, quindi la macchina virtuale non può accettare nuove connessioni.

Per risolvere il problema:
   - Ridurre il limite massimo della sessione. In questo modo si garantisce che le risorse vengano distribuite in modo più uniforme tra gli host della sessione e impediscano il consumo delle risorse.
   - Aumentare la capacità delle risorse delle macchine virtuali.

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>Errore: uso di una VM Pro o di un altro sistema operativo non supportato

Lo stack affiancato è supportato solo da SKU Windows Enterprise o Windows Server, il che significa che i sistemi operativi come la VM Pro non lo sono. Se non si dispone di uno SKU aziendale o server, lo stack verrà installato nella macchina virtuale, ma non verrà attivato, quindi non verrà visualizzato quando si esegue **qwinsta** nella riga di comando.

Per risolvere questo problema, creare una macchina virtuale Windows Enterprise o Windows Server.
1. Passare a [Dettagli macchina virtuale](create-host-pools-azure-marketplace.md#virtual-machine-details) e seguire i passaggi 1-12 per configurare una delle seguenti immagini consigliate:
   - Windows 10 Enterprise multisessione, versione 1909
   - Windows 10 Enterprise multisessione, versione 1909 + Microsoft 365 app
   - Windows Server 2019 Datacenter
   - Windows 10 Enterprise multisessione, versione 2004
   - Windows 10 Enterprise multisessione, versione 2004 + Microsoft 365 app
2. Selezionare **revisione e creazione**.

## <a name="error-name_already_registered"></a>Errore: NAME_ALREADY_REGISTERED

Il nome della macchina virtuale è già stato registrato ed è probabilmente un duplicato.

Per risolvere il problema:
1. Seguire i passaggi descritti nella sezione [rimuovere il host sessione dal pool di host](#step-2-remove-the-session-host-from-the-host-pool) .
2. [Creare un'altra macchina virtuale](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal). Assicurarsi di scegliere un nome univoco per questa macchina virtuale.
3. Passare alla portale di Azure] ( https://portal.azure.com) e aprire la pagina **Panoramica** per il pool host in cui si trovava la macchina virtuale. 
4. Aprire la scheda **host sessione** e verificare che tutti gli host della sessione si trovino nel pool host.
5. Attendere 5-10 minuti per indicare che lo stato dell'host sessione è **disponibile**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot dell'host sessione disponibile](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>Il problema non è elencato qui o non è stato risolto

Se il problema non è stato trovato in questo articolo o le istruzioni non sono state utili, è consigliabile disinstallare, reinstallare e registrare di nuovo l'agente desktop virtuale di Windows. Le istruzioni riportate in questa sezione illustrano come registrare nuovamente la VM nel servizio desktop virtuale di Windows disinstallando tutti i componenti agente, caricatore di avvio e stack, rimuovendo l'host sessione dal pool host, generando una nuova chiave di registrazione per la macchina virtuale e reinstallando l'agente e il caricatore di avvio. Se si applicano uno o più degli scenari seguenti, seguire queste istruzioni:
- La macchina virtuale è bloccata durante l' **aggiornamento** o non è **disponibile**
- Il listener dello stack non funziona ed è in esecuzione in Windows 10 1809, 1903 o 1904
- Si riceve un errore di **EXPIRED_REGISTRATION_TOKEN**
- Le macchine virtuali non vengono visualizzate nell'elenco degli host della sessione
- Il **caricatore dell'agente di desktop remoto** non viene visualizzato nella finestra Servizi
- Il componente **RdAgentBootLoader** non è visualizzato in Gestione attività
- Le istruzioni riportate in questo articolo non hanno risolto il problema

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>Passaggio 1: disinstallare tutti i programmi Agent, boot loader e stack Component

Prima di reinstallare l'agente, il caricatore di avvio e lo stack, è necessario disinstallare tutti i programmi componente esistenti dalla macchina virtuale. Per disinstallare tutti i programmi agente, caricatore di avvio e componente dello stack:
1. Accedere alla macchina virtuale come amministratore. 
2. Passare a **Pannello di controllo** > **Programmi** > **Programmi e funzionalità**.
3. Rimuovere i programmi seguenti:
   - Caricatore di avvio dell'agente di Desktop remoto
   - Agente di infrastruttura Servizi Desktop remoto
   - Agente di Servizi Desktop remoto infrastruttura Geneva
   - Stack di rete Servizi Desktop remoto SxS
   
>[!NOTE]
>È possibile che vengano visualizzate più istanze di questi programmi. Assicurarsi di rimuoverli tutti.

   > [!div class="mx-imgBorder"]
   > ![Screenshot della disinstallazione dei programmi](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>Passaggio 2: rimuovere l'host sessione dal pool host

Quando si rimuove l'host sessione dal pool host, l'host sessione non è più registrato in tale pool host. Questa operazione funge da reimpostazione per la registrazione dell'host sessione. Per rimuovere l'host sessione dal pool host:
1. Passare alla pagina **Panoramica** per il pool host in cui si trova la macchina virtuale, nella [portale di Azure](https://portal.azure.com). 
2. Passare alla scheda **host sessione** per visualizzare l'elenco di tutti gli host di sessione nel pool host.
3. Esaminare l'elenco degli host di sessione e selezionare la macchina virtuale che si vuole rimuovere.
4. Selezionare **Rimuovi**.  

   > [!div class="mx-imgBorder"]
   > ![Screenshot della rimozione della macchina virtuale dal pool di host](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>Passaggio 3: generare una nuova chiave di registrazione per la macchina virtuale

È necessario generare una nuova chiave di registrazione usata per registrare nuovamente la macchina virtuale nel pool host e nel servizio. Per generare una nuova chiave di registrazione per la macchina virtuale:
1. Aprire il [portale di Azure](https://portal.azure.com) e passare alla pagina **Panoramica** per il pool host della macchina virtuale che si vuole modificare.
2. Selezionare la **chiave di registrazione**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot della chiave di registrazione nel portale](media/reg-key.png)

3. Aprire la scheda **chiave di registrazione** e selezionare **genera nuova chiave**.
4. Immettere la data di scadenza e quindi fare clic su **OK**.  

>[!NOTE]
>La data di scadenza non può essere inferiore a un'ora e non più di 27 giorni dalla data e dall'ora di generazione. È consigliabile impostare la data di scadenza su un massimo di 27 giorni.

5. Copiare la chiave appena generata negli Appunti. Questa chiave sarà necessaria in un secondo momento.

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>Passaggio 4: reinstallare l'agente e il caricatore di avvio

Reinstallando la versione più aggiornata dell'agente e del caricatore di avvio, viene installato automaticamente anche lo stack affiancato e l'agente di monitoraggio di Ginevra. Per reinstallare l'agente e il caricatore di avvio:
1. Accedere alla macchina virtuale come amministratore e seguire le istruzioni in registrare le [macchine virtuali](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) per scaricare l' **agente desktop virtuale di Windows** e il **bootloader dell'agente desktop virtuale di Windows**.

   > [!div class="mx-imgBorder"]
   > ![Screenshot della pagina di download di Agent e bootloader](media/download-agent.png)

2. Fare clic con il pulsante destro del mouse sull'agente e sui programmi di installazione del caricatore di avvio appena scaricati.
3. Selezionare **Proprietà**.
4. Selezionare **Unblock** (Sblocca).
5. Selezionare **OK**.
6. Eseguire il programma di installazione dell'agente.
7. Quando il programma di installazione richiede il token di registrazione, incollare la chiave di registrazione dagli Appunti. 

   > [!div class="mx-imgBorder"]
   > ![Screenshot del token di registrazione incollato](media/pasted-agent-token.png)

8. Eseguire il programma di installazione del caricatore di avvio.
9. Riavviare la VM. 
10. Passare alla [portale di Azure](https://portal.azure.com) e aprire la pagina **Panoramica** per il pool di host a cui appartiene la macchina virtuale.
11. Passare alla scheda **host sessione** per visualizzare l'elenco di tutti gli host di sessione nel pool host.
12. A questo punto dovrebbe essere visualizzato l'host sessione registrato nel pool di host con lo stato **disponibile**. 

   > [!div class="mx-imgBorder"]
   > ![Screenshot dell'host sessione disponibile](media/hostpool-portal.png)

## <a name="next-steps"></a>Passaggi successivi

Se il problema persiste, creare una richiesta di supporto e includere informazioni dettagliate sul problema che si sta verificando e sulle azioni intraprese per tentare di risolverlo. Nell'elenco seguente sono incluse altre risorse che è possibile utilizzare per risolvere i problemi relativi alla distribuzione di desktop virtuali Windows.

- Per una panoramica sulla risoluzione dei problemi relativi a Desktop virtuale Windows e alle tracce di escalation, consultare [Panoramica della risoluzione dei problemi, feedback e supporto](troubleshoot-set-up-overview.md).
- Per risolvere i problemi durante la creazione di un pool host in un ambiente desktop virtuale Windows, vedere la pagina relativa alla [creazione di ambienti e pool host](troubleshoot-set-up-issues.md).
- Per risolvere i problemi durante la configurazione di una macchina virtuale (VM) in Desktop virtuale Windows, consultare [Configurazione di macchine virtuali nell'host sessione](troubleshoot-vm-configuration.md).
- Per risolvere i problemi relativi alle connessioni client di desktop virtuali Windows, vedere [connessioni al servizio desktop virtuale di Windows](troubleshoot-service-connection.md).
- Per risolvere i problemi relativi ai client di Desktop remoto, vedere [Troubleshoot the desktop remoto client](troubleshoot-client.md).
- Per risolvere i problemi relativi all'uso di PowerShell con Desktop virtuale di Windows, consultare [PowerShell con Desktop virtuale Windows](troubleshoot-powershell.md).
- Per ulteriori informazioni sul servizio, vedere [ambiente desktop virtuale di Windows](environment-setup.md).
- Per eseguire un'esercitazione di risoluzione dei problemi, vedere [Esercitazione: Risolvere i problemi delle distribuzioni dei modelli di Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Per altre informazioni sulle azioni di controllo, vedere [Operazioni di controllo con Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Per altre informazioni sulle azioni che consentono di determinare gli errori di distribuzione, vedere [Visualizzare le operazioni di distribuzione con il portale di Azure](../azure-resource-manager/templates/deployment-history.md).
