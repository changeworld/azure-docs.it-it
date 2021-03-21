---
title: Windows Virtual Desktop MSIX app per il portale di connessione anteprima-Azure
description: Come configurare la connessione all'app MSIX per desktop virtuale di Windows usando il portale di Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 02/11/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: c775d81b88c891d6d8ea0a4597b4fa4fee29c86a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737546"
---
# <a name="set-up-msix-app-attach-with-the-azure-portal"></a>Configurare la connessione all'app MSIX con il portale di Azure

> [!IMPORTANT]
> La connessione all'app MSIX è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Questo articolo illustra come configurare la connessione all'app MSIX (anteprima) in un ambiente desktop virtuale di Windows.

## <a name="requirements"></a>Requisiti

>[!IMPORTANT]
>Prima di iniziare, assicurarsi di compilare e inviare [il modulo](https://aka.ms/enablemsixappattach) per abilitare la connessione dell'app MSIX nella sottoscrizione. Se non si ha una richiesta approvata, la connessione all'app MSIX non funzionerà. L'approvazione delle richieste può richiedere fino a 24 ore durante i giorni lavorativi. Quando la richiesta è stata accettata e completata, si riceverà un messaggio di posta elettronica.

Ecco cosa occorre per configurare la connessione all'app MSIX:

- Una distribuzione di Desktop virtuale Windows funzionante. Per informazioni su come distribuire desktop virtuale di Windows (versione classica), vedere [creare un tenant in un desktop virtuale di Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Per informazioni su come distribuire desktop virtuale di Windows con l'integrazione di Azure Resource Manager, vedere [creare un pool host con l'portale di Azure](./create-host-pools-azure-marketplace.md).
- Un pool host di desktop virtuali Windows con almeno un host sessione attiva.
- Questo pool host deve trovarsi nell'ambiente di convalida. 
- Strumento per la creazione di pacchetti MSIX.
- Un'applicazione in pacchetto MSIX è stata espansa in un'immagine MSIX che viene caricata in una condivisione file.
- Una condivisione file nella distribuzione di desktop virtuale di Windows in cui verrà archiviato il pacchetto MSIX.
- La condivisione file in cui è stata caricata l'immagine di MSIX deve essere accessibile anche a tutte le macchine virtuali (VM) nel pool host. Per accedere all'immagine, gli utenti dovranno disporre di autorizzazioni di sola lettura.
- Se il certificato non è attendibile pubblicamente, seguire le istruzioni riportate in [installare i certificati](app-attach.md#install-certificates).

## <a name="turn-off-automatic-updates-for-msix-app-attach-applications"></a>Disattivare gli aggiornamenti automatici per le applicazioni di alconnessione app MSIX

Prima di iniziare, è necessario disabilitare gli aggiornamenti automatici per le applicazioni di connessione all'app MSIX. Per disabilitare gli aggiornamenti automatici, è necessario eseguire i comandi seguenti in un prompt dei comandi con privilegi elevati:

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
>Si consiglia di riavviare la macchina virtuale dopo l'abilitazione di Hyper-V.

## <a name="configure-the-msix-app-attach-management-interface"></a>Configurare l'interfaccia di gestione di associazione app MSIX

A questo punto, è necessario scaricare e configurare l'interfaccia di gestione di MSIX app di connessione per il portale di Azure.

Per configurare l'interfaccia di gestione:

1. [Aprire il portale di Azure](https://portal.azure.com).
2. Se viene richiesto di prendere in considerazione l'estensione Trustworthy, selezionare **Consenti**.

      > [!div class="mx-imgBorder"]
      > ![Screenshot della finestra estensioni non attendibili. "Allow" è evidenziato in rosso.](media/untrusted-extensions.png)


## <a name="add-an-msix-image-to-the-host-pool"></a>Aggiungere un'immagine MSIX al pool host

Sarà quindi necessario aggiungere l'immagine MSIX al pool host.

Per aggiungere l'immagine MSIX:

1. Aprire il portale di Azure.

2. Immettere **desktop virtuale Windows** nella barra di ricerca, quindi selezionare il nome del servizio.

      > [!div class="mx-imgBorder"]
      > ![Screenshot di un utente che seleziona "desktop virtuale Windows" dal menu a discesa della barra di ricerca nel portale di Azure. "Desktop virtuale Windows" è evidenziato in rosso.](media/find-and-select.png)

3. Selezionare il pool host in cui si intende inserire le app MSIX.

4. Selezionare i **pacchetti MSIX** per aprire la griglia dei dati con tutti i **pacchetti MSIX** attualmente aggiunti al pool host.

5. Selezionare **+ Aggiungi** per aprire la scheda **Aggiungi pacchetto di MSIX** .

6. Nella scheda **Aggiungi pacchetto di MSIX** immettere i valori seguenti:

      - Per **MSIX Image Path** immettere un percorso UNC valido che punta all'immagine MSIX nella condivisione file. (Ad esempio, `\\storageaccount.file.core.windows.net\msixshare\appfolder\MSIXimage.vhd` ). Al termine, selezionare **Aggiungi** per interrogare il contenitore MSIX per verificare se il percorso è valido.

      - Per il **pacchetto MSIX** selezionare il nome del pacchetto MSIX pertinente dal menu a discesa. Questo menu verrà popolato solo se è stato immesso un percorso immagine valido in **MSIX Image Path**.

      - Per le **applicazioni di pacchetto**, verificare che l'elenco contenga tutte le applicazioni MSIX che si desidera siano disponibili per gli utenti nel pacchetto MSIX.

      - Facoltativamente, immettere un **nome visualizzato** se si desidera che il pacchetto disponga di una più intuitiva distribuzione utente.

      - Verificare che la **versione** abbia il numero di versione corretto.

      - Selezionare il **tipo di registrazione** che si vuole usare. Quello che si usa dipende dalle esigenze:

    - La **registrazione su richiesta** posticipa la registrazione completa dell'applicazione MSIX fino a quando l'utente non avvia l'applicazione. Questo è il tipo di registrazione che si consiglia di usare.

    - **Log on blocking** registra solo quando l'utente esegue l'accesso. Questo tipo non è consigliato perché può comportare tempi di accesso più lunghi per gli utenti.

7.  Per **stato** selezionare lo stato preferito.
    -  Lo stato **attivo** consente agli utenti di interagire con il pacchetto.
    -  Lo stato **inattivo** fa in modo che desktop virtuale di Windows ignori il pacchetto e non lo fornisca agli utenti.

8. Al termine, selezionare **Aggiungi**.

## <a name="publish-msix-apps-to-an-app-group"></a>Pubblicare app MSIX in un gruppo di app

Successivamente, sarà necessario pubblicare le app nel pacchetto. È necessario eseguire questa operazione per i gruppi di applicazioni desktop e remote.

Se si dispone già di un'immagine MSIX, passare a [pubblicare app MSIX in un gruppo di app](#publish-msix-apps-to-an-app-group). Per testare le applicazioni legacy, seguire le istruzioni riportate in [Creare un pacchetto MSIX da un programma di installazione desktop in una macchina virtuale](/windows/msix/packaging-tool/create-app-package-msi-vm/) per convertire l'applicazione legacy in un pacchetto MSIX.

Per pubblicare le app:

1. Nel provider di risorse desktop virtuale Windows selezionare la scheda **gruppi di applicazioni** .

2. Selezionare il gruppo di applicazioni in cui si vuole pubblicare le app.

   >[!NOTE]
   >Le applicazioni MSIX possono essere recapitate con la connessione di app MSIX a gruppi di app desktop e app Remote

3. Quando ci si trova nel gruppo app, selezionare la scheda **applicazioni** . Nella griglia **applicazioni** vengono visualizzate tutte le app esistenti all'interno del gruppo di app.

4. Selezionare **+ Aggiungi** per aprire la scheda **Aggiungi applicazione** .

      > [!div class="mx-imgBorder"]
      > ![Screenshot dell'utente che seleziona + Aggiungi per aprire la scheda Aggiungi applicazione](media/select-add.png)

5. Per **origine applicazione**, scegliere l'origine per l'applicazione.
    - Se si usa un gruppo di app desktop, scegliere il **pacchetto MSIX**.
      
      > [!div class="mx-imgBorder"]
      > ![Screenshot di un cliente che seleziona il pacchetto MSIX dal menu a discesa origine applicazione. Il pacchetto MSIX è evidenziato in rosso.](media/select-source.png)
    
    - Se si usa un gruppo di app Remote, scegliere una delle opzioni seguenti:
        
        - Menu Start
        - Percorso app
        - Pacchetto MSIX

    - Per **nome applicazione** immettere un nome descrittivo per l'applicazione.

    È anche possibile configurare le funzionalità facoltative seguenti:
   
    - Per **nome visualizzato** immettere un nuovo nome per il pacchetto che gli utenti visualizzeranno.

    - Per **Description (Descrizione**) immettere una breve descrizione del pacchetto dell'app.

    - Se si usa un gruppo di app Remote, è anche possibile configurare queste opzioni:

        - **Percorso icona**
        - **Indice icona**
        - **Mostra nel feed Web**

6. Al termine, selezionare **Salva**.

>[!NOTE]
>Quando un utente viene assegnato a un gruppo di app Remote e a un gruppo di app desktop dallo stesso pool di host, il gruppo di app desktop verrà visualizzato nel feed.

## <a name="assign-a-user-to-an-app-group"></a>Assegnare un utente a un gruppo di app

Dopo aver assegnato le app MSIX a un gruppo di app, è necessario concedere agli utenti l'accesso. È possibile assegnare l'accesso aggiungendo utenti o gruppi di utenti a un gruppo di app con le applicazioni MSIX pubblicate. Seguire le istruzioni riportate in [gestire i gruppi di app con la portale di Azure](manage-app-groups.md) per assegnare gli utenti a un gruppo di app.

>[!NOTE]
>Le app Remote di collegamento app MSIX possono scomparire dal feed quando si testano le app Remote durante l'anteprima pubblica. Le app non vengono visualizzate perché il pool host che si sta usando nell'ambiente di valutazione viene servito da un broker desktop remoto nell'ambiente di produzione. Poiché il broker di desktop remoto nell'ambiente di produzione non registra la presenza dell'app MSIX Connetti app Remote, le app non verranno visualizzate nel feed.

## <a name="change-msix-package-state"></a>Modificare lo stato del pacchetto MSIX

A questo punto, sarà necessario modificare lo stato del pacchetto MSIX impostando **attivo** o **inattivo**, a seconda di ciò che si desidera eseguire con il pacchetto. I pacchetti attivi sono pacchetti con cui gli utenti possono interagire dopo la pubblicazione. I pacchetti inattivi vengono ignorati da desktop virtuale di Windows, in modo che gli utenti non possano interagire con le app all'interno di.

### <a name="change-state-with-the-applications-list"></a>Modificare lo stato con l'elenco delle applicazioni

Per modificare lo stato del pacchetto con l'elenco delle applicazioni:

1. Passare al pool host e selezionare **pacchetti MSIX**. Verrà visualizzato un elenco di tutti i pacchetti MSIX esistenti all'interno del pool host.

2. Selezionare i pacchetti MSIX i cui Stati è necessario modificare, quindi selezionare **modifica stato**.

### <a name="change-state-with-update-package"></a>Modificare lo stato con il pacchetto di aggiornamento

Per modificare lo stato del pacchetto con un pacchetto di aggiornamento:

1. Passare al pool host e selezionare **pacchetti MSIX**. Verrà visualizzato un elenco di tutti i pacchetti MSIX esistenti all'interno del pool host.

2. Selezionare il nome del pacchetto di cui si desidera modificare lo stato dall'elenco di pacchetti MSIX. Verrà visualizzata la scheda **Aggiorna pacchetto** .

3. Attivare o disattivare l'opzione **stato** su **inattivo** o **attivo**, quindi selezionare **Salva.**

## <a name="change-msix-package-registration-type"></a>Modificare il tipo di registrazione del pacchetto MSIX

Per modificare il tipo di registrazione del pacchetto:

1. Selezionare i **pacchetti MSIX**. Verrà visualizzato un elenco di tutti i pacchetti MSIX esistenti all'interno del pool host.

2. Selezionare **nome pacchetto nella** **griglia pacchetti MSIX** . verrà aperto il pannello per aggiornare il pacchetto.

3. Consente di impostare il **tipo di registrazione** tramite il pulsante di **blocco su richiesta/accesso** in modo desiderato e selezionare **Salva.**

## <a name="remove-an-msix-package"></a>Rimuovere un pacchetto MSIX

Per rimuovere un pacchetto MSIX dal pool host:

1. Selezionare i **pacchetti MSIX**.  Verrà visualizzato un elenco di tutti i pacchetti MSIX esistenti all'interno del pool host.

2. Selezionare i puntini di sospensione sul lato destro del nome del pacchetto che si desidera eliminare, quindi selezionare **Rimuovi**.

## <a name="remove-msix-apps"></a>Rimuovi app MSIX

Per rimuovere le singole app MSIX dal pacchetto:

1. Passare al pool host e selezionare **gruppi di applicazioni**.

2. Selezionare il gruppo di applicazioni da cui si vuole rimuovere le app MSIX.

3. Aprire la scheda **applicazioni** .

4. Selezionare l'app che si vuole rimuovere, quindi selezionare **Rimuovi**.

## <a name="next-steps"></a>Passaggi successivi

Porre le domande della community su questa funzionalità nel [desktop virtuale di Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

È anche possibile lasciare feedback per Desktop virtuale Windows nell'[hub di commenti e suggerimenti per Desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Di seguito sono riportati alcuni altri articoli che possono risultare utili:

- [Glossario per il fissaggio dell'app MSIX](app-attach-glossary.md)
- [Domande frequenti sull'aggiunta di app MSIX](app-attach-faq.md)
