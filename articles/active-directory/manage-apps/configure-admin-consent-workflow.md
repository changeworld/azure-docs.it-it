---
title: Configurare il flusso di lavoro di consenso dell'amministratore - Azure Active Directory | Microsoft Docs
description: Informazioni su come configurare un modo per consentire agli utenti finali di richiedere l'accesso alle applicazioni che richiedono il consenso dell'amministratore.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2019
ms.author: iangithinji
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9811c3d1833a02ad3cbaf22b9f0b31fd2da5bb6d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375186"
---
# <a name="configure-the-admin-consent-workflow"></a>Configurare il flusso di lavoro di consenso dell'amministratore

Questo articolo descrive come abilitare la funzionalità del flusso di lavoro di consenso dell'amministratore, che offre agli utenti finali un modo per richiedere l'accesso alle applicazioni che richiedono il consenso dell'amministratore.

Senza un flusso di lavoro di consenso dell'amministratore, un utente in un tenant in cui il consenso utente è disabilitato verrà bloccato quando tenta di accedere a qualsiasi app che richiede le autorizzazioni per accedere ai dati aziendali. L'utente visualizza un messaggio di errore generico che indica che non è autorizzato ad accedere all'app e deve chiedere assistenza all'amministratore. Spesso, tuttavia, l'utente non sa chi contattare, quindi deve rinunciare o creare un nuovo account locale nell'applicazione. Anche quando un amministratore viene informato, non esiste sempre un processo semplificato per consentire all'amministratore di concedere l'accesso e inviare una notifica agli utenti.
 
Il flusso di lavoro di consenso dell'amministratore offre agli amministratori un modo sicuro per concedere l'accesso alle applicazioni che richiedono l'approvazione dell'amministratore. Quando un utente tenta di accedere a un'applicazione ma non è in grado di fornire il consenso, può inviare una richiesta di approvazione dell'amministratore. La richiesta viene inviata tramite posta elettronica agli amministratori designati come revisori. Un revisore accetta un'azione sulla richiesta e l'utente viene informato dell'azione.

Per approvare le richieste, un revisore deve essere un amministratore globale, un amministratore di applicazioni cloud o un amministratore dell'applicazione. Al revisore deve essere già assegnato uno di questi ruoli di amministratore. semplicemente designarli come revisori non eleva i propri privilegi.

## <a name="enable-the-admin-consent-workflow"></a>Abilitare il flusso di lavoro di consenso dell'amministratore

Per abilitare il flusso di lavoro di consenso dell'amministratore e scegliere i revisori:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale.
2. Fare clic su **Tutti i servizi** nella parte superiore del menu di spostamento a sinistra. Aprire l'estensione **Azure Active Directory**.
3. Nella casella di ricerca del filtro digitare "**Azure Active Directory**" e **selezionare** Azure Active Directory elemento.
4. Scegliere **Applicazioni aziendali** dal menu di spostamento. 
5. In **Gestisci** selezionare **Impostazioni utente**.
6. In **Richieste di consenso dell'amministratore** impostare Gli utenti possono richiedere il consenso dell'amministratore alle app a cui non sono in grado di **acconsentire su** **Sì.**

   ![Configurare le impostazioni del flusso di lavoro per il consenso dell'amministratore](media/configure-admin-consent-workflow/admin-consent-requests-settings.png)
 
6. Configurare le seguenti impostazioni:

   * **Selezionare gli utenti per esaminare le richieste di consenso dell'amministratore.** Selezionare i revisori per questo flusso di lavoro da un set di utenti con i ruoli di amministratore globale, amministratore di applicazioni cloud e amministratore dell'applicazione.
   * **Gli utenti selezionati riceveranno notifiche tramite posta elettronica per le richieste**. Abilitare o disabilitare le notifiche tramite posta elettronica ai revisori quando viene effettuata una richiesta.  
   * **Gli utenti selezionati riceveranno promemoria di scadenza della richiesta.** Abilitare o disabilitare le notifiche di posta elettronica di promemoria per i revisori quando una richiesta sta per scadere.  
   * **La richiesta di consenso scade dopo (giorni).** Specificare per quanto tempo le richieste rimangono valide.

7. Selezionare **Salva**. L'a attivazione della funzionalità può richiedere fino a un'ora.

> [!NOTE]
> È possibile aggiungere o rimuovere revisori per questo flusso di lavoro modificando l'elenco Seleziona revisori richieste **di consenso amministratore.** Si noti che una limitazione corrente di questa funzionalità è che i revisori possono mantenere la possibilità di esaminare le richieste effettuate mentre sono stati designati come revisori.

## <a name="how-users-request-admin-consent"></a>Come gli utenti richiedono il consenso dell'amministratore

Dopo aver abilitato il flusso di lavoro di consenso dell'amministratore, gli utenti possono richiedere l'approvazione dell'amministratore per un'applicazione per cui non sono autorizzati a dare il consenso. La procedura seguente descrive l'esperienza dell'utente quando richiede l'approvazione. 

1. L'utente tenta di accedere all'applicazione.

2. Viene **visualizzato il messaggio Approvazione** richiesta. L'utente digitare una giustificazione per richiedere l'accesso all'app e quindi selezionare **Richiedi approvazione.**

   ![Screenshot che mostra una finestra di dialogo Approvazione richiesta in cui è possibile richiedere l'approvazione.](media/configure-admin-consent-workflow/end-user-justification.png)

3. Un **messaggio Di richiesta** inviata conferma che la richiesta è stata inviata all'amministratore. Se l'utente invia diverse richieste, all'amministratore viene inviata solo la prima richiesta.

   ![Screenshot che mostra la richiesta di conferma inviata.](media/configure-admin-consent-workflow/end-user-sent-request.png)

 4. L'utente riceve una notifica tramite posta elettronica quando la richiesta viene approvata, negata o bloccata. 

## <a name="review-and-take-action-on-admin-consent-requests"></a>Esaminare e intervenire sulle richieste di consenso dell'amministratore

Per esaminare le richieste di consenso dell'amministratore ed eseguire le azioni seguenti:

1. Accedere al portale di Azure [come](https://portal.azure.com) uno dei revisori registrati del flusso di lavoro di consenso dell'amministratore.
2. Selezionare **Tutti i** servizi nella parte superiore del menu di spostamento a sinistra. Aprire l'estensione **Azure Active Directory**.
3. Nella casella di ricerca del filtro digitare "**Azure Active Directory**" e selezionare **l'Azure Active Directory** elemento.
4. Scegliere **Applicazioni aziendali** dal menu di spostamento.
5. In **Attività** selezionare Richieste **di consenso dell'amministratore.**

   > [!NOTE]
   > I revisori visualizzano solo le richieste di amministratore create dopo la designazione come revisore.

1. Selezionare l'applicazione richiesta.
2. Esaminare i dettagli sulla richiesta:  

   * Per vedere chi richiede l'accesso e perché, selezionare la **scheda Richiesto da.**
   * Per visualizzare le autorizzazioni richieste dall'applicazione, selezionare **Rivedi autorizzazioni e consenso**.

8. Valutare la richiesta ed eseguire l'azione appropriata:

   * **Approvare la richiesta**. Per approvare una richiesta, concedere il consenso dell'amministratore all'applicazione. Dopo l'approvazione di una richiesta, a tutti i richiedenti viene notificato che gli è stato concesso l'accesso.  
   * **Negare la richiesta**. Per negare una richiesta, è necessario fornire una giustificazione che verrà fornita a tutti i richiedenti. Dopo che una richiesta è stata negata, a tutti i richiedenti viene notificato che è stato negato l'accesso all'applicazione. La negazione di una richiesta non impedisce agli utenti di richiedere nuovamente il consenso dell'amministratore all'app in futuro.  
   * **Bloccare la richiesta**. Per bloccare una richiesta, è necessario fornire una giustificazione che verrà fornita a tutti i richiedenti. Dopo che una richiesta è stata bloccata, tutti i richiedenti vengono informati che è stato negato l'accesso all'applicazione. Il blocco di una richiesta crea un oggetto entità servizio per l'applicazione nel tenant in uno stato disabilitato. Gli utenti non potranno richiedere il consenso dell'amministratore per l'applicazione in futuro.
 
## <a name="email-notifications"></a>Notifiche di posta elettronica
 
Se configurato, tutti i revisori riceveranno notifiche tramite posta elettronica quando:

* È stata creata una nuova richiesta
* Una richiesta è scaduta
* Una richiesta sta per scadere  
 
I richiedenti riceveranno notifiche tramite posta elettronica quando:

* Inviano una nuova richiesta di accesso
* La richiesta è scaduta
* La richiesta è stata negata o bloccata
* La richiesta è stata approvata
 
## <a name="audit-logs"></a>Log di controllo 
 
La tabella seguente illustra gli scenari e i valori di controllo disponibili per il flusso di lavoro di consenso dell'amministratore.

|Scenario  |Servizio di controllo  |Categoria di controllo  |Attività di controllo  |Audit Actor  |Limitazioni del log di controllo  |
|---------|---------|---------|---------|---------|---------|
|Amministratore che abilita il flusso di lavoro della richiesta di consenso        |Verifiche di accesso           |UserManagement           |Creare un modello di criteri di governance          |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente            |
|Disabilitazione del flusso di lavoro della richiesta di consenso da parte dell'amministratore       |Verifiche di accesso           |UserManagement           |Eliminare il modello di criteri di governance          |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente           |
|Aggiornamento delle configurazioni del flusso di lavoro del consenso da parte dell'amministratore        |Verifiche di accesso           |UserManagement           |Aggiornare il modello di criteri di governance          |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente           |
|Utente finale che crea una richiesta di consenso dell'amministratore per un'app       |Verifiche di accesso           |Criteri         |Creare una richiesta           |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente           |
|Revisori che approvano una richiesta di consenso dell'amministratore       |Verifiche di accesso           |UserManagement           |Approvare tutte le richieste nel flusso aziendale          |Contesto dell'app            |Attualmente non è possibile trovare il contesto utente o l'ID app a cui è stato concesso il consenso dell'amministratore.           |
|Revisori che negano una richiesta di consenso dell'amministratore       |Verifiche di accesso           |UserManagement           |Approvare tutte le richieste nel flusso aziendale          |Contesto dell'app            | Attualmente non è possibile trovare il contesto utente dell'attore che ha negato una richiesta di consenso dell'amministratore          |

## <a name="faq"></a>Domande frequenti 

**Questo flusso di lavoro è stato attivato, ma quando si testa la funzionalità, perché non è possibile visualizzare la nuova richiesta di approvazione necessaria che consente di richiedere l'accesso?**

Dopo l'attivazione della funzionalità, potrebbero essere fino a 60 minuti prima che gli utenti finali possano visualizzare l'aggiornamento. È possibile verificare che la configurazione sia stata eseguita correttamente visualizzando il **valore EnableAdminConsentRequests** nell'API. `https://graph.microsoft.com/beta/settings`

**Perché un revisore non può visualizzare tutte le richieste in sospeso?**

I revisori possono visualizzare solo le richieste di amministratore create dopo essere stati designati come revisori. Pertanto, se è stato aggiunto di recente come revisore, non verrà visualizzata alcuna richiesta creata prima dell'assegnazione.

**Perché un revisore visualizza più richieste per la stessa applicazione?**
  
Se uno sviluppatore di applicazioni ha configurato l'app per l'uso del consenso statico e dinamico per richiedere l'accesso ai dati dell'utente finale, verranno visualizzati due richieste di consenso dell'amministratore. Una richiesta rappresenta le autorizzazioni statiche e l'altra rappresenta le autorizzazioni dinamiche.

**I richiedenti possono controllare lo stato della richiesta?**  

No, per il momento i richiedenti sono in grado di ottenere gli aggiornamenti solo tramite notifiche tramite posta elettronica.

**Un revisore può approvare l'applicazione, ma non per tutti?**
 
Se si è interessati a concedere il consenso dell'amministratore e consentire a tutti gli utenti del tenant di usare l'applicazione, è consigliabile rifiutare la richiesta. Concedere quindi manualmente il consenso dell'amministratore limitando l'accesso all'applicazione richiedendo l'assegnazione utente e assegnando utenti o gruppi all'applicazione. Per altre informazioni, vedere [Metodi per l'assegnazione di utenti e gruppi](./assign-user-or-group-access-portal.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul consenso alle applicazioni, vedere [framework di consenso di Azure Active Directory](../develop/consent-framework.md).

[Configurare la modalità con cui gli utenti finali consentono le applicazioni](configure-user-consent.md)

[Concedere a un'applicazione il consenso amministratore a livello di tenant](grant-admin-consent.md)

[Autorizzazioni e consenso in Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)

[Azure AD su Microsoft Q&A](/answers/topics/azure-active-directory.html)
