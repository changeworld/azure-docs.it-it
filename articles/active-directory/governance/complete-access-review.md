---
title: Completare una verifica di accesso dei gruppi & applicazioni-Azure AD
description: Informazioni su come completare una verifica di accesso dei membri del gruppo o dell'accesso alle applicazioni in Azure Active Directory verifiche di accesso.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 02/08/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: e31b9ce115ebe0723a9d082aaddd8056e486ed27
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176978"
---
# <a name="complete-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Completare una verifica di accesso dei gruppi e delle applicazioni nelle verifiche di accesso Azure AD

In qualità di amministratore, si [Crea una verifica di accesso di gruppi o applicazioni](create-access-review.md) e i revisori [eseguono la verifica dell'accesso](perform-access-review.md). Questo articolo descrive come visualizzare i risultati della verifica di accesso e applicare i risultati.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="prerequisites"></a>Prerequisiti

- Azure AD Premium P2
- Amministratore globale, Amministratore utenti, amministratore della sicurezza o lettore sicurezza

Per altre informazioni, vedere [Requisiti relativi alle licenze](access-reviews-overview.md#license-requirements).

## <a name="view-an-access-review"></a>Visualizzare una verifica di accesso

È possibile tenere traccia dello stato di avanzamento dei revisori che completano le proprie revisioni.

1. Accedere alla portale di Azure e aprire la [pagina governance delle identità](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Nel menu a sinistra fare clic su verifiche di **accesso**.

1. Nell'elenco fare clic su una verifica di accesso.

    Per visualizzare le istanze future delle verifiche di accesso, passare alla verifica di accesso e selezionare revisioni pianificate.

    Nella pagina **Panoramica** è possibile visualizzare lo stato dell'istanza corrente. Nessun diritto di accesso viene modificato nella directory fino al completamento della verifica.

     ![Revisione di tutti i gruppi aziendali](./media/complete-access-review/all-company-group.png)

    Tutti i pannelli sotto la corrente sono visualizzabili solo durante la durata di ogni istanza di revisione. 

    Nella pagina risultati vengono fornite ulteriori informazioni su ogni utente in revisione nell'istanza, inclusa la possibilità di arrestare, reimpostare e scaricare i risultati.

    ![Esaminare l'accesso guest tra gruppi Microsoft 365](./media/complete-access-review/all-company-group-results.png)


    Se si visualizza una verifica di accesso che esamina l'accesso guest tra Microsoft 365 gruppi, nel pannello panoramica viene elencato ogni gruppo della verifica. 
   
    ![esaminare l'accesso guest tra gruppi Microsoft 365](./media/complete-access-review/review-guest-access-across-365-groups.png)

    Fare clic su un gruppo per visualizzare lo stato di avanzamento della revisione sul gruppo, nonché per arrestare, reimpostare, applicare ed eliminare.

   ![esaminare i dettagli dell'accesso guest nei gruppi di Microsoft 365](./media/complete-access-review/progress-group-review.png)

1. Se si desidera arrestare una verifica di accesso prima di aver raggiunto la data di fine pianificata, fare clic sul pulsante **Arresta** .

    Quando si arresta una verifica, i revisori non saranno più in grado di fornire risposte. Non è possibile riavviare una verifica dopo che è stata interrotta.

1. Se non si è più interessati alla verifica di accesso, è possibile eliminarlo facendo clic sul pulsante **Elimina** .

## <a name="apply-the-changes"></a>Applicare le modifiche.

Se l' **opzione applica automaticamente i risultati alla risorsa** è stata abilitata in base alle impostazioni selezionate in **al termine**, l'applicazione automatica verrà eseguita dopo la data di fine della verifica o quando si arresta manualmente la revisione.

Se l' **opzione applica automaticamente i risultati alla risorsa** non è stata abilitata per la revisione, passare a **esaminare la cronologia** in **serie** dopo la scadenza della verifica oppure la revisione è stata arrestata in anticipo, quindi fare clic sull'istanza della revisione che si desidera applicare.

![Applicare le modifiche della verifica di accesso](./media/complete-access-review/apply-changes.png)

Fare clic su **applica** per applicare manualmente le modifiche. Se l'accesso di un utente è stato negato durante la verifica, quando si fa clic su **applica** Azure ad rimuove l'assegnazione dell'applicazione o dell'appartenenza.

![Pulsante Applica modifiche verifica di accesso](./media/complete-access-review/apply-changes-button.png)


Lo stato della revisione passerà da **completato** a stati intermedi, ad esempio l' **applicazione** e infine il **risultato** di stato applicato. È possibile che, dopo alcuni minuti, eventuali utenti rifiutati vengano rimossi dall'appartenenza al gruppo o dall'assegnazione dell'applicazione.

L'applicazione manuale o automatica dei risultati non ha effetto su un gruppo che ha origine in una directory locale o in un gruppo dinamico. Per modificare un gruppo che ha origine in locale, scaricare i risultati e applicare queste modifiche alla rappresentazione del gruppo in questa directory.

## <a name="retrieve-the-results"></a>Recuperare i risultati

Per visualizzare i risultati di una verifica di accesso monouso, fare clic sulla pagina **risultati** . Per visualizzare solo l'accesso di un utente, nella casella di ricerca digitare il nome visualizzato o il nome dell'entità utente di un utente il cui accesso è stato esaminato.

![Recuperare i risultati per una verifica di accesso](./media/complete-access-review/retrieve-results.png) 

Per visualizzare lo stato di una verifica di accesso attivo ricorrente, fare clic sulla pagina **risultati** .

Per visualizzare i risultati di un'istanza completata di una verifica di accesso ricorrente, fare clic su **Verifica cronologia**, quindi selezionare l'istanza specifica dall'elenco di istanze di verifica di accesso completate in base alla data di inizio e di fine dell'istanza. I risultati di questa istanza possono essere ottenuti dalla pagina **risultati** .

Per recuperare tutti i risultati di una verifica di accesso, fare clic sul pulsante **download** . Il file CSV risultante può essere visualizzato in Excel o in altri programmi aperti in file CSV con codifica UTF-8.

## <a name="remove-users-from-an-access-review"></a>Rimuovere gli utenti da una verifica di accesso

 Per impostazione predefinita, un utente eliminato rimarrà comunque in Azure AD per 30 giorni, periodo durante il quale un amministratore potrà eseguirne il ripristino se necessario.  Dopo 30 giorni l'utente verrà eliminato definitivamente.  Inoltre, tramite il portale di Azure Active Directory, un Amministratore globale può in modo esplicito [eliminare definitivamente un utente eliminato di recente](../fundamentals/active-directory-users-restore.md) prima della scadenza di tale periodo.  Dopo l'eliminazione definitiva di un utente, i dati riguardanti tale utente verranno rimossi dalle verifiche di accesso attive.  Le informazioni di controllo sugli utenti eliminati restano nel log di controllo.

## <a name="next-steps"></a>Passaggi successivi

- [Gestire l'accesso utente con le verifiche di accesso di Azure AD](manage-user-access-with-access-reviews.md)
- [Gestire l'accesso guest con le verifiche di accesso di Azure AD](manage-guest-access-with-access-reviews.md)
- [Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)
- [Creare una verifica di accesso degli utenti in un ruolo amministrativo Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md)
