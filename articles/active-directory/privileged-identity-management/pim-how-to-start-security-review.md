---
title: Creare una verifica di accesso dei ruoli Azure AD in PIM-Azure AD | Microsoft Docs
description: Informazioni su come creare una verifica di accesso dei ruoli Azure AD in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 3/16/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 310122177d4bd1603f5f498aa2a51620eeda4a20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592797"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Creare una verifica di accesso dei ruoli Azure AD in Privileged Identity Management

Per ridurre i rischi associati alle assegnazioni di ruolo obsolete, è necessario esaminare periodicamente l'accesso. È possibile usare Azure AD Privileged Identity Management (PIM) per creare verifiche di accesso per i ruoli Azure AD con privilegi. È anche possibile configurare le verifiche di accesso periodiche che vengono eseguite automaticamente.

Questo articolo descrive come creare una o più verifiche di accesso per i ruoli di Azure AD con privilegi.

## <a name="prerequisites"></a>Prerequisiti

[Amministratore dei ruoli con privilegi](../roles/permissions-reference.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Apri verifiche di accesso

1. Accedere a [portale di Azure](https://portal.azure.com/) con un utente membro del ruolo di amministratore dei ruoli con privilegi.

1. Aprire **Azure ad Privileged Identity Management**.

1. Selezionare **Azure ad ruoli**.

1. In Gestisci selezionare verifiche di accesso e quindi fare clic **su** **nuovo**.

    ![Azure AD Roles-elenco di verifiche di accesso che mostra lo stato di tutte le revisioni](./media/pim-how-to-start-security-review/access-reviews.png)

Fare clic su **Nuova** per creare una nuova verifica di accesso.

1. Assegnare un nome alla verifica di accesso. Facoltativamente è possibile assegnare una descrizione alla verifica. Il nome e la descrizione vengono visualizzati dai revisori.

    ![Creare una verifica di accesso - Nome della verifica e descrizione](./media/pim-how-to-start-security-review/name-description.png)

1. Impostare un valore per **Data di inizio**. Per impostazione predefinita, la verifica di accesso viene eseguita una sola volta, viene avviata lo stesso giorno in cui viene creata e termina entro un mese. È possibile cambiare le date di inizio e di fine per iniziare la verifica di accesso in futuro e impostare la durata sul numero di giorni desiderato.

    ![Data di inizio, frequenza, durata, fine, numero di volte e data di fine](./media/pim-how-to-start-security-review/start-end-dates.png)

1. Per rendere ricorrente la verifica di accesso, modificare l'impostazione **Frequenza** da **Singola** a **Settimanale**, **Mensile**, **Trimestrale** o **Annuale** o **Semestrale**. Usare il dispositivo di scorrimento **Durata** o la casella di testo per definire il numero di giorni per cui ogni revisione della serie ricorrente verrà aperta per l'input dai revisori. La durata massima che è possibile impostare per una verifica mensile, ad esempio, è di 27 giorni, per evitare la sovrapposizione delle verifiche.

1. Usare l'impostazione **Fine** per specificare come terminare la serie di verifiche di accesso ricorrenti. La serie può terminare in tre modi: può essere eseguita in modo continuo per avviare le verifiche per un periodo illimitato o fino a una data specifica oppure terminare dopo che è stato completato un numero definito di occorrenze. Un utente con ruolo Amministratore utente o Amministratore globale può interrompere la serie dopo la creazione modificando la data in **Impostazioni**, in modo che termini in tale data.

1. Nella sezione **Utenti** selezionare uno o più ruoli di cui si desidera verificare l'appartenenza.

    ![Ambito degli utenti per verificare l'appartenenza ai ruoli di](./media/pim-how-to-start-security-review/users.png)

    > [!NOTE]
    > - I ruoli selezionati qui includono sia i [ruoli permanenti che idonei](../privileged-identity-management/pim-how-to-add-role-to-user.md).
    > - Se si seleziona più di un ruolo, vengono create più verifiche di accesso. Se ad esempio si selezionano cinque ruoli, vengono create cinque verifiche di accesso separate.
    > - Per i ruoli con i gruppi assegnati, l'accesso di ogni gruppo collegato al ruolo in fase di revisione verrà esaminato come parte della verifica di accesso.
    Se si sta creando una verifica di accesso dei **ruoli di Azure AD**, di seguito è riportato un esempio dell'elenco Verifica l'appartenenza.

    ![Riquadro Verifica l'appartenenza che elenca i ruoli di Azure AD che è possibile selezionare](./media/pim-how-to-start-security-review/review-membership.png)

    Se si sta creando una verifica di accesso dei **ruoli delle risorse di Azure**, l'immagine seguente riporta un esempio dell'elenco Verifica l'appartenenza.

    ![Riquadro Verifica l'appartenenza che elenca i ruoli delle risorse di Azure che è possibile selezionare](./media/pim-how-to-start-security-review/review-membership-azure-resource-roles.png)

1. Nella sezione **Revisori** selezionare una o più persone per la verifica di tutti gli utenti. In alternativa è possibile fare in modo che i membri verifichino il proprio accesso.

    ![Elenco dei revisori degli utenti o membri selezionati (autonomo)](./media/pim-how-to-start-security-review/reviewers.png)

    - **Utenti selezionati**: usare questa opzione quando non è noto chi abbia bisogno dell'accesso. Con questa opzione è possibile assegnare l'esecuzione della revisione a un proprietario delle risorse o a un gestore del gruppo.
    - **Membri (autonomo)** : usare questa opzione per fare in modo che gli utenti verifichino le proprie assegnazioni di ruolo. Quando questa opzione è selezionata, i gruppi assegnati al ruolo non faranno parte della verifica.
    - **Manager** : usare questa opzione per fare in modo che il responsabile dell'utente riveda l'assegnazione di ruolo. Quando si seleziona Manager, sarà anche possibile specificare un revisore di fallback. Ai revisori di fallback viene richiesto di esaminare un utente quando l'utente non dispone di un responsabile specificato nella directory. I gruppi assegnati al ruolo verranno esaminati dal revisore del fallback se ne viene selezionato uno. 

### <a name="upon-completion-settings"></a>Impostazioni al completamento

1. Per specificare cosa succede dopo il completamento di una verifica, espandere la sezione **Impostazioni al completamento**.

    ![Le Impostazioni al completamento per l'applicazione automatica e la revisione non rispondono](./media/pim-how-to-start-security-review/upon-completion-settings.png)

1. Se si vuole rimuovere automaticamente l'accesso per gli utenti rifiutati, impostare l'opzione **Applica automaticamente i risultati alla risorsa** su **Abilita**. Per applicare manualmente i risultati al termine della verifica, impostare l'opzione su **Disabilita**.

1. Usare l'elenco **In caso di mancata risposta del revisore** per specificare cosa accade per gli utenti non sottoposti a verifica dal revisore entro il periodo di verifica. Questa impostazione non ha alcun impatto sugli utenti che sono stati sottoposti a verifica manualmente dai revisori. Se la decisione finale del revisore è il rifiuto, l'accesso dell'utente verrà rimosso.

    - **Nessuna modifica**: non viene apportata alcuna modifica all'accesso dell'utente
    - **Rimuovi accesso**: l'accesso dell'utente viene rimosso
    - **Approva accesso**: l'accesso dell'utente viene approvato
    - **Accetta i consigli**: vengono applicati i consigli del sistema per rifiutare o approvare l'accesso continuo dell'utente

### <a name="advanced-settings"></a>Impostazioni avanzate

1. Per specificare impostazioni aggiuntive, espandere la sezione **Impostazioni avanzate**.

    ![Impostazioni avanzate per Mostra suggerimenti, Richiedi il motivo all'approvazione, Notifiche tramite posta elettronica e Promemoria](./media/pim-how-to-start-security-review/advanced-settings.png)

1. Impostare **Mostra i consigli** su **Abilita** per mostrare ai revisori i consigli del sistema basati sulle informazioni di accesso dell'utente.

1. Impostare **Richiedi il motivo all'approvazione** su **Abilita** per richiedere al revisore di specificare un motivo per l'approvazione.

1. Impostare **Notifiche tramite posta elettronica** su **Abilita** per fare in modo che Azure AD invii notifiche tramite posta elettronica ai revisori all'avvio di una verifica di accesso e agli amministratori al completamento di una verifica.

1. Impostare **Promemoria** su **Abilita** per fare in modo che Azure AD invii promemoria delle verifiche di accesso in corso ai revisori che non hanno completato la verifica.
1. Il contenuto del messaggio di posta elettronica inviato ai revisori viene generato automaticamente in base ai dettagli della revisione, ad esempio il nome della revisione, il nome della risorsa, la scadenza e così via. Se è necessario un modo per comunicare informazioni aggiuntive, ad esempio istruzioni aggiuntive o informazioni di contatto, è possibile specificare questi dettagli nel **contenuto aggiuntivo per l'indirizzo di posta elettronica del revisore** che verrà incluso nei messaggi di posta elettronica di invito e promemoria inviati ai revisori assegnati. La sezione evidenziata di seguito consente di visualizzare queste informazioni.

    ![Contenuto del messaggio di posta elettronica inviato ai revisori con i punti salienti](./media/pim-how-to-start-security-review/email-info.png)

## <a name="start-the-access-review"></a>Avviare la verifica di accesso

Dopo aver specificato le impostazioni per una verifica di accesso, selezionare **Avvia**. La verifica di accesso verrà visualizzata nell'elenco con un indicatore dello stato.

![Elenco di verifiche di accesso che mostra lo stato delle revisioni avviate](./media/pim-how-to-start-security-review/access-reviews-list.png)

Per impostazione predefinita, Azure AD invia un messaggio di posta elettronica ai revisori poco tempo dopo l'inizio della verifica. Se si imposta Azure AD in modo che non invii un messaggio di posta elettronica, assicurarsi di informare i revisori che vi è una verifica di accesso in attesa di completamento. È possibile visualizzare le istruzioni su come [verificare l'accesso ai ruoli Azure ad](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gestire la verifica di accesso

È possibile tenere traccia dello stato di avanzamento dei revisori che completano le proprie revisioni nella pagina **Panoramica** della verifica di accesso. Nessun diritto di accesso viene modificato nella directory fino al [completamento della verifica](pim-how-to-complete-review.md).

![Pagina di panoramica delle verifiche di accesso che mostra i dettagli della verifica](./media/pim-how-to-start-security-review/access-review-overview.png)

Se si verifica una sola volta, al termine del periodo di verifica di accesso o se l'amministratore interrompe la verifica di accesso, seguire i passaggi descritti in [completare una verifica di accesso dei ruoli Azure ad](pim-how-to-complete-review.md) per visualizzare e applicare i risultati.  

Per gestire una serie di verifiche di accesso, passare alla verifica di accesso e trovare le occorrenze imminenti nelle revisioni pianificate e modificare di conseguenza la data di fine o aggiungere/rimuovere revisori.

In base alle impostazioni selezionate in **al termine**, l'applicazione automatica verrà eseguita dopo la data di fine della verifica o quando si arresta manualmente la revisione. Lo stato della revisione passerà da **completato** a stati intermedi, ad esempio l' **applicazione** e infine lo stato **applicato**. È necessario che gli utenti negati, se presenti, vengano rimossi dai ruoli in pochi minuti.

## <a name="next-steps"></a>Passaggi successivi

- [Controllare l'accesso ai ruoli di Azure AD](pim-how-to-perform-security-review.md)
- [Completare una verifica di accesso dei ruoli Azure AD](pim-how-to-complete-review.md)
- [Creare una verifica di accesso dei ruoli delle risorse di Azure](pim-resource-roles-start-access-review.md)
