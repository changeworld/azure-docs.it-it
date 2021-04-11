---
title: Abilitare la reimpostazione della password self-service di Azure Active Directory
description: Questa esercitazione illustra come abilitare la reimpostazione della password self-service di Azure Active Directory per un gruppo di utenti e testare il processo di reimpostazione della password.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 03/25/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39eec4fb6e9907b36908a87c09aceabd0dd1a678
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075167"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Esercitazione: Consentire agli utenti di sbloccare l'account o reimpostare le password con la reimpostazione della password self-service di Azure Active Directory

La reimpostazione della password self-service di Azure Active Directory (Azure AD) consente agli utenti di cambiare o reimpostare la password, senza intervento dell'amministratore o dell'help desk. Se Azure AD blocca l'account di un utente o dimentica la password, può seguire le istruzioni per sbloccarsi e tornare al lavoro. Questa funzionalità riduce le chiamate all'help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione. Si consiglia di usare questo video per [abilitare e configurare SSPR in Azure ad](https://www.youtube.com/watch?v=rA8TvhNcCvQ). È anche presente un video per gli amministratori IT sulla [risoluzione dei sei messaggi di errore dell'utente finale più comuni con SSPR](https://www.youtube.com/watch?v=9RPrNVLzT8I).

> [!IMPORTANT]
> Questa esercitazione illustra in che modo un amministratore può abilitare la reimpostazione della password self-service. Se si è un utente finale già registrato per la reimpostazione della password self-service ed è necessario tornare al proprio account, passare alla pagina di [reimpostazione della password di Microsoft Online](https://passwordreset.microsoftonline.com/) .
>
> Se il team IT non ha abilitato la funzionalità per reimpostare la propria password, rivolgersi al supporto tecnico per assistenza aggiuntiva.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Abilitare la reimpostazione della password self-service per un gruppo di utenti di Azure AD
> * Configurare i metodi di autenticazione e le opzioni di registrazione
> * Testare il processo di reimpostazione della password self-service come utente

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari i privilegi e le risorse seguenti:

* Un tenant Azure AD funzionante con almeno una licenza Azure AD gratuita o di valutazione abilitata. Nel livello gratuito SSPR funziona solo per gli utenti del cloud in Azure AD.
    * Per le esercitazioni successive in questa serie, è necessario un Azure AD Premium P1 o una licenza di valutazione per il writeback delle password locale.
    * Se necessario, [creare un account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un account con privilegi di *amministratore globale*.
* Un utente non amministratore con una password conosciuta, ad esempio *testuser*. In questa esercitazione si verificherà l'esperienza SSPR dell'utente finale usando questo account.
    * Se è necessario creare un utente, vedere [Avvio rapido: Aggiungere nuovi utenti ad Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).
* Gruppo di cui l'utente non amministratore è membro, piace *SSPR-test-Group*. In questa esercitazione verrà abilitato SSPR per questo gruppo.
    * Se è necessario creare un gruppo, vedere [creare un gruppo di base e aggiungere membri con Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Abilitare la reimpostazione self-service delle password

Azure AD consente di abilitare la reimpostazione della password self-service per *Nessuno*, utente *Selezionato* o *Tutti* gli utenti. Questa funzionalità granulare consente di scegliere un subset di utenti per testare il processo e il flusso di lavoro di reimpostazione della password self-service e registrazione. Quando si ha familiarità con il processo e il tempo è giusto per comunicare i requisiti con un set più ampio di utenti, è possibile selezionare un gruppo di utenti da abilitare per SSPR. In alternativa, è possibile abilitare la reimpostazione della password self-service per tutti gli utenti nel tenant Azure AD.

> [!NOTE]
> Attualmente, è possibile abilitare un solo gruppo Azure AD per SSPR usando il portale di Azure. Come parte di una distribuzione più ampia di SSPR, Azure AD supporta i gruppi annidati. Verificare che agli utenti del gruppo scelto siano assegnate le licenze appropriate. Attualmente non è disponibile alcun processo di convalida per questi requisiti di licenza.

In questa esercitazione, configurare SSPR per un set di utenti in un gruppo di test. Usare *SSPR-test-Group* e fornire un gruppo di Azure ad personalizzato in base alle esigenze:

1. Accedere al [portale di Azure](https://portal.azure.com) usando un account con autorizzazioni di *amministratore globale*.
1. Cercare e selezionare **Azure Active Directory**, quindi selezionare **reimpostazione della password** dal menu a sinistra.
1. Nella pagina **Proprietà** , sotto l'opzione *reimpostazione password self-service abilitata*, selezionare **Seleziona gruppo**
1. Cercare e selezionare il gruppo di Azure AD, ad esempio *SSPR-test-Group*, quindi scegliere *Seleziona*.

    [![Selezionare un gruppo nel portale di Azure da abilitare per la reimpostazione della password self-service](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Per abilitare la reimpostazione della password self-service per gli utenti selezionati, selezionare **Salva**.

## <a name="select-authentication-methods-and-registration-options"></a>Selezionare i metodi di autenticazione e le opzioni di registrazione

Quando gli utenti devono sbloccare l'account o reimpostare la password, viene richiesto un altro metodo di conferma. Questo fattore di autenticazione aggiuntivo garantisce che Azure AD terminato solo gli eventi SSPR approvati. È possibile scegliere quali metodi di autenticazione consentire, in base alle informazioni di registrazione fornite dall'utente.

1. Dal menu sul lato sinistro della pagina metodi di **autenticazione** , impostare il **numero di metodi necessari per la reimpostazione** a *1*.

    Per migliorare la sicurezza, è possibile aumentare il numero di metodi di autenticazione necessari per la reimpostazione della password self-service.

1. Scegliere i **Metodi disponibili per gli utenti** che l'organizzazione vuole consentire. Per questa esercitazione, selezionare le caselle per abilitare i metodi seguenti:

    * *Notifica dell'app per dispositivi mobili*
    * *Codice app per dispositivi mobili*
    * *Indirizzo di posta elettronica*
    * *Cellulare*

    Per soddisfare i requisiti aziendali, è possibile abilitare altri metodi di autenticazione, ad esempio il *telefono dell'ufficio* o le *domande di sicurezza*.

1. Per applicare i metodi di autenticazione, selezionare **Salva**.

Prima di poter sbloccare l'account o reimpostare una password, gli utenti devono registrare le proprie informazioni di contatto. Azure AD usa queste informazioni di contatto per i diversi metodi di autenticazione configurati nei passaggi precedenti.

Le informazioni di contatto possono essere specificate manualmente dall'amministratore o dagli utenti stessi tramite un portale di registrazione. In questa esercitazione, configurare Azure AD per richiedere agli utenti la registrazione al successivo accesso.

1. Dal menu sul lato sinistro della pagina di **registrazione** , selezionare *Sì* per **Richiedi agli utenti di registrarsi all'accesso**.
1. Impostare **Numero di giorni prima che agli utenti venga chiesto di riconfermare le informazioni di autenticazione** su *180*.

    È importante lasciare aggiornate le informazioni di contatto. Se le informazioni di contatto obsolete sono presenti all'avvio di un evento SSPR, l'utente potrebbe non essere in grado di sbloccare l'account o di reimpostare la password.

1. Per applicare le impostazioni di registrazione, selezionare **Salva**.

## <a name="set-up-notifications-and-customizations"></a>Configurare notifiche e personalizzazioni

Per informare gli utenti sull'attività dell'account, è possibile configurare Azure AD per inviare notifiche tramite posta elettronica quando si verifica un evento SSPR. Queste notifiche possono essere impostate sia per gli account utente normali che per gli account amministratore. Per gli account amministrativi, questa notifica fornisce un altro livello di consapevolezza quando la password di un account amministratore con privilegi viene reimpostata usando SSPR. Azure AD invierà una notifica a tutti gli amministratori globali quando un utente usa SSPR per un account amministratore.

1. Dal menu sul lato sinistro della pagina **notifiche** impostare le opzioni seguenti:

   * Impostare l'opzione **Notificare agli utenti le reimpostazioni delle password** su *Sì*.
   * Impostare **Notificare agli amministratori quando altri amministratori reimpostano le proprie password** su *Sì*.

1. Per applicare le preferenze di notifica, selezionare **Salva**.

Se gli utenti hanno bisogno di ulteriore assistenza per il processo di SSPR, è possibile personalizzare il collegamento "contattare l'amministratore". L'utente può selezionare questo collegamento nel processo di registrazione SSPR e quando sblocca l'account o Reimposta la password. Per assicurarsi che gli utenti ottengano il supporto necessario, è consigliabile fornire un URL o un indirizzo di posta elettronica del supporto tecnico personalizzato.

1. Dal menu sul lato sinistro della pagina **personalizzazione** impostare **Personalizza il collegamento helpdesk** su *Sì*.
1. Nel campo indirizzo di **posta elettronica o URL del supporto tecnico personalizzato** specificare un indirizzo di posta elettronica o un URL della pagina Web in cui gli utenti possono ottenere ulteriori informazioni dalla propria organizzazione, ad esempio *https: \/ /support.contoso.com/*
1. Per applicare il collegamento personalizzato, selezionare **Salva**.

## <a name="test-self-service-password-reset"></a>Testare la reimpostazione della password self-service

Con SSPR abilitato e configurato, testare il processo SSPR con un utente che fa parte del gruppo selezionato nella sezione precedente, ad esempio *test-SSPR-Group*. Nell'esempio seguente viene usato l'account *testuser* . Fornire il proprio account utente. Fa parte del gruppo abilitato per SSPR nella prima sezione di questa esercitazione.

> [!NOTE]
> Per eseguire il test della reimpostazione della password self-service, usare un account non amministratore. Per impostazione predefinita, Azure AD Abilita la reimpostazione della password self-service per gli amministratori. È necessario usare due metodi di autenticazione per reimpostare la password. Per altre informazioni, vedere [Differenze dei criteri di reimpostazione degli amministratori](concept-sspr-policy.md#administrator-reset-policy-differences).

1. Per visualizzare il processo di registrazione manuale, aprire una nuova finestra del browser in modalità InPrivate o in incognito e passare a *https: \/ /aka.ms/ssprsetup*. Azure AD indirizzare gli utenti al portale di registrazione al momento dell'accesso successivo.
1. Accedere con un utente di test non amministratore, ad esempio *testuser*, e registrare le informazioni di contatto dei metodi di autenticazione.
1. Al termine, selezionare il pulsante contrassegnato **sembra corretto** e chiudere la finestra del browser.
1. Aprire una nuova finestra del browser in modalità InPrivate o in incognito e passare a *https: \/ /aka.ms/SSPR*.
1. Immettere le informazioni sull'account degli utenti di test non amministratore, ad esempio *testuser*, i caratteri del CAPTCHA e quindi fare clic su **Next (avanti**).

    ![Immettere le informazioni sull'account utente per reimpostare la password](media/tutorial-enable-sspr/password-reset-page.png)

1. Seguire i passaggi di verifica per reimpostare la password. Al termine, si riceverà una notifica di posta elettronica che segnala che la password è stata reimpostata.

## <a name="clean-up-resources"></a>Pulire le risorse

In un'esercitazione successiva di questa serie verrà configurato il writeback delle password. Questa funzionalità consente di eseguire il writeback delle modifiche delle password dalla reimpostazione della password self-service di Azure AD a un ambiente Active Directory locale. Se si desidera continuare con questa serie di esercitazioni per configurare il writeback delle password, non disabilitare SSPR Now.

Se non si vuole più usare la funzionalità SSPR configurata come parte di questa esercitazione, impostare lo stato di SSPR su **None** usando la procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare e selezionare **Azure Active Directory**, quindi selezionare **reimpostazione della password** dal menu a sinistra.
1. Nella pagina **Proprietà** , sotto l'opzione *reimpostazione password self-service abilitata*, selezionare **nessuno**.
1. Per applicare la modifica alla reimpostazione della password self-service, selezionare **Salva**.

## <a name="faqs"></a>Domande frequenti

In questa sezione vengono illustrate le domande più comuni degli amministratori e degli utenti finali che tentano di SSPR:

- Perché gli utenti federati attendono fino a 2 minuti dopo che **la password è stata reimpostata** prima di poter usare le password sincronizzate dall'ambiente locale?

  Per gli utenti federati le cui password sono sincronizzate, l'origine dell'autorità per le password è locale. Di conseguenza, SSPR aggiorna solo le password locali. La sincronizzazione dell'hash delle password con Azure AD è pianificata ogni 2 minuti.

- Quando un utente appena creato che viene prepopolato con dati SSPR, ad esempio telefono e posta elettronica, visita la pagina di registrazione SSPR, **non perdere l'accesso al tuo account.** viene visualizzato come titolo della pagina. Perché gli altri utenti che dispongono di dati di SSPR sono già popolati, vedere il messaggio?

  Un utente che **non è in grado di perdere l'accesso all'account.** è un membro di SSPR/gruppi di registrazione combinati configurati per il tenant. Gli utenti che non visualizzano **l'accesso all'account non perdono.** non fanno parte dei gruppi di registrazione SSPR/combinati.

- Quando alcuni utenti passano attraverso il processo SSPR e reimpostano la password, perché non visualizzano l'indicatore di attendibilità della password?

  Per gli utenti che non riescono a visualizzare il livello di attendibilità delle password è abilitato il writeback delle password. Poiché SSPR non è in grado di determinare i criteri password dell'ambiente locale del cliente, non può convalidare il livello di attendibilità o la debolezza della password. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata abilitata la reimpostazione della password self-service di Azure AD per un gruppo selezionato di utenti. Si è appreso come:

> [!div class="checklist"]
> * Abilitare la reimpostazione della password self-service per un gruppo di utenti di Azure AD
> * Configurare i metodi di autenticazione e le opzioni di registrazione
> * Testare il processo di reimpostazione della password self-service come utente

> [!div class="nextstepaction"]
> [Abilitare Azure AD Multi-Factor Authentication](./tutorial-enable-azure-mfa.md)
