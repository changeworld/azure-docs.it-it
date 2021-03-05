---
title: Creare una verifica di accesso dei gruppi & applicazioni-Azure AD
description: Informazioni su come creare una verifica di accesso dei membri del gruppo o dell'accesso alle applicazioni in Azure Active Directory verifiche di accesso.
services: active-directory
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 3/3/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7143c3f9786d41c32ae954ab219197a9cfaa1050
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176876"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Creare una verifica di accesso dei gruppi e delle applicazioni nelle verifiche di accesso Azure AD

L'accesso a gruppi e applicazioni per dipendenti e utenti guest cambia nel corso del tempo. Per ridurre i rischi associati alle assegnazioni di accesso obsolete, gli amministratori possono usare Azure Active Directory (Azure AD) per creare verifiche di accesso per i membri dei gruppi o l'accesso alle applicazioni. Se è necessario esaminare periodicamente l'accesso, è anche possibile creare verifiche di accesso ricorrenti. Per altre informazioni su questi scenari, vedere [Gestire l'accesso utente](manage-user-access-with-access-reviews.md) e [Gestire l'accesso guest](manage-guest-access-with-access-reviews.md).

È possibile guardare un breve video su come abilitare le verifiche di accesso:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Questo articolo descrive come creare una o più verifiche di accesso per i membri del gruppo o l'accesso alle applicazioni.

## <a name="prerequisites"></a>Prerequisiti

- Azure AD Premium P2
- Amministratore globale o Amministratore utenti

Per altre informazioni, vedere [Requisiti relativi alle licenze](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Creare una o più verifiche di accesso

1. Accedere alla portale di Azure e aprire la [pagina governance delle identità](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

2. Nel menu a sinistra fare clic su verifiche di **accesso**.

3. Fare clic su **Nuova verifica di accesso** per creare una nuova verifica di accesso.

    ![Riquadro verifiche di accesso nella governance delle identità](./media/create-access-review/access-reviews.png)

4. In **passaggio 1: selezionare gli elementi da rivedere** selezionare la risorsa che si desidera esaminare.

    ![Creare una verifica di accesso - Nome della verifica e descrizione](./media/create-access-review/select-what-review.png)

5. Se sono stati selezionati **Team + gruppi** nel passaggio 1, sono disponibili due opzioni nel passaggio 2
   - **Tutti i gruppi di Microsoft 365 con utenti guest.** Selezionare questa opzione se si desidera creare revisioni ricorrenti su tutti gli utenti guest in tutti i team Microsoft e i gruppi di M365 nell'organizzazione. È possibile scegliere di escludere determinati gruppi facendo clic su "Seleziona gruppi da escludere".
   - **Selezionare teams + groups.** Selezionare questa opzione se si desidera specificare un set di team e/o gruppi finiti da rivedere. Dopo aver fatto clic su questa opzione, verrà visualizzato un elenco di gruppi a destra da cui scegliere.

     ![Team e gruppi](./media/create-access-review/teams-groups.png)

     ![Team e gruppi scelti nell'interfaccia utente](./media/create-access-review/teams-groups-detailed.png)

6. Se è stata selezionata l'opzione **applicazioni** nel passaggio 1, è possibile selezionare una o più applicazioni nel passaggio 2.

    >[!NOTE]
    > La selezione di più gruppi e/o applicazioni comporterà la creazione di più verifiche di accesso. Se, ad esempio, si selezionano 5 gruppi da rivedere, si otterranno 5 verifiche di accesso separate

   ![Interfaccia visualizzata se si scelgono le applicazioni anziché i gruppi](./media/create-access-review/select-application-detailed.png)

7. Successivamente, nel passaggio 3 è possibile selezionare un ambito per la revisione. Le opzioni disponibili sono
   - **Solo utenti guest.** Se si seleziona questa opzione, la verifica di accesso viene limitata solo ai Azure AD utenti Guest B2B nella propria directory.
   - **Tutti.** La selezione di questa opzione consente di definire l'ambito della verifica di accesso a tutti gli oggetti utente associati alla risorsa.

    >[!NOTE]
    > Se nel passaggio 2 sono stati selezionati tutti i gruppi di Microsoft 365 con utenti guest, l'unica opzione consiste nell'esaminare gli utenti guest nel passaggio 3

8. Fare clic su Avanti: revisioni
9. Nella sezione **Seleziona revisori** selezionare una o più persone per eseguire le verifiche di accesso. È possibile scegliere tra:
    - **Proprietario del gruppo** (sono disponibili solo quando si esegue una verifica in un team o gruppo)
    - **Utenti o gruppi selezionati**
    - **Utenti che verificano l'accesso**
    - **Manager degli utenti.**
    Se si sceglie un **Manager di utenti** o **proprietari di gruppo**  , è possibile specificare anche un revisore di fallback. Ai revisori di fallback viene chiesto di eseguire una verifica quando l'utente non ha specificato alcuna gestione nella directory o il gruppo non dispone di un proprietario.

    ![nuova verifica di accesso](./media/create-access-review/new-access-review.png)

10. Nella sezione **specifica della ricorrenza della revisione** è possibile specificare una frequenza, ad esempio ogni settimana, ogni **mese, trimestrale,** semestrale, annuale. Viene quindi specificata una **durata**, che definisce per quanto tempo verrà aperta una revisione per l'input dai revisori. La durata massima che è possibile impostare per una verifica mensile, ad esempio, è di 27 giorni, per evitare la sovrapposizione delle verifiche. Potrebbe essere necessario ridurre la durata per assicurarsi che l'input dei revisori venga applicato in precedenza. Successivamente, è possibile selezionare una **Data di inizio** e una **Data di fine**.

    ![Scegliere la frequenza con cui deve essere eseguita la verifica](./media/create-access-review/frequency.png)

11. Fare clic sul pulsante **Next: Settings (impostazioni** ) nella parte inferiore della pagina
12. Nelle **impostazioni al completamento** è possibile specificare cosa accade dopo il completamento della verifica

    ![Creare una verifica di accesso-dopo le impostazioni di completamento](./media/create-access-review/upon-completion-settings-new.png)

Se si vuole rimuovere automaticamente l'accesso per gli utenti non autorizzati, impostare auto Apply results to resource to Enable. Per applicare manualmente i risultati al termine della verifica, impostare l'opzione su Disabilita.
Usare l'elenco se i revisori non rispondono per specificare cosa accade per gli utenti che non vengono esaminati dal revisore entro il periodo di revisione. Questa impostazione non ha alcun impatto sugli utenti che sono stati sottoposti a verifica manualmente dai revisori. Se la decisione finale del revisore è il rifiuto, l'accesso dell'utente verrà rimosso.

- **Nessuna modifica**: non viene apportata alcuna modifica all'accesso dell'utente
- **Rimuovi accesso**: l'accesso dell'utente viene rimosso
- **Approva accesso**: l'accesso dell'utente viene approvato
- **Accetta i consigli**: vengono applicati i consigli del sistema per rifiutare o approvare l'accesso continuo dell'utente

    ![Opzioni delle impostazioni di completamento](./media/create-access-review/upon-completion-settings-new.png)

Usare l'azione per applicare agli utenti **Guest** negati per specificare cosa accade agli utenti Guest se vengono negati.
- Per rimuovere l'appartenenza dell'utente dalla risorsa, l'accesso dell'utente negato al gruppo o all'applicazione da rivedere sarà comunque in grado di accedere al tenant.
- Bloccare l'accesso dell'utente per 30 giorni, quindi rimuovere l'utente dal tenant bloccherà l'accesso degli utenti negati al tenant, indipendentemente dal fatto che abbiano accesso ad altre risorse. Se si è verificato un errore o se un amministratore decide di riabilitare l'accesso di un utente, può eseguire questa operazione entro 30 giorni dopo che l'utente è stato disabilitato. Se non viene eseguita alcuna azione sugli utenti disabilitati, questi verranno eliminati dal tenant.

Per ulteriori informazioni sulle procedure consigliate per la rimozione degli utenti guest che non hanno più accesso alle risorse dell'organizzazione, leggere l'articolo intitolato [usare Azure ad Identity governance per rivedere e rimuovere gli utenti esterni che non hanno più accesso alle risorse.](access-reviews-external-users.md)

   >[!NOTE]
   >L'azione da applicare agli utenti guest non autorizzati non è configurabile nelle revisioni che hanno come ambito più di utenti guest. Non è inoltre configurabile per le verifiche di **tutti i gruppi di M365 con utenti guest.** Quando non è configurabile, l'opzione predefinita di rimozione dell'appartenenza dell'utente dalla risorsa viene utilizzata per utenti non autorizzati.

13. In **Enable Review Decision Helper** scegliere se si desidera che il revisore riceva le raccomandazioni durante il processo di revisione.

    ![Abilita opzioni di supporto decisionale](./media/create-access-review/helpers.png)

14. Nella sezione **Impostazioni avanzate** è possibile scegliere quanto segue
    - Impostare la **giustificazione necessaria** per **consentire** a di richiedere al revisore di fornire un motivo per l'approvazione.
    - Impostare le **notifiche di posta elettronica** per **consentire** a di Azure ad inviare notifiche tramite posta elettronica ai revisori all'avvio di una verifica di accesso e agli amministratori al completamento di una verifica.
    - Impostare **Promemoria** su **Abilita** per fare in modo che Azure AD invii promemoria delle verifiche di accesso in corso ai revisori che non hanno completato la verifica. Questi promemoria saranno autonomi a metà della durata della verifica.
    - Il contenuto del messaggio di posta elettronica inviato ai revisori viene generato automaticamente in base ai dettagli della revisione, ad esempio il nome della revisione, il nome della risorsa, la scadenza e così via. Se è necessario un modo per comunicare informazioni aggiuntive, ad esempio istruzioni aggiuntive o informazioni di contatto, è possibile specificare questi dettagli nella sezione **contenuto aggiuntivo per l'indirizzo di posta elettronica del revisore** . Le informazioni immesse sono incluse nei messaggi di posta elettronica di invito e promemoria inviati ai revisori assegnati. La sezione evidenziata nell'immagine seguente mostra la posizione in cui vengono visualizzate le informazioni.


      ![contenuto aggiuntivo per il revisore](./media/create-access-review/additional-content-reviewer.png)

15. Fare clic su Avanti **: esaminare + crea** per passare alla pagina successiva
16. Assegnare un nome alla verifica di accesso. Facoltativamente è possibile assegnare una descrizione alla verifica. Il nome e la descrizione vengono visualizzati dai revisori.
17. Esaminare le informazioni e selezionare **Crea**

       ![schermata Crea Revisione](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>Avviare la verifica di accesso

Una volta specificate le impostazioni per una verifica di accesso, fare clic su **Avvia**. La verifica di accesso verrà visualizzata nell'elenco con un indicatore dello stato.

![Elenco delle verifiche di accesso e del relativo stato](./media/create-access-review/access-reviews-list.png)

Per impostazione predefinita, Azure AD invia un messaggio di posta elettronica ai revisori poco tempo dopo l'inizio della verifica. Se si imposta Azure AD in modo che non invii un messaggio di posta elettronica, assicurarsi di informare i revisori che vi è una verifica di accesso in attesa di completamento. È possibile visualizzare le istruzioni su come [verificare l'accesso ai gruppi o alle applicazioni](perform-access-review.md). Se la verifica è destinata ai guest per verificare il proprio accesso, visualizzare le istruzioni su come [verificare l'accesso da soli a gruppi o applicazioni](review-your-access.md).

Se sono stati assegnati Guest come revisori che non hanno accettato l'invito, non riceveranno un messaggio di posta elettronica dalle verifiche di accesso perché devono prima accettare l'invito prima di esaminarlo.

## <a name="access-review-status-table"></a>Tabella dello stato della verifica di accesso

| Stato | Definizione |
|--------|------------|
|NotStarted | La revisione è stata creata, l'individuazione utente è in attesa di essere avviata. |
|Inizializzazione in corso   | È in corso l'individuazione utente per identificare tutti gli utenti che fanno parte della revisione. |
|Avvio in corso | È in corso l'avvio della verifica. Se sono abilitate le notifiche tramite posta elettronica, i messaggi di posta elettronica vengono inviati ai revisori. |
|InProgress | Verifica avviata. Se sono state inviate notifiche tramite posta elettronica ai revisori. I revisori possono inviare decisioni fino alla data di scadenza. |
|Completamento | La verifica è stata completata e i messaggi di posta elettronica vengono inviati al proprietario della verifica. |
|Revisione automatica | La revisione si trova in una fase di verifica del sistema. Il sistema sta registrando le decisioni per gli utenti che non sono stati esaminati in base a raccomandazioni o decisioni preconfigurate. |
|Revisione automatica | Le decisioni sono state registrate dal sistema per tutti gli utenti che non sono stati rivisti. Verifica è pronto per procedere con l' **applicazione** se l'applicazione automatica è abilitata. |
|Applicazione | Non verrà apportata alcuna modifica all'accesso per gli utenti che sono stati approvati. |
|Applicato | Gli utenti negati, se presenti, sono stati rimossi dalla risorsa o dalla directory. |
|Non riuscito | Non è stato possibile procedere con la revisione. Questo errore può essere correlato all'eliminazione del tenant, a una modifica nelle licenze o ad altre modifiche interne del tenant. |

## <a name="create-reviews-via-apis"></a>Creare verifiche tramite API

È anche possibile creare verifiche di accesso usando le API. Le operazioni eseguite per gestire le verifiche di accesso di gruppi e utenti di applicazioni nel portale di Azure possono essere svolte anche tramite le API Microsoft Graph. Per altre informazioni, vedere le informazioni di [riferimento sull'API Azure ad Access revisioni](/graph/api/resources/accessreviews-root?view=graph-rest-beta). Per un esempio di codice, vedere [esempio di recupero delle verifiche di accesso Azure ad tramite Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Passaggi successivi

- [Verificare l'accesso a gruppi o applicazioni](perform-access-review.md)
- [Verificare l'accesso per se stessi a gruppi o applicazioni](review-your-access.md)
- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)