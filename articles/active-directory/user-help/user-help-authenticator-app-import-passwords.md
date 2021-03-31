---
title: Importare le password nell'app Microsoft Authenticator-Azure AD
description: Come importare le password nell'app Microsoft Authentication dai gestori delle password più diffusi.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ecc6580148dfba92077336a26ff9160fbe88eb2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99806156"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>Importare le password nell'app Microsoft Authenticator

Microsoft Authenticator supporta l'importazione di password da Google Chrome, Firefox, LastPass, Bitwarden e RoboForm. Se Microsoft non supporta attualmente il gestore delle password esistente, è possibile [immettere manualmente le credenziali di accesso nel file CSV del modello](https://go.microsoft.com/fwlink/?linkid=2134938). Per importare le password esistenti e gestirle nell'app Authenticator, è sufficiente esportare le password da Gestione password esistente nel formato con valori delimitati da virgole (CSV). Importare quindi il file CSV esportato in Authenticator nell'estensione del browser Chrome o direttamente nell'app Authenticator (Android e iOS).

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Importa da Google Chrome o Android Smart Lock

È possibile importare le password da Google Chrome o Android Smart Lock in Authenticator sullo smartphone o sul computer desktop. È possibile:

- [Importa da Chrome in Android e iOS](#import-from-chrome-on-android-and-ios)
- [Importare da Chrome Desktop browser](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Importa da Chrome in Android e iOS

Gli utenti di Google Chrome sui telefoni Android e Apple possono importare le proprie password direttamente dal telefono con pochi semplici passaggi.

1. Installare l'app Authenticator sul telefono e aprire la scheda **password** .

1. Accedere a Google Chrome sul telefono.

1. Toccare il ![ menu con i puntini di sospensione di Google Chrome ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) in alto a destra per telefoni Android o in basso a destra per i dispositivi iOS, quindi toccare **impostazioni.**

   Piattaforma | Collegamento
   ---------- | --------
   Android | ![Posizione del menu impostazioni di Google Chrome](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Icona del menu delle impostazioni di Google Chrome](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. In **Impostazioni** aprire **password**.

   Piattaforma | Collegamento
   ---------- | --------
   Android | ![Percorso del comando per le password di Andoid Chrome](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Percorso del comando per le password di Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. Nei dispositivi Android toccare il ![ menu di Google Chrome con i puntini di sospensione ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) in alto a destra per telefoni Android o in basso a destra per i dispositivi iOS, quindi toccare **Esporta password**.

   Piattaforma | Collegamento
   ---------- | --------
   Android | ![Percorso delle password di esportazione Chrome Android](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Percorso delle password di esportazione di Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   È necessario specificare un PIN, un'impronta digitale o un riconoscimento facciale. Confermare l'identità e toccare di nuovo **Esporta password** per avviare l'esportazione.

1. Dopo l'esportazione delle password, Chrome chiede di scegliere l'app da importare. Selezionare **Authenticator** per avviare l'importazione delle password. Al termine, verranno fornite informazioni sullo stato dell'importazione.

   Piattaforma | Collegamento
   ---------- | --------
   Android | ![Percorso delle password di importazione Android Chrome](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Percorso delle password di importazione di Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Importare da Chrome Desktop browser

Prima di iniziare, è necessario installare e accedere all'estensione di [riempimento automatico Microsoft](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion) nel browser Chrome.

1. Aprire [Google password manager](https://passwords.google.com) in qualsiasi browser. Se non è già stato fatto, accedere al proprio account Google.

1. Selezionare l'icona dell'ingranaggio ![Icona dell'ingranaggio di gestione password desktop](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) per aprire la pagina Impostazioni password.

1. Selezionare **Export (Esporta**) e quindi nella pagina successiva selezionare **Export (Esporta** ) per avviare l'esportazione delle password. Fornire la password di Google quando viene richiesto di confermare l'identità. Al termine, verranno fornite informazioni sullo stato dell'importazione.

   ![Percorso del comando per l'esportazione delle password del browser Chrome Desktop](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. Aprire l'estensione riempimento automatico Chrome e selezionare **Impostazioni**.

   ![Percorso delle impostazioni dell'estensione di riempimento automatico del browser Chrome Desktop](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. Selezionare **Importa dati** per aprire una finestra di dialogo. Selezionare quindi **Scegli file** per individuare e importare il file CSV.

   ![Percorso CSV di importazione dati del browser Chrome Desktop](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Importa da Firefox

Firefox consente di esportare le password solo dal browser desktop, quindi assicurarsi di avere accesso a Firefox Desktop browser prima di importare le password da Firefox.

1. Accedere alla versione più recente di Firefox sul desktop e selezionare il ![Menu "hamburger" di Firefox](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) dal menu superiore destro dello schermo.

1. Selezionare **account di accesso e password**.

   ![Percorso password e account di accesso del browser Firefox Desktop](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. Dalla pagina Firefox Lockwise selezionare il menu dei ![ puntini ](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) di sospensione di Firefox, selezionare **Esporta account di accesso** e quindi confermare le finalità selezionando **Esporta**. Viene richiesto di identificarsi immettendo il PIN, la password del dispositivo o eseguendo la scansione delle impronte digitali. Una volta identificato correttamente, Firefox Esporta le password in formato CSV nel percorso selezionato.

   ![Percorso delle password di esportazione del browser Firefox per desktop](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. È possibile importare le password in Authenticator da un browser desktop o da telefoni iOS o Android. Per eseguire l'importazione nell'app Authenticator sul telefono:

      1. Trasferire il file CSV esportato nel telefono Android o iOS usando un metodo preferenziale e sicuro, quindi scaricarlo. Quindi, condividere il file CSV con l'app Authenticator per avviare l'importazione.

         Piattaforma | Collegamento
         ---------- | --------
         Android | ![Percorso delle password di importazione Android Chrome](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Percorso delle password di importazione di Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Dopo aver importato correttamente la password in Authenticator, eliminare il file CSV dal desktop o dal telefono cellulare.

## <a name="import-from-lastpass"></a>Importa da LastPass

LastPass supporta l'esportazione di password solo da un browser desktop, quindi è necessario assicurarsi di avere accesso a un browser desktop prima di iniziare a importare le password.

1. Accedere al [sito Web LastPass](https://lastpass.com) e selezionare **Opzioni avanzate** e quindi selezionare **Esporta**.

   ![Percorso delle password di esportazione LastPass desktop](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. Identificarsi quando richiesto fornendo la password master. Successivamente, verranno visualizzate le password esportate nella pagina Web.

1. Copiare il contenuto della pagina Web.

1. Aprire il blocco note o l'editor di testo preferito e incollare il contenuto copiato.

1. Salvare il file del blocco note selezionando **file** &gt; **Salva con nome**. Specificare un nome che termini con ". csv" (ad esempio LastPass.csv) in una posizione sicura sul desktop.

   ![LastPass desktop-Salva file CSV](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. È possibile importare le password nell'autenticatore in un browser desktop o in telefoni iOS o Android. Per eseguire l'importazione nell'app Authenticator sul telefono:

      1. Trasferire il file CSV esportato sullo smartphone usando un metodo preferito e sicuro, quindi scaricarlo. Quindi condividere il file CSV con l'app Authenticator per avviare l'importazione.

         Piattaforma | Collegamento
         ---------- | --------
         Android | ![Percorso delle password di importazione Android LastPass](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Percorso delle password di importazione di Apple LastPass](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Dopo aver importato correttamente la password in Authenticator, eliminare il file CSV dal desktop o dal telefono cellulare.

## <a name="import-from-bitwarden"></a>Importa da Bitwarden

Bitwarden supporta l'esportazione di password solo da un browser desktop, quindi è necessario assicurarsi di avere accesso a un browser desktop prima di iniziare a importare le password.

1. Accedere a https://vault.bitwarden.com/ e selezionare **strumenti** &gt; **Esporta** insieme di credenziali. Scegliere il formato di file CSV, specificare la password master e quindi selezionare **Esporta** insieme di credenziali per avviare l'esportazione.

   ![Percorso dell'insieme di credenziali di esportazione Bitwarden](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. È possibile importare le password nell'autenticatore in un browser desktop o in telefoni iOS o Android. Per eseguire l'importazione nell'app Authenticator sul telefono:

      1. Trasferire il file CSV esportato sullo smartphone usando un metodo preferito e sicuro, quindi scaricarlo. Quindi condividere il file CSV con l'app Authenticator per avviare l'importazione.

         Piattaforma | Collegamento
         ---------- | --------
         Android | ![Percorso delle password di importazione Android Bitwarden](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Percorso delle password di importazione di Apple Bitwarden](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Dopo aver importato correttamente la password in Authenticator, eliminare il file CSV dal desktop o dal telefono cellulare.

## <a name="import-from-roboform"></a>Importa da RoboForm

RoboForm consente l'esportazione di password solo dall'app desktop, quindi è necessario assicurarsi di avere accesso all'app RoboForm su un desktop prima di avviare l'importazione.

1. Avviare RoboForm dal client desktop e accedere al proprio account.

1. Scegliere **Opzioni** dal menu **RoboForm** .

   ![Menu delle opzioni del desktop RoboForm](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. Selezionare **account &** &gt; **esportazione** dati.

   ![Percorso del comando di esportazione RoboForm desktop](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. Scegliere un percorso sicuro per salvare il file esportato. Selezionare **account di accesso** come tipo di **dati** e selezionare il file CSV come formato e quindi selezionare **Esporta**.

   ![Finestra di dialogo di esportazione RoboForm desktop](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. Confermare le finalità e il file CSV viene quindi esportato nel percorso selezionato.

   ![Finestra di dialogo di conferma esportazione RoboForm desktop](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. È possibile importare le password nell'autenticatore in un browser desktop o in telefoni iOS o Android. Per eseguire l'importazione nell'app Authenticator sul telefono:

      1. Trasferire il file CSV esportato sullo smartphone usando un metodo preferito e sicuro, quindi scaricarlo. Quindi condividere il file CSV con l'app Authenticator per avviare l'importazione.

         Piattaforma | Collegamento
         ---------- | --------
         Android | ![Percorso delle password di importazione di Android RoboForm](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Posizione delle password di importazione di Apple RoboForm](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Dopo aver importato correttamente la password in Authenticator, eliminare il file CSV dal desktop o dal telefono cellulare.

## <a name="import-by-creating-a-csv"></a>Importazione mediante creazione di un volume condiviso cluster

Se i passaggi per l'importazione delle password da Gestione password non sono elencati in questo articolo, è possibile creare un file CSV che è possibile usare per importare le password nell'autenticatore. Microsoft consiglia di attenersi alla procedura seguente su un desktop per facilitarne la formattazione.

1. Sul desktop [scaricare e aprire il modello di importazione](https://go.microsoft.com/fwlink/?linkid=2134938). Se si è un utente Apple iPhone, Safari e keychain, è ora possibile passare al passaggio 4.

1. Esportare le password da Gestione password esistente in un file CSV non crittografato.

1. Copiare le colonne rilevanti dal file CSV esportato nel file CSV del modello e quindi salvare.

1. Se non si ha un file CSV esportato, è possibile copiare ogni account di accesso da Gestione password esistente nel file CSV del modello. Non rimuovere o modificare la riga di intestazione. Al termine, verificare l'integrità dei dati prima di iniziare il passaggio successivo.

1. È possibile importare le password nell'autenticatore in un browser desktop o in telefoni iOS o Android. Per eseguire l'importazione nell'app Authenticator sul telefono:

      1. Trasferire il file CSV esportato sullo smartphone usando un metodo preferito e sicuro, quindi scaricarlo. Quindi condividere il file CSV con l'app Authenticator per avviare l'importazione.

         Piattaforma | Collegamento
         ---------- | --------
         Android | ![Percorso delle password di importazione di Android CSV](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Posizione delle password di importazione CSV di Apple](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Dopo aver importato correttamente la password in Authenticator, eliminare il file CSV dal desktop o dal telefono cellulare.

## <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

La ragione più comune di importazioni non riuscite è la formattazione non corretta nel file CSV. Per risolvere il problema, è possibile provare a eseguire i passaggi seguenti.

- Consultare questo articolo per verificare se è già supportata l'importazione di password dal gestore delle password corrente. In tal caso, è possibile ripetere l'importazione attenendosi alla procedura indicata per il rispettivo provider.

- Se al momento non è supportata l'importazione del formato di gestione delle password, è possibile riprovare [creando manualmente il file CSV](#import-by-creating-a-csv).

- È possibile verificare l'integrità dei dati CSV con i seguenti suggerimenti:

  - La prima riga deve contenere un'intestazione con tre colonne: **URL**, **nome utente** e **password**.

  - Ogni riga deve contenere un valore nelle colonne **URL** e **password** .

- È possibile ricreare il volume condiviso cluster incollando il contenuto nel [file di modello CSV](https://go.microsoft.com/fwlink/?linkid=2134938).

- Se non funziona altro, segnala il problema usando il collegamento **Invia commenti e suggerimenti** dalle impostazioni dell'app Authenticator.
