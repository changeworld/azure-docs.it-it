---
title: Portale di montaggio app MSIX Desktop virtuale Windows - Azure
description: Come configurare montaggio app MSIX desktop virtuale Windows usando il portale di Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 21dbab6c8d4fb12fe79434a6994dd7f5b8a49190
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502709"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Configurare la connessione all'app MSIX con il portale di Azure

Questo articolo illustra come configurare montaggio app MSIX in un ambiente Desktop virtuale Windows.

## <a name="requirements"></a>Requisiti

Ecco cosa è necessario per configurare montaggio app MSIX:

- Una distribuzione di Desktop virtuale Windows funzionante. Per informazioni su come distribuire Desktop virtuale Windows (versione classica), vedere [Creare un tenant in Desktop virtuale Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Per informazioni su come distribuire Desktop virtuale Windows con Azure Resource Manager, vedere [Creare un pool di host](./create-host-pools-azure-marketplace.md)con il portale di Azure .
- Pool di host desktop virtuale Windows con almeno un host di sessione attivo.
- Strumento di creazione pacchetti MSIX.
- Un'applicazione in pacchetto MSIX è stata espansa in un'immagine MSIX caricata in una condivisione file.
- Condivisione file nella distribuzione di Desktop virtuale Windows in cui verrà archiviato il pacchetto MSIX.
- La condivisione file in cui è stata caricata l'immagine MSIX deve essere accessibile anche a tutte le macchine virtuali nel pool host. Gli utenti dovranno disporre di autorizzazioni di sola lettura per accedere all'immagine.
- Se il certificato non è attendibile pubblicamente, seguire le istruzioni in [Installare i certificati](app-attach.md#install-certificates).

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Disattivare gli aggiornamenti automatici per montaggio app MSIX applicazioni

Prima di iniziare, è necessario disabilitare gli aggiornamenti automatici per montaggio app MSIX applicazioni. Per disabilitare gli aggiornamenti automatici, è necessario eseguire i comandi seguenti in un prompt dei comandi con privilegi elevati:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

```

>[!NOTE]
>È consigliabile riavviare la macchina virtuale dopo aver abilitato Hyper-V.

## <a name="configure-the-msix-app-attach-management-interface"></a>Configurare l'montaggio app MSIX di gestione dei dati

Sarà quindi necessario scaricare e configurare l'interfaccia di gestione montaggio app MSIX per l'portale di Azure.

Per configurare l'interfaccia di gestione:

1. [Aprire il portale di Azure](https://portal.azure.com).
2. Se viene visualizzato un messaggio che chiede se si considera attendibile l'estensione, selezionare **Consenti**.

      > [!div class="mx-imgBorder"]
      > ![Screenshot della finestra estensioni non attendibili. "Consenti" è evidenziato in rosso.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Aggiungere un'immagine MSIX al pool di host

Sarà quindi necessario aggiungere l'immagine MSIX al pool di host.

Per aggiungere l'immagine MSIX:

1. Aprire il portale di Azure.

2. Immettere **Desktop virtuale Windows** nella barra di ricerca e quindi selezionare il nome del servizio.

      > [!div class="mx-imgBorder"]
      > ![Screenshot di un utente che seleziona "Desktop virtuale Windows" dal menu a discesa della barra di ricerca nella portale di Azure. "Desktop virtuale Windows" è evidenziato in rosso.](media/find-and-select.png)

3. Selezionare il pool di host in cui si prevede di inserire le app MSIX.

4. Selezionare **Pacchetti MSIX** per aprire la griglia dei dati con tutti **i pacchetti MSIX** attualmente aggiunti al pool host.

5. Selezionare **+ Aggiungi** per aprire la scheda Aggiungi pacchetto **MSIX.**

6. Nella scheda **Aggiungi pacchetto MSIX** immettere i valori seguenti:

      - Per **Percorso immagine MSIX** immettere un percorso UNC valido che punta all'immagine MSIX nella condivisione file. (Ad esempio, `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` .) Al termine, selezionare **Aggiungi** per interrogare il contenitore MSIX per verificare se il percorso è valido.

      - Per **Pacchetto MSIX** selezionare il nome del pacchetto MSIX pertinente dal menu a discesa. Questo menu verrà popolato solo se è stato immesso un percorso di immagine valido nel **percorso immagine MSIX**.

      - Per **Le applicazioni del** pacchetto assicurarsi che l'elenco contenga tutte le applicazioni MSIX che si vuole rendere disponibili agli utenti nel pacchetto MSIX.

      - Facoltativamente, immettere **un nome visualizzato** se si vuole che il pacchetto abbia un nome più descrittivo nelle distribuzioni utente.

      - Assicurarsi che **version abbia** il numero di versione corretto.

      - Selezionare il **tipo di** registrazione che si vuole usare. Quello che si usa dipende dalle proprie esigenze:

    - **La registrazione su richiesta** posticipa la registrazione completa dell'applicazione MSIX fino a quando l'utente non avvia l'applicazione. Si tratta del tipo di registrazione che è consigliabile usare.

    - **Il blocco dell'accesso** viene registrato solo durante l'accesso dell'utente. Questo tipo non è consigliabile perché può comportare tempi di accesso più lunghi per gli utenti.

7.  Per **Stato** selezionare lo stato preferito.
    -  Lo **stato Attivo** consente agli utenti di interagire con il pacchetto.
    -  Lo **stato Inattivo** fa sì che Desktop virtuale Windows ignori il pacchetto e non lo consegnerà agli utenti.

8. Al termine, selezionare **Aggiungi**.

## <a name="publish-msix-apps-to-an-app-group"></a>Pubblicare app MSIX in un gruppo di app

Sarà quindi necessario pubblicare le app nel pacchetto. È necessario eseguire questa operazione sia per i gruppi di applicazioni desktop che per i gruppi di applicazioni remote.

Se si ha già un'immagine MSIX, passare a Pubblicare app [MSIX in un gruppo di app.](#publish-msix-apps-to-an-app-group) Per testare le applicazioni legacy, seguire le istruzioni riportate in [Creare un pacchetto MSIX da un programma di installazione desktop in una macchina virtuale](/windows/msix/packaging-tool/create-app-package-msi-vm/) per convertire l'applicazione legacy in un pacchetto MSIX.

Per pubblicare le app:

1. Nel provider di risorse Desktop virtuale Windows selezionare la **scheda Gruppi di** applicazioni.

2. Selezionare il gruppo di applicazioni in cui pubblicare le app.

   >[!NOTE]
   >Le applicazioni MSIX possono essere recapitate con montaggio app MSIX a gruppi di app remote e desktop

3. Dopo aver fatto parte del gruppo di app, selezionare la **scheda** Applicazioni. Nella **griglia Applicazioni** verranno visualizzate tutte le app esistenti all'interno del gruppo di app.

4. Selezionare **+ Aggiungi** per aprire la scheda **Aggiungi** applicazione.

      > [!div class="mx-imgBorder"]
      > ![Screenshot dell'utente che seleziona + Aggiungi per aprire la scheda Aggiungi applicazione](media/select-add.png)

5. Per **Origine applicazione** scegliere l'origine per l'applicazione.
    - Se si usa un gruppo di app desktop, scegliere **Pacchetto MSIX**.
      
      > [!div class="mx-imgBorder"]
      > ![Screenshot di un cliente che seleziona il pacchetto MSIX dal menu a discesa dell'origine dell'applicazione. Il pacchetto MSIX è evidenziato in rosso.](media/select-source.png)
    
    - Se si usa un gruppo di app remote, scegliere una delle opzioni seguenti:
        
        - Menu Start
        - Percorso dell'app
        - Pacchetto MSIX

    - Per **Nome applicazione** immettere un nome descrittivo per l'applicazione.

    È anche possibile configurare le funzionalità facoltative seguenti:
   
    - Per **Nome visualizzato** immettere un nuovo nome per il pacchetto che verrà visualizzato dagli utenti.

    - In **Descrizione** immettere una breve descrizione del pacchetto dell'app.

    - Se si usa un gruppo di app remote, è anche possibile configurare queste opzioni:

        - **Percorso dell'icona**
        - **Indice delle icone**
        - **Mostra nel feed Web**

6. Al termine, selezionare **Salva**.

>[!NOTE]
>Quando un utente viene assegnato al gruppo di app remote e al gruppo di app desktop dallo stesso pool di host, il gruppo di app desktop verrà visualizzato nel feed.

## <a name="assign-a-user-to-an-app-group"></a>Assegnare un utente a un gruppo di app

Dopo aver assegnato le app MSIX a un gruppo di app, è necessario concedere agli utenti l'accesso. È possibile assegnare l'accesso aggiungendo utenti o gruppi di utenti a un gruppo di app con applicazioni MSIX pubblicate. Seguire le istruzioni in [Gestire i gruppi di app con il portale di Azure](manage-app-groups.md) per assegnare gli utenti a un gruppo di app.

>[!NOTE]
>montaggio app MSIX app remote potrebbero scomparire dal feed quando si testano le app remote durante l'anteprima pubblica. Le app non vengono visualizzate perché il pool di host in uso nell'ambiente di valutazione viene gestito da Un Broker Desktop remoto nell'ambiente di produzione. Poiché Gestore Desktop remoto nell'ambiente di produzione non registra la presenza del montaggio app MSIX remote, le app non verranno visualizzate nel feed.

## <a name="change-msix-package-state"></a>Modificare lo stato del pacchetto MSIX

Sarà quindi necessario modificare lo stato del pacchetto MSIX in **Attivo** o **Inattivo,** a seconda delle attività da eseguire con il pacchetto. I pacchetti attivi sono pacchetti con cui gli utenti possono interagire dopo la pubblicazione. I pacchetti inattivi vengono ignorati da Desktop virtuale Windows, quindi gli utenti non possono interagire con le app all'interno.

### <a name="change-state-with-the-applications-list"></a>Modificare lo stato con l'elenco Applicazioni

Per modificare lo stato del pacchetto con l'elenco Delle applicazioni:

1. Passare al pool di host e selezionare **Pacchetti MSIX**. Verrà visualizzato un elenco di tutti i pacchetti MSIX esistenti all'interno del pool di host.

2. Selezionare i pacchetti MSIX di cui è necessario modificare gli stati, quindi selezionare **Cambia stato**.

### <a name="change-state-with-update-package"></a>Modificare lo stato con il pacchetto di aggiornamento

Per modificare lo stato del pacchetto con un pacchetto di aggiornamento:

1. Passare al pool di host e selezionare **Pacchetti MSIX**. Verrà visualizzato un elenco di tutti i pacchetti MSIX esistenti all'interno del pool host.

2. Selezionare il nome del pacchetto di cui si vuole modificare lo stato dall'elenco dei pacchetti MSIX. Verrà aperta la **scheda Aggiorna** pacchetto.

3. Impostare **l'opzione** Stato su **Inattivo** **o Attivo** e quindi selezionare **Salva.**

## <a name="change-msix-package-registration-type"></a>Modificare il tipo di registrazione del pacchetto MSIX

Per modificare il tipo di registrazione del pacchetto:

1. Selezionare **Pacchetti MSIX**. Verrà visualizzato un elenco di tutti i pacchetti MSIX esistenti all'interno del pool host.

2. Selezionare **Nome pacchetto nella** griglia Pacchetti **MSIX** per aprire il pannello per aggiornare il pacchetto.

3. Attivare o **disattivare** il tipo di registrazione tramite il pulsante Di blocco su **richiesta/Accesso** come desiderato e selezionare **Salva.**

## <a name="remove-an-msix-package"></a>Rimuovere un pacchetto MSIX

Per rimuovere un pacchetto MSIX dal pool di host:

1. Selezionare **Pacchetti MSIX**.  Verrà visualizzato un elenco di tutti i pacchetti MSIX esistenti all'interno del pool host.

2. Selezionare i puntini di sospensione sul lato destro del nome del pacchetto da eliminare, quindi selezionare **Rimuovi**.

## <a name="remove-msix-apps"></a>Rimuovere le app MSIX

Per rimuovere singole app MSIX dal pacchetto:

1. Passare al pool di host e selezionare **Gruppi di applicazioni**.

2. Selezionare il gruppo di applicazioni da cui rimuovere le app MSIX.

3. Aprire la **scheda** Applicazioni.

4. Selezionare l'app da rimuovere, quindi **selezionare Rimuovi**.

## <a name="next-steps"></a>Passaggi successivi

Porre domande alla community su questa funzionalità in [TechCommunity di Desktop virtuale Windows.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

È anche possibile lasciare feedback per Desktop virtuale Windows nell'[hub di commenti e suggerimenti per Desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Ecco alcuni altri articoli che possono risultare utili:

- [montaggio app MSIX glossario](app-attach-glossary.md)
- [montaggio app MSIX domande frequenti](app-attach-faq.md)
