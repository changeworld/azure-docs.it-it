---
title: Gestione del consenso alle applicazioni e valutazione delle richieste di consenso in Azure Active Directory
description: Informazioni su come gestire le richieste di consenso quando il consenso dell'utente è disabilitato o limitato e come valutare una richiesta di consenso dell'amministratore a livello di tenant a un'applicazione in Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: iangithinji
ms.reviewer: phsignor
ms.openlocfilehash: 3405181f9bace023950e583dfe1a334216bf0aa0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373945"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Gestione del consenso alle applicazioni e valutazione delle richieste di consenso

Microsoft [consiglia di disabilitare](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations) il consenso dell'utente finale alle applicazioni. In questo modo il processo decisionale verrà centralizzato con il team di amministratore della sicurezza e delle identità dell'organizzazione.

Dopo che il consenso dell'utente finale è stato disabilitato o limitato, è necessario assicurarsi che l'organizzazione rimanga sicura pur consentendo l'uso di applicazioni business critical. Questi passaggi sono fondamentali per ridurre al minimo l'impatto sul team di supporto dell'organizzazione e sugli amministratori IT, impedendo l'uso di account non gestiti in applicazioni di terze parti.

## <a name="process-changes-and-education"></a>Elaborare le modifiche e l'istruzione

 1. Valutare la possibilità di abilitare [il flusso di lavoro di](configure-admin-consent-workflow.md) consenso dell'amministratore per consentire agli utenti di richiedere l'approvazione dell'amministratore direttamente dalla schermata di consenso.

 2. Assicurarsi che tutti gli amministratori comprendano le autorizzazioni e il [framework](../develop/consent-framework.md)di consenso, come funziona la richiesta di consenso e come valutare una richiesta di consenso dell'amministratore a livello [di tenant.](#evaluating-a-request-for-tenant-wide-admin-consent) [](../develop/application-consent-experience.md)
 3. Esaminare i processi esistenti dell'organizzazione per consentire agli utenti di richiedere l'approvazione dell'amministratore per un'applicazione e, se necessario, apportare aggiornamenti. Se i processi vengono modificati:
    * Aggiornare la documentazione pertinente, il monitoraggio, l'automazione e così via.
    * Comunicare le modifiche ai processi a tutti gli utenti, gli sviluppatori, i team di supporto e gli amministratori IT interessati.

## <a name="auditing-and-monitoring"></a>Controllo e monitoraggio

1. [Controllare le app e le autorizzazioni concesse](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) nell'organizzazione per assicurarsi che in precedenza alle applicazioni non ingiustificate o sospette sia stato concesso l'accesso ai dati.

2. Per altre procedure consigliate e misure di sicurezza contro le applicazioni sospette che richiedono il consenso OAuth, vedere Rilevare e correggere le concessioni di consenso illecito [in Office 365.](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)

3. Se l'organizzazione ha la licenza appropriata:

    * Usare funzionalità [aggiuntive di controllo delle applicazioni OAuth in Microsoft Cloud App Security](/cloud-app-security/investigate-risky-oauth).
    * Usare Monitoraggio di Azure [cartelle di lavoro per monitorare le autorizzazioni e l'attività correlata](../reports-monitoring/howto-use-azure-monitor-workbooks.md) al consenso. La *cartella di lavoro Informazioni dettagliate* sul consenso offre una visualizzazione delle app in base al numero di richieste di consenso non riuscite. Ciò può essere utile per assegnare la priorità alle applicazioni per consentire agli amministratori di esaminare e decidere se concedere loro il consenso dell'amministratore.

### <a name="additional-considerations-for-reducing-friction"></a>Considerazioni aggiuntive per ridurre i attriti

Per ridurre al minimo l'impatto sulle applicazioni attendibili e critiche che sono già in uso, è consigliabile concedere in modo proattivo il consenso dell'amministratore alle applicazioni con un numero elevato di concessioni di consenso utente:

1. Eseguire un inventario delle app già aggiunte all'organizzazione con utilizzo elevato, in base ai log di accesso o all'attività di concessione del consenso. Uno [script](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) di PowerShell può essere usato per individuare rapidamente e facilmente le applicazioni con un numero elevato di concessioni di consenso utente.

2. Valutare le applicazioni principali a cui non è ancora stato concesso il consenso dell'amministratore.

   > [!IMPORTANT]
   > Valutare attentamente un'applicazione prima di concedere il consenso dell'amministratore a livello di tenant, anche se molti utenti dell'organizzazione hanno già dato il proprio consenso.

3. Per ogni applicazione approvata, concedere il consenso dell'amministratore a livello di tenant usando uno dei metodi descritti di seguito.

4. Per ogni applicazione approvata, provare [a limitare l'accesso utente.](configure-user-consent.md)

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Valutazione di una richiesta di consenso dell'amministratore a livello di tenant

La concessione del consenso dell'amministratore a livello di tenant è un'operazione sensibile.  Le autorizzazioni verranno concesse per conto dell'intera organizzazione e possono includere le autorizzazioni per tentare operazioni con privilegi elevati. Ad esempio, la gestione dei ruoli, l'accesso completo a tutte le cassette postali o a tutti i siti e la rappresentazione utente completa.

Prima di concedere il consenso dell'amministratore a livello di tenant, è necessario assicurarsi di considerare attendibili l'applicazione e l'autore dell'applicazione per il livello di accesso che si sta concedendo. Se non si è certi di comprendere chi controlla l'applicazione e perché l'applicazione richiede le autorizzazioni, non *concedere il consenso*.

L'elenco seguente fornisce alcune raccomandazioni da considerare quando si valuta una richiesta di concessione del consenso dell'amministratore.

* **Informazioni sul [framework di autorizzazioni e consenso](../develop/consent-framework.md) in Microsoft Identity Platform.**

* **Comprendere la differenza tra [le autorizzazioni delegate e le autorizzazioni dell'applicazione](../develop/v2-permissions-and-consent.md#permission-types).**

   Le autorizzazioni dell'applicazione consentono all'applicazione di accedere ai dati per l'intera organizzazione, senza alcuna interazione dell'utente. Le autorizzazioni delegate consentono all'applicazione di agire per conto di un utente che a un certo punto è stato connesso all'applicazione.

* **Informazioni sulle autorizzazioni richieste.**

   Le autorizzazioni richieste dall'applicazione sono elencate nella richiesta [di consenso](../develop/application-consent-experience.md). Espandendo il titolo dell'autorizzazione verrà visualizzata la descrizione dell'autorizzazione. La descrizione delle autorizzazioni dell'applicazione in genere termina con "senza un utente connesso". La descrizione delle autorizzazioni delegate terminerà in genere con "per conto dell'utente connesso". Le autorizzazioni per l'API Microsoft Graph sono descritte [in](/graph/permissions-reference) informazioni di riferimento sulle autorizzazioni Microsoft Graph. Fare riferimento alla documentazione per altre API per comprendere le autorizzazioni che espongono.

   Se non si comprende un'autorizzazione richiesta, *non concedere il consenso*.

* **Informazioni sull'applicazione che richiede le autorizzazioni e su chi l'ha pubblicata.**

   Diffidare delle applicazioni dannose che tentano di avere un aspetto simile ad altre applicazioni.

   In caso di dubbi sulla legittimità di un'applicazione o del relativo editore, *non concedere il consenso*. Cercare invece una conferma aggiuntiva, ad esempio direttamente dall'autore dell'applicazione.

* **Assicurarsi che le autorizzazioni richieste siano allineate alle funzionalità previste dall'applicazione.**

   Ad esempio, un'applicazione che offre la gestione del sito di SharePoint può richiedere l'accesso delegato per leggere tutte le raccolte siti, ma non richiede necessariamente l'accesso completo a tutte le cassette postali o privilegi di rappresentazione completi nella directory.

   Se si sospetta che l'applicazione chieda più autorizzazioni di quelle necessarie, *non concedere il consenso*. Per ottenere altri dettagli, contattare l'autore dell'applicazione.

## <a name="granting-consent-as-an-administrator"></a>Concessione del consenso come amministratore

### <a name="granting-tenant-wide-admin-consent"></a>Concessione del consenso dell'amministratore a livello di tenant
Vedere Concedere il consenso dell'amministratore a livello di [tenant](grant-admin-consent.md) a un'applicazione per istruzioni dettagliate per concedere il consenso dell'amministratore a livello di tenant dal portale di Azure, usando Azure AD PowerShell o dalla richiesta di consenso.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Concessione del consenso per conto di un utente specifico
Anziché concedere il consenso per l'intera organizzazione, un amministratore può anche usare [l'API Microsoft Graph](/graph/use-the-api) per concedere il consenso alle autorizzazioni delegate per conto di un singolo utente. Per altre informazioni, vedere [Ottenere l'accesso per conto di un utente.](/graph/auth-v2-user)

## <a name="limiting-user-access-to-applications"></a>Limitazione dell'accesso utente alle applicazioni
L'accesso degli utenti alle applicazioni può comunque essere limitato anche quando è stato concesso il consenso dell'amministratore a livello di tenant. Per altre informazioni su come richiedere l'assegnazione di utenti a un'applicazione, vedere Metodi [per l'assegnazione di utenti e gruppi.](./assign-user-or-group-access-portal.md)

Per una panoramica più ampia, inclusa la gestione di scenari complessi aggiuntivi, vedere Uso di Azure AD [per la gestione dell'accesso alle applicazioni.](what-is-access-management.md)

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Disabilitare tutte le operazioni future di consenso dell'utente per qualsiasi applicazione
La disabilitazione di consenso da parte dell'utente per l'intera directory impedisce agli utenti finali di consentire l'accesso a qualsiasi applicazione. Gli amministratori possono comunque dare il consenso per conto dell'utente. Per informazioni sul consenso per le applicazioni e sui motivi per cui può essere opportuno o meno concederlo, vedere [Informazioni sul consenso dell'utente e dell'amministratore](../develop/howto-convert-app-to-be-multi-tenant.md).

Per disabilitare tutte le future operazioni di consenso utente nell'intera directory, seguire questa procedura:
1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.
2.  Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3.  Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4.  Selezionare **Utenti e gruppi** nel menu di spostamento.
5.  Selezionare **Impostazioni utente.**
6.  Disabilitare tutte le future operazioni di consenso degli utenti impostando l'opzione **Gli utenti possono consentire alle app di accedere ai propri dati** su **No** e facendo clic sul pulsante **Salva**.

## <a name="next-steps"></a>Passaggi successivi
* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [Configurare il flusso di lavoro di consenso dell'amministratore](configure-admin-consent-workflow.md)
* [Configurare la modalità con cui gli utenti finali consentono le applicazioni](configure-user-consent.md)
* [Autorizzazioni e consenso in Microsoft Identity Platform](../develop/v2-permissions-and-consent.md)
