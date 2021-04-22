---
title: Abilitare la reimpostazione della password self-service di Azure Active Directory
description: Questa esercitazione illustra come abilitare la reimpostazione della password self-service di Azure Active Directory per un gruppo di utenti e testare il processo di reimpostazione della password.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c18dd231a708030e3a454ab8708e3f0f11dbecf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861824"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Esercitazione: Consentire agli utenti di sbloccare l'account o reimpostare le password con la reimpostazione della password self-service di Azure Active Directory

La reimpostazione della password self-service di Azure Active Directory (Azure AD) consente agli utenti di cambiare o reimpostare la password, senza intervento dell'amministratore o dell'help desk. Se Azure AD blocca l'account di un utente o dimentica la password, può seguire le istruzioni per sbloccarsi e tornare al lavoro. Questa funzionalità riduce le chiamate all'help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione. È consigliabile questo video su come abilitare e configurare la [SSPR in Azure AD](https://www.youtube.com/watch?v=rA8TvhNcCvQ). È disponibile anche un video per gli amministratori IT sulla risoluzione dei sei messaggi di errore più comuni dell'utente finale [con SSPR.](https://www.youtube.com/watch?v=9RPrNVLzT8I)

> [!IMPORTANT]
> Questa esercitazione illustra in che modo un amministratore può abilitare la reimpostazione della password self-service. Se si è un utente finale già registrato per la reimpostazione della password self-service ed è necessario accedere nuovamente all'account, passare alla pagina Reimpostazione password di [Microsoft Online.](https://passwordreset.microsoftonline.com/)
>
> Se il team IT non ha abilitato la funzionalità per reimpostare la propria password, rivolgersi al supporto tecnico per assistenza aggiuntiva.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare la reimpostazione della password self-service per un gruppo di utenti di Azure AD
> * Configurare i metodi di autenticazione e le opzioni di registrazione
> * Testare il processo di reimpostazione della password self-service come utente

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari i privilegi e le risorse seguenti:

* Un tenant Azure AD lavoro con almeno una licenza Azure AD gratuita o di valutazione abilitata. Nel livello gratuito SSPR funziona solo per gli utenti del cloud in Azure AD. La modifica della password è supportata nel livello gratuito, ma la reimpostazione della password non lo è. 
    * Per le esercitazioni successive di questa serie, è necessaria una licenza Azure AD Premium P1 o di valutazione per il writeback delle password locale.
    * Se necessario, [creare un account Azure gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Un account con privilegi di *amministratore globale*.
* Un utente non amministratore con una password che si conosce, ad esempio *testuser*. In questa esercitazione si testerà l'esperienza di SSPR per l'utente finale usando questo account.
    * Se è necessario creare un utente, vedere [Avvio rapido: Aggiungere nuovi utenti ad Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* Gruppo di cui l'utente non amministratore è membro, ad esempio *SSPR-Test-Group.* In questa esercitazione si abiliterà la SSPR per questo gruppo.
    * Se è necessario creare un gruppo, vedere Creare un gruppo [di base e aggiungere membri usando Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Abilitare la reimpostazione self-service delle password

Azure AD consente di abilitare la reimpostazione della password self-service per *Nessuno*, utente *Selezionato* o *Tutti* gli utenti. Questa funzionalità granulare consente di scegliere un subset di utenti per testare il processo e il flusso di lavoro di reimpostazione della password self-service e registrazione. Quando si ha familiarità con il processo ed è il momento giusto per comunicare i requisiti con un set più ampio di utenti, è possibile selezionare un gruppo di utenti da abilitare per la SSPR. In alternativa, è possibile abilitare la reimpostazione della password self-service per tutti gli utenti nel tenant Azure AD.

> [!NOTE]
> Attualmente, è possibile abilitare un solo gruppo Azure AD per la SSPR usando il portale di Azure. Come parte di una distribuzione più ampia della SSPR, Azure AD i gruppi annidati. Verificare che agli utenti del gruppo scelto siano assegnate le licenze appropriate. Attualmente non è disponibile alcun processo di convalida per questi requisiti di licenza.

In questa esercitazione viene impostata la SSPR per un set di utenti in un gruppo di test. Usare *SSPR-Test-Group e* specificare il proprio gruppo Azure AD in base alle esigenze:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account con autorizzazioni di *amministratore globale*.
1. Cercare e **selezionare** Azure Active Directory e quindi selezionare **Reimpostazione password** dal menu a sinistra.
1. Nella pagina **Proprietà** selezionare Seleziona gruppo nell'opzione *Reimpostazione password self-service* **abilitata**
1. Cercare e selezionare il gruppo Azure AD, ad esempio *SSPR-Test-Group,* quindi scegliere *Seleziona*.

    [![Selezionare un gruppo nel portale di Azure da abilitare per la reimpostazione della password self-service](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Per abilitare la reimpostazione della password self-service per gli utenti selezionati, selezionare **Salva**.

## <a name="select-authentication-methods-and-registration-options"></a>Selezionare i metodi di autenticazione e le opzioni di registrazione

Quando gli utenti devono sbloccare l'account o reimpostare la password, viene richiesto un altro metodo di conferma. Questo fattore di autenticazione aggiuntivo garantisce che Azure AD solo gli eventi SSPR approvati. È possibile scegliere quali metodi di autenticazione consentire, in base alle informazioni di registrazione fornite dall'utente.

1. Dal menu a sinistra della pagina **Metodi di** autenticazione impostare Numero di metodi **necessari** per la reimpostazione su *1.*

    Per migliorare la sicurezza, è possibile aumentare il numero di metodi di autenticazione necessari per la reimpostazione della password self-service.

1. Scegliere i **Metodi disponibili per gli utenti** che l'organizzazione vuole consentire. Per questa esercitazione, selezionare le caselle per abilitare i metodi seguenti:

    * *Notifica dell'app per dispositivi mobili*
    * *Codice app per dispositivi mobili*
    * *Indirizzo di posta elettronica*
    * *Cellulare*

    È possibile abilitare altri metodi di autenticazione, ad *esempio il telefono di Office* o le domande di sicurezza, in base alle esigenze aziendali. 

1. Per applicare i metodi di autenticazione, selezionare **Salva**.

Prima di poter sbloccare l'account o reimpostare una password, gli utenti devono registrare le proprie informazioni di contatto. Azure AD le informazioni di contatto per i diversi metodi di autenticazione impostati nei passaggi precedenti.

Le informazioni di contatto possono essere specificate manualmente dall'amministratore o dagli utenti stessi tramite un portale di registrazione. In questa esercitazione, configurare Azure AD richiedere agli utenti la registrazione al successivo accesso.

1. Dal menu a sinistra della pagina **Registrazione** selezionare *Sì* per Richiedi agli utenti **di eseguire la registrazione all'accesso.**
1. Impostare **Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione** su *180*.

    È importante mantenere aggiornate le informazioni di contatto. Se all'avvio di un evento SSPR sono presenti informazioni di contatto obsolete, l'utente potrebbe non essere in grado di sbloccare l'account o reimpostare la password.

1. Per applicare le impostazioni di registrazione, selezionare **Salva**.

## <a name="set-up-notifications-and-customizations"></a>Configurare notifiche e personalizzazioni

Per mantenere gli utenti informati sull'attività dell'account, è possibile configurare Azure AD inviare notifiche di posta elettronica quando si verifica un evento della firma di accesso con firma di accesso client. Queste notifiche possono essere impostate sia per gli account utente normali che per gli account amministratore. Per gli account amministratore, questa notifica offre un altro livello di consapevolezza quando la password di un account amministratore con privilegi viene reimpostata usando la reimpostazione della reimpostazione della password della reimpostazione della password. Azure AD notifica a tutti gli amministratori globali quando un utente usa la password della password di accesso in un account amministratore.

1. Nel menu a sinistra della pagina **Notifiche** configurare le opzioni seguenti:

   * Impostare l'opzione **Notificare agli utenti le reimpostazioni delle password** su *Sì*.
   * Impostare **Notificare agli amministratori quando altri amministratori reimpostano le proprie password** su *Sì*.

1. Per applicare le preferenze di notifica, selezionare **Salva**.

Se gli utenti necessitano di assistenza per il processo di ripristino della password di ripristino di sistema, è possibile personalizzare il collegamento "Contattare l'amministratore". L'utente può selezionare questo collegamento nel processo di registrazione SSPR e quando sblocca il proprio account o reimposta la password. Per assicurarsi che gli utenti osercitino il supporto necessario, è consigliabile specificare un URL o un indirizzo di posta elettronica del supporto tecnico personalizzato.

1. Dal menu a sinistra della pagina Personalizzazione **impostare** Personalizza collegamento al supporto **help desk** su *Sì.*
1. Nel campo **Custom helpdesk email or URL (Indirizzo** di posta elettronica o URL del supporto help desk personalizzato) specificare un indirizzo di posta elettronica o un URL della pagina Web in cui gli utenti possono ottenere assistenza dall'organizzazione, ad esempio *https: \/ /support.contoso.com/*
1. Per applicare il collegamento personalizzato, selezionare **Salva**.

## <a name="test-self-service-password-reset"></a>Testare la reimpostazione della password self-service

Dopo aver abilitato e configurato la SSPR, testare il processo di SSPR con un utente che fa parte del gruppo selezionato nella sezione precedente, ad esempio *Test-SSPR-Group.* Nell'esempio seguente viene utilizzato *l'account testuser.* Specificare il proprio account utente. Fa parte del gruppo abilitato per la SSPR nella prima sezione di questa esercitazione.

> [!NOTE]
> Per eseguire il test della reimpostazione della password self-service, usare un account non amministratore. Per impostazione predefinita, Azure AD la reimpostazione della password self-service per gli amministratori. È necessario usare due metodi di autenticazione per reimpostare la password. Per altre informazioni, vedere [Differenze dei criteri di reimpostazione degli amministratori](concept-sspr-policy.md#administrator-reset-policy-differences).

1. Per visualizzare il processo di registrazione manuale, aprire una nuova finestra del browser in modalità InPrivate o in incognito e passare a *https: \/ /aka.ms/ssprsetup*. Azure AD gli utenti a questo portale di registrazione al successivo accesso.
1. Accedere con un utente test non amministratore, ad esempio *testuser,* e registrare le informazioni di contatto dei metodi di autenticazione.
1. Al termine, selezionare il pulsante Con l'aspetto **positivo** e chiudere la finestra del browser.
1. Aprire una nuova finestra del browser in modalità InPrivate o in incognito e passare a *https: \/ /aka.ms/sspr*.
1. Immettere le informazioni sull'account degli utenti di test non amministratore, ad esempio *testuser*, i caratteri di CAPTCHA e quindi selezionare **Avanti.**

    ![Immettere le informazioni sull'account utente per reimpostare la password](media/tutorial-enable-sspr/password-reset-page.png)

1. Seguire i passaggi di verifica per reimpostare la password. Al termine, si riceverà una notifica tramite posta elettronica che indica che la password è stata reimpostata.

## <a name="clean-up-resources"></a>Pulire le risorse

In un'esercitazione successiva di questa serie verrà configurato il writeback delle password. Questa funzionalità consente di eseguire il writeback delle modifiche delle password dalla reimpostazione della password self-service di Azure AD a un ambiente Active Directory locale. Se si vuole continuare con questa serie di esercitazioni per configurare il writeback delle password, non disabilitare la reimpostazione della password.

Se non si vuole più usare la funzionalità SSPR che è stata impostata come parte di questa esercitazione, impostare lo stato della richiesta SSPR su **Nessuno** seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare e **selezionare** Azure Active Directory e quindi selezionare **Reimpostazione password** dal menu a sinistra.
1. Nella pagina **Proprietà** selezionare Nessuna nell'opzione *Reimpostazione password self-service* **abilitata.**
1. Per applicare la modifica alla reimpostazione della password self-service, selezionare **Salva**.

## <a name="faqs"></a>Domande frequenti

Questa sezione illustra le domande comuni degli amministratori e degli utenti finali che provano la SSPR:

- Perché gli utenti federati attendono fino a 2 minuti dopo aver visualizzato La **password** è stata reimpostata prima di poter usare password sincronizzate da locale?

  Per gli utenti federati le cui password sono sincronizzate, l'origine dell'autorità per le password è locale. Di conseguenza, la password SSPR aggiorna solo le password locali. La sincronizzazione dell'hash delle password Azure AD è pianificata ogni 2 minuti.

- Quando un nuovo utente creato che è pre-popolato con dati SSPR, ad esempio telefono e posta elettronica, visita la pagina di registrazione della richiesta di firma della password sSPR, non perdere **l'accesso all'account.** viene visualizzato come titolo della pagina. Perché il messaggio non viene visualizzato da altri utenti con dati SSPR precompilato?

  Un utente che vede Non **perdere l'accesso all'account.** è un membro dei gruppi di registrazione SSPR/combinati configurati per il tenant. Gli utenti che non vedono Non **perdere l'accesso all'account.** non fanno parte dei gruppi di registrazione SSPR/combinati.

- Quando alcuni utenti passano attraverso il processo SSPR e reimpostano la password, perché non vedono l'indicatore di complessità della password?

  Gli utenti che non vedono la complessità della password debole/complessa hanno abilitato il writeback delle password sincronizzato. Poiché la reimpostazione della password non è in grado di determinare i criteri password dell'ambiente locale del cliente, non può convalidare la complessità o la debolezza della password. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata abilitata la reimpostazione della password self-service di Azure AD per un gruppo selezionato di utenti. Si è appreso come:

> [!div class="checklist"]
> * Abilitare la reimpostazione della password self-service per un gruppo di utenti di Azure AD
> * Configurare i metodi di autenticazione e le opzioni di registrazione
> * Testare il processo di reimpostazione della password self-service come utente

> [!div class="nextstepaction"]
> [Abilitare Azure AD Multi-Factor Authentication](./tutorial-enable-azure-mfa.md)
