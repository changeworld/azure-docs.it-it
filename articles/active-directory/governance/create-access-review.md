---
title: Creare una verifica di accesso dei gruppi & applicazioni - Azure AD
description: Informazioni su come creare una verifica di accesso dei membri del gruppo o dell'accesso alle applicazioni in Azure Active Directory di accesso.
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
ms.openlocfilehash: f4d6502ffdd13272d396852b11a11d13f929b11b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532265"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Creare una verifica di accesso di gruppi e applicazioni in Azure AD di accesso

L'accesso a gruppi e applicazioni per dipendenti e utenti guest cambia nel corso del tempo. Per ridurre i rischi associati alle assegnazioni di accesso obsolete, gli amministratori possono usare Azure Active Directory (Azure AD) per creare verifiche di accesso per i membri dei gruppi o l'accesso alle applicazioni. Se è necessario esaminare periodicamente l'accesso, è anche possibile creare verifiche di accesso ricorrenti. Per altre informazioni su questi scenari, vedere [Gestire l'accesso utente](manage-user-access-with-access-reviews.md) e [Gestire l'accesso guest](manage-guest-access-with-access-reviews.md).

È possibile guardare un video rapido che illustra l'abilitazione delle verifiche di accesso:

>[!VIDEO https://www.youtube.com/embed/X1SL2uubx9M]

Questo articolo descrive come creare una o più verifiche di accesso per i membri del gruppo o l'accesso alle applicazioni.

## <a name="prerequisites"></a>Prerequisiti

- Azure AD Premium P2
- Amministratore globale o Amministratore utenti

Per altre informazioni, vedere [Requisiti relativi alle licenze](access-reviews-overview.md#license-requirements).

## <a name="create-one-or-more-access-reviews"></a>Creare una o più verifiche di accesso

1. Accedere al portale di Azure aprire la pagina [Governance delle identità](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

2. Nel menu a sinistra fare clic su **Verifiche di accesso**.

3. Fare clic su **Nuova verifica di accesso** per creare una nuova verifica di accesso.

    ![Riquadro Verifiche di accesso in Governance delle identità](./media/create-access-review/access-reviews.png)

4. In **Passaggio 1: selezionare gli elementi da esaminare** selezionare la risorsa da esaminare.

    ![Creare una verifica di accesso - Nome della verifica e descrizione](./media/create-access-review/select-what-review.png)

5. Se è stata **selezionata l'opzione Teams + Groups** nel passaggio 1, sono disponibili due opzioni nel passaggio 2
   - **Tutti Microsoft 365 gruppi con utenti guest.** Selezionare questa opzione se si desidera creare revisioni ricorrenti su tutti gli utenti guest in tutti i gruppi Microsoft Teams e M365 dell'organizzazione. È possibile scegliere di escludere determinati gruppi facendo clic su "Seleziona gruppi da escludere".
   - **Selezionare teams + groups (Team + gruppi).** Selezionare questa opzione se si vuole specificare un set finito di team e/o gruppi da esaminare. Dopo aver fatto clic su questa opzione, verrà visualizzato un elenco di gruppi a destra tra cui scegliere.

     ![Team e gruppi](./media/create-access-review/teams-groups.png)

     ![Team e gruppi scelti nell'interfaccia utente](./media/create-access-review/teams-groups-detailed.png)

6. Se nel passaggio 1 **è** stata selezionata l'opzione Applicazioni, è possibile selezionare una o più applicazioni nel passaggio 2.

    >[!NOTE]
    > La selezione di più gruppi e/o applicazioni comporta la creazione di più verifiche di accesso. Ad esempio, se si selezionano 5 gruppi da esaminare, verranno restituite 5 verifiche di accesso separate

   ![L'interfaccia visualizzata se si scelgono le applicazioni anziché i gruppi](./media/create-access-review/select-application-detailed.png)

7. Successivamente, nel passaggio 3 è possibile selezionare un ambito per la revisione. Le opzioni sono
   - **Solo utenti guest.** La selezione di questa opzione limita la verifica di accesso solo Azure AD utenti guest B2B nella directory.
   - **Tutti.** La selezione di questa opzione consente di impostare l'ambito della verifica di accesso a tutti gli oggetti utente associati alla risorsa.

    >[!NOTE]
    > Se nel passaggio 2 è stata selezionata l'opzione Tutti Microsoft 365 gruppi con utenti guest, l'unica opzione consiste nell'esaminare gli utenti guest nel passaggio 3

8. Fare clic su Avanti: Revisioni
9. Nella sezione **Selezione revisori** selezionare una o più persone per eseguire le verifiche di accesso. È possibile scegliere tra:
    - **Proprietario del gruppo (disponibile** solo quando si esegue una revisione in un team o in un gruppo)
    - **Utenti o gruppi selezionati**
    - **Gli utenti verificano l'accesso**
    - **Responsabili degli utenti.**
    Se si sceglie **Manager di utenti o** Proprietari **del**  gruppo, è anche possibile specificare un revisore di fallback. Ai revisori di fallback viene richiesto di eseguire una revisione quando l'utente non ha un manager specificato nella directory o il gruppo non ha un proprietario.

    ![nuova verifica di accesso](./media/create-access-review/new-access-review.png)

10. Nella sezione **Specificare la ricorrenza** della revisione è possibile specificare una frequenza, ad esempio **Settimanale, Mensile, Trimestrale, Semestrali, Annuale.** Si specifica quindi una **durata**, che definisce per quanto tempo una revisione sarà aperta per l'input dei revisori. La durata massima che è possibile impostare per una verifica mensile, ad esempio, è di 27 giorni, per evitare la sovrapposizione delle verifiche. È possibile abbreviare la durata per assicurarsi che l'input dei revisori venga applicato in precedenza. Successivamente, è possibile selezionare una **data di inizio** e una data di **fine.**

    ![Scegliere la frequenza con cui deve verificarsi la revisione](./media/create-access-review/frequency.png)

11. Fare clic **sul pulsante Avanti: Impostazioni** nella parte inferiore della pagina
12. Nelle impostazioni **al completamento è** possibile specificare cosa accade al termine della verifica

    ![Creare una verifica di accesso: al termine delle impostazioni](./media/create-access-review/upon-completion-settings-new.png)

Se si vuole rimuovere automaticamente l'accesso per gli utenti negati, impostare Applica automaticamente i risultati alla risorsa su Abilita. Per applicare manualmente i risultati al termine della verifica, impostare l'opzione su Disabilita.
Usare l'elenco Se i revisori non rispondono per specificare cosa accade agli utenti che non vengono esaminati dal revisore entro il periodo di revisione. Questa impostazione non ha alcun impatto sugli utenti che sono stati sottoposti a verifica manualmente dai revisori. Se la decisione finale del revisore è il rifiuto, l'accesso dell'utente verrà rimosso.

- **Nessuna modifica**: non viene apportata alcuna modifica all'accesso dell'utente
- **Rimuovi accesso**: l'accesso dell'utente viene rimosso
- **Approva accesso**: l'accesso dell'utente viene approvato
- **Accetta i consigli**: vengono applicati i consigli del sistema per rifiutare o approvare l'accesso continuo dell'utente

    ![Al completamento delle opzioni delle impostazioni](./media/create-access-review/upon-completion-settings-new.png)

Usare l'azione da applicare agli utenti **guest** negati per specificare cosa accade agli utenti guest se vengono negati.
- Rimuovere l'appartenenza dell'utente dalla risorsa rimuoverà l'accesso dell'utente negato al gruppo o all'applicazione in fase di revisione. Sarà comunque in grado di accedere al tenant.
- Blocca l'accesso dell'utente per 30 giorni, quindi rimuove l'utente dal tenant blocca l'accesso degli utenti negati al tenant, indipendentemente dal fatto che abbia accesso ad altre risorse. Se si verifica un errore o se un amministratore decide di ri-abilitare l'accesso, può farlo entro 30 giorni dopo che l'utente è stato disabilitato. Se non viene eseguita alcuna azione sugli utenti disabilitati, verranno eliminati dal tenant.

Per altre informazioni sulle procedure consigliate per rimuovere gli utenti guest che non hanno più accesso alle risorse nell'organizzazione, vedere l'articolo usare Azure AD Identity Governance per esaminare e rimuovere gli utenti esterni che non hanno più accesso alle [risorse.](access-reviews-external-users.md)

   >[!NOTE]
   >L'azione da applicare agli utenti guest negati non è configurabile nelle verifiche con ambito superiore a quello degli utenti guest. Non è inoltre configurabile per le revisioni di **Tutti i gruppi M365 con utenti guest.** Quando non è configurabile, l'opzione predefinita di rimozione dell'appartenenza dell'utente dalla risorsa viene usata per gli utenti negati.

13. In **Abilita helper decisionali di revisione** scegliere se si vuole che il revisore riceva le raccomandazioni durante il processo di revisione.

    ![Abilitare le opzioni degli helper decisionali](./media/create-access-review/helpers.png)

14. Nella sezione **Impostazioni avanzate** è possibile scegliere quanto segue:
    - Impostare **Giustificazione necessaria su** Abilita **per** richiedere al revisore di fornire un motivo per l'approvazione.
    - Impostare **Notifiche tramite posta elettronica** su **Abilita** per fare in modo Azure AD inviare notifiche tramite posta elettronica ai revisori all'avvio di una verifica di accesso e agli amministratori al termine di una verifica.
    - Impostare **Promemoria** su **Abilita** per fare in modo che Azure AD invii promemoria delle verifiche di accesso in corso ai revisori che non hanno completato la verifica. Questi promemoria saranno a metà della durata della revisione.
    - Il contenuto del messaggio di posta elettronica inviato ai revisori viene rigenerato automaticamente in base ai dettagli della revisione, ad esempio il nome della revisione, il nome della risorsa, la data di scadenza e così via. Se è necessario un modo per comunicare informazioni aggiuntive, ad esempio istruzioni aggiuntive o informazioni di contatto, è possibile specificare questi dettagli nella sezione Contenuto aggiuntivo per l'indirizzo di posta elettronica **del revisore.** Le informazioni immesse sono incluse nei messaggi di posta elettronica di invito e promemoria inviati ai revisori assegnati. La sezione evidenziata nell'immagine seguente mostra dove vengono visualizzate queste informazioni.


      ![contenuto aggiuntivo per il revisore](./media/create-access-review/additional-content-reviewer.png)

15. Fare clic **su Avanti: Rivedi e crea** per passare alla pagina successiva
16. Assegnare un nome alla verifica di accesso. Facoltativamente è possibile assegnare una descrizione alla verifica. Il nome e la descrizione vengono visualizzati dai revisori.
17. Esaminare le informazioni e selezionare **Crea**

       ![Schermata di creazione della revisione](./media/create-access-review/create-review.png)

## <a name="start-the-access-review"></a>Avviare la verifica di accesso

Una volta specificate le impostazioni per una verifica di accesso, fare clic su **Avvia**. La verifica di accesso verrà visualizzata nell'elenco con un indicatore dello stato.

![Elenco delle verifiche di accesso e relativo stato](./media/create-access-review/access-reviews-list.png)

Per impostazione predefinita, Azure AD invia un messaggio di posta elettronica ai revisori poco tempo dopo l'inizio della verifica. Se si imposta Azure AD in modo che non invii un messaggio di posta elettronica, assicurarsi di informare i revisori che vi è una verifica di accesso in attesa di completamento. È possibile visualizzare le istruzioni per verificare l'accesso [a gruppi o applicazioni.](perform-access-review.md) Se la verifica è per i guest di verificare il proprio accesso, mostrare loro le istruzioni per verificare l'accesso a gruppi [o applicazioni.](review-your-access.md)

Se gli utenti guest sono stati assegnati come revisori e questi non hanno accettato l'invito, non riceveranno un messaggio di posta elettronica dalle verifiche di accesso perché devono prima accettare l'invito prima della revisione.

## <a name="access-review-status-table"></a>Tabella dello stato della verifica di accesso

| Stato | Definizione |
|--------|------------|
|NotStarted | La verifica è stata creata, l'individuazione utente è in attesa di avvio. |
|Inizializzazione in corso   | L'individuazione utente è in corso per identificare tutti gli utenti che fanno parte della revisione. |
|Avvio in corso | È in corso l'avvio della verifica. Se le notifiche tramite posta elettronica sono abilitate, i messaggi di posta elettronica vengono inviati ai revisori. |
|InProgress | La verifica è stata avviata. Se le notifiche tramite posta elettronica sono abilitate, i messaggi di posta elettronica sono stati inviati ai revisori. I revisori possono inviare decisioni fino alla data di scadenza. |
|Completamento | La revisione è stata completata e i messaggi di posta elettronica vengono inviati al proprietario della revisione. |
|Revisione automatica | La revisione è in una fase di revisione del sistema. Il sistema sta registrando le decisioni per gli utenti che non sono stati esaminati in base a raccomandazioni o decisioni preconfigurate. |
|Verifica automatica | Le decisioni sono state registrate dal sistema per tutti gli utenti che non sono stati esaminati. La verifica è pronta per passare **ad Applicazione se** l'opzione Applica automaticamente è abilitata. |
|Applicazione | Non verrà apportata alcuna modifica all'accesso per gli utenti approvati. |
|Applicato | Gli utenti negati, se presenti, sono stati rimossi dalla risorsa o dalla directory. |
|Non riuscito | Impossibile completare la verifica. Questo errore potrebbe essere correlato all'eliminazione del tenant, a una modifica delle licenze o ad altre modifiche interne del tenant. |

## <a name="create-reviews-via-apis"></a>Creare verifiche tramite API

È anche possibile creare verifiche di accesso usando le API. Le operazioni eseguite per gestire le verifiche di accesso di gruppi e utenti di applicazioni nel portale di Azure possono essere svolte anche tramite le API Microsoft Graph. Per altre informazioni, vedere le informazioni di riferimento [Azure AD'API verifiche di accesso.](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) Per un esempio di codice, vedere Esempio di recupero Azure AD [verifiche di accesso tramite Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>Passaggi successivi

- [Verificare l'accesso a gruppi o applicazioni](perform-access-review.md)
- [Verificare l'accesso a gruppi o applicazioni](review-your-access.md)
- [Completare una verifica di accesso di gruppi o applicazioni](complete-access-review.md)