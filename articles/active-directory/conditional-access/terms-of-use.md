---
title: Condizioni per l'utilizzo - Azure Active Directory | Microsoft Docs
description: Introduzione all'uso Azure Active Directory condizioni per l'utilizzo per presentare informazioni a dipendenti o guest prima di ottenere l'accesso.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 01/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c8e18979ff1575e1a050244a96e7858cdce46b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530253"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory condizioni per l'utilizzo

Azure AD criteri relativi alle condizioni per l'utilizzo forniscono un metodo semplice che le organizzazioni possono usare per presentare informazioni agli utenti finali. In questo modo si garantisce che gli utenti vedano le dichiarazioni rilevanti di non responsabilità che si riferiscono ai requisiti legali o di conformità. Questo articolo descrive come iniziare a usare i criteri per le condizioni per l'utilizzo (ToU).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Panoramica video

Il video seguente offre una rapida panoramica dei criteri ToU.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

Per altri video, vedere:
- [Come distribuire criteri per le condizioni per l'utilizzo in Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Come implementare i criteri relativi alle condizioni per l'utilizzo in Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Cosa è possibile fare con le condizioni per l'utilizzo?

Azure AD i criteri per le condizioni per l'utilizzo hanno le funzionalità seguenti:

- Richiedere ai dipendenti o ai clienti guest di accettare i criteri relativi alle condizioni per l'utilizzo prima di ottenere l'accesso.
- Richiedere ai dipendenti o ai guest di accettare i criteri relativi alle condizioni per l'utilizzo in ogni dispositivo prima di ottenere l'accesso.
- Richiedere ai dipendenti o ai clienti guest di accettare i criteri relativi alle condizioni per l'utilizzo in base a una pianificazione ricorrente.
- Richiedere ai dipendenti o ai guest di accettare i criteri relativi alle condizioni per l'utilizzo prima di registrare le informazioni di sicurezza in Azure AD Multi-Factor Authentication (MFA).
- Richiedere ai dipendenti di accettare i criteri relativi alle condizioni per l'utilizzo prima di registrare le informazioni di sicurezza Azure AD reimpostazione della password self-service.
- Presentare criteri generali per le condizioni per l'utilizzo per tutti gli utenti dell'organizzazione.
- Presentare criteri specifici per le condizioni per l'utilizzo in base agli attributi utente (ad esempio medici o infermieri oppure dipendenti nazionali o internazionali, tramite [gruppi dinamici](../enterprise-users/groups-dynamic-membership.md)).
- Presentare criteri specifici per le condizioni per l'utilizzo quando si accede ad applicazioni ad alto impatto aziendale, ad esempio Salesforce.
- Presentare i criteri relativi alle condizioni per l'utilizzo in lingue diverse.
- Elencare chi ha o non ha accettato i criteri per le condizioni per l'utilizzo.
- Contribuire a rispettare le normative sulla privacy.
- Visualizzare un log delle attività dei criteri delle condizioni per l'utilizzo per la conformità e il controllo.
- Creare e gestire i criteri delle condizioni per [l'utilizzo usando Microsoft Graph API](/graph/api/resources/agreement) (attualmente in anteprima).

## <a name="prerequisites"></a>Prerequisiti

Per usare e configurare Azure AD criteri per le condizioni per l'utilizzo, è necessario disporre di:

- Una sottoscrizione di Azure AD Premium P1, P2, EMS E3 o EMS E5.
   - Se non si dispone di una di queste sottoscrizioni, è possibile [ottenere Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) oppure [abilitare la versione di valutazione di Azure AD Premium](https://azure.microsoft.com/trial/get-started-active-directory/).
- Uno dei seguenti account di amministratore per la directory da configurare:
   - Amministratore globale
   - Amministratore della protezione
   - Amministratore accesso condizionale

## <a name="terms-of-use-document"></a>Documento sulle condizioni per l'utilizzo

Azure AD criteri relativi alle condizioni per l'utilizzo usano il formato PDF per presentare il contenuto. Il contenuto del file PDF può essere di qualsiasi tipo, ad esempio documenti di contratti esistenti. Questo consente di acquisire il consenso degli utenti finali durante l'accesso. Per supportare gli utenti sui dispositivi mobili, le dimensioni del carattere consigliato nel file PDF sono 24 punti.

## <a name="add-terms-of-use"></a>Aggiungere le condizioni per l'utilizzo

Dopo aver finalizzato il documento dei criteri relativi alle condizioni per l'utilizzo, usare la procedura seguente per aggiungerlo.

1. Accedere ad Azure come amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale.
1. Passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).

    ![Accesso condizionale - pannello Condizioni per l'utilizzo condizionale](./media/terms-of-use/tou-blade.png)

1. Fare clic su **Nuove condizioni**.

    ![Riquadro Nuovo periodo di utilizzo per specificare le impostazioni delle condizioni per l'utilizzo](./media/terms-of-use/new-tou.png)

1. Nella casella **Nome** immettere un nome per i criteri delle condizioni per l'utilizzo che verranno usati nel portale di Azure.
1. Nella casella **Nome visualizzato** immettere un titolo visualizzato dagli utenti quando eseguono l'accesso.
1. Per **Condizioni per l'utilizzo documento,** passare al PDF dei criteri per le condizioni per l'utilizzo finalizzato e selezionarlo.
1. Selezionare la lingua per il documento dei criteri relativi alle condizioni per l'utilizzo. L'opzione language consente di caricare più criteri per le condizioni per l'utilizzo, ognuno con una lingua diversa. La versione dei criteri delle condizioni per l'utilizzo che verrà visualizzata da un utente finale sarà basata sulle preferenze del browser.
1. Per richiedere agli utenti finali di visualizzare i criteri  relativi alle condizioni per l'utilizzo prima di accettarli, impostare Richiedi agli utenti di espandere le condizioni per l'utilizzo su **On**.
1. Per richiedere agli utenti finali di accettare i criteri relativi alle condizioni per l'utilizzo in ogni dispositivo da cui accedono, impostare Richiedi agli utenti di acconsentire per **ogni** dispositivo su **On**. Se questa opzione è abilitata, potrebbe essere necessario installare altre applicazioni. Per altre informazioni, vedere [Condizioni per l'utilizzo per dispositivo.](#per-device-terms-of-use)
1. Se si desidera scadere i consensi dei criteri delle condizioni per l'utilizzo in base a una pianificazione, impostare **Consensi** di scadenza su **On**. Se si imposta su On, vengono visualizzate due impostazioni aggiuntive di pianificazione.

    ![Impostazioni di scadenza dei consensi per impostare la data di inizio, la frequenza e la durata](./media/terms-of-use/expire-consents.png)

1. Usare le **impostazioni Scadenza a partire da** e **Frequenza** per specificare la pianificazione per le scadenze dei criteri delle condizioni per l'utilizzo. La tabella seguente illustra il risultato di un paio di impostazioni di esempio:

   | Scadenza a partire da | Frequenza | Risultato |
   | --- | --- | --- |
   | Data odierna  | Mensile | A partire da oggi, gli utenti devono accettare i criteri relativi alle condizioni per l'utilizzo e quindi riaccept ogni mese. |
   | Data nel futuro  | Mensile | A partire da oggi, gli utenti devono accettare i criteri relativi alle condizioni per l'utilizzo. Quando si verifica la data futura, scadono i consensi e quindi gli utenti devono riaccettare ogni mese.  |

   Ad esempio, se si imposta la scadenza a partire dalla data **1 gen** e la frequenza su **Mensile**, ecco come le scadenze potrebbero verificarsi per i due utenti:

   | User | Prima data di accettazione | Prima data di scadenza | Seconda data di scadenza | Terza data di scadenza |
   | --- | --- | --- | --- | --- |
   | Alice | 1 gen | 1 feb | 1 mar | 1 apr |
   | Bob | 15 gen | 1 feb | 1 mar | 1 apr |

1. Usare l'impostazione Durata prima che la **riacceptance richieda (giorni)** per specificare il numero di giorni prima che l'utente deve riaccept i criteri relativi alle condizioni per l'utilizzo. Ciò consente agli utenti di seguire una pianificazione personalizzata. Ad esempio, se si imposta la durata su **30** giorni, ecco come le scadenze potrebbero verificarsi per due utenti:

   | User | Prima data di accettazione | Prima data di scadenza | Seconda data di scadenza | Terza data di scadenza |
   | --- | --- | --- | --- | --- |
   | Alice | 1 gen | 31 gen | 2 mar | 1 apr |
   | Bob | 15 gen | 14 feb | 16 mar | 15 apr |

   È possibile usare le impostazioni **Expire consents** (Scadenza consensi) e Duration (Durata) prima che la **riacceptance richieda (giorni)** insieme, ma in genere si usa una o l'altra.

1. In **Accesso condizionale** usare l'elenco applica con il modello di criteri di **accesso** condizionale per selezionare il modello per applicare i criteri delle condizioni per l'utilizzo.

    ![Elenco a discesa Accesso condizionale per selezionare un modello di criteri](./media/terms-of-use/conditional-access-templates.png)

   | Modello | Descrizione |
   | --- | --- |
   | **Accesso alle app cloud per tutti i guest** | Verranno creati criteri di accesso condizionale per tutti i guest e tutte le app cloud. Questi criteri influiscono sul portale di Azure. Dopo la creazione, potrebbe essere necessario disconnettersi e accedere. |
   | **Accesso alle app cloud per tutti gli utenti** | Verranno creati criteri di accesso condizionale per tutti gli utenti e tutte le app cloud. Questi criteri influiscono sul portale di Azure. Dopo la creazione, sarà necessario disconnettersi e accedere. |
   | **Criteri personalizzati** | Selezionare gli utenti, i gruppi e le app a cui verranno applicati i criteri relativi alle condizioni per l'utilizzo. |
   | **Creare criteri di accesso condizionale in un secondo momento** | Questi criteri delle condizioni per l'utilizzo verranno visualizzati nell'elenco di controllo delle concessioni quando si creano criteri di accesso condizionale. |

   >[!IMPORTANT]
   >I controlli dei criteri di accesso condizionale (inclusi i criteri delle condizioni per l'utilizzo) non supportano l'imposizione sugli account del servizio. È consigliabile escludere tutti gli account del servizio dai criteri di accesso condizionale.

    I criteri di accesso condizionale personalizzati consentono criteri granulari per le condizioni per l'utilizzo, fino a un'applicazione cloud o a un gruppo di utenti specifico. Per altre informazioni, vedere Avvio rapido: Richiedere l'accettazione delle condizioni per l'utilizzo [prima di accedere alle app cloud.](require-tou.md)

1. Fare clic su **Crea**.

    Se è stato selezionato un modello di accesso condizionale personalizzato, viene visualizzata una nuova schermata che consente di creare i criteri di accesso condizionale personalizzati.

   ![Nuovo riquadro Accesso condizionale se si è scelto il modello di criteri di accesso condizionale personalizzato](./media/terms-of-use/custom-policy.png)

   A questo punto dovrebbero essere visualizzati i nuovi criteri delle condizioni per l'utilizzo.

   ![Nuove condizioni per l'utilizzo elencate nel pannello Condizioni per l'utilizzo](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Visualizzare il report degli utenti che hanno accettato e rifiutato

Nel pannello delle condizioni per l'utilizzo è visualizzato il numero di utenti che hanno accettato e rifiutato. Questi conteggi e gli utenti che hanno accettato/rifiutato vengono archiviati per tutta la durata dei criteri delle condizioni per l'utilizzo.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).

    ![Condizioni per l'utilizzo che elenca il numero di utenti visualizzati che sono stati accettati e rifiutati](./media/terms-of-use/view-tou.png)

1. Per i criteri relativi alle condizioni per l'utilizzo, fare clic sui numeri in **Accettato** o **Rifiutato** per visualizzare lo stato corrente per gli utenti.

    ![Condizioni per l'utilizzo riquadro dei consensi che elenca gli utenti che hanno accettato](./media/terms-of-use/accepted-tou.png)

1. Per visualizzare la cronologia per un singolo utente, fare clic sui puntini di sospensione (**...**) e quindi **Visualizza cronologia**.

    ![Menu di scelta rapida Visualizza cronologia per un utente](./media/terms-of-use/view-history-menu.png)

   Nel riquadro di visualizzazione della cronologia, si visualizza una cronologia di tutte le accettazioni, i rifiuti e le scadenze.

   ![Il riquadro Visualizza cronologia elenca le accettazioni, i rifiuti e le scadenze della cronologia per un utente](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Visualizzare i log di controllo di Azure AD

Se si desidera visualizzare attività aggiuntive, i criteri Azure AD condizioni per l'utilizzo includono i log di controllo. Ogni consenso dell'utente genera un evento nei log di controllo che viene conservato per **30 giorni**. È possibile visualizzare questi log nel portale o scaricarli come file CSV.

Per iniziare a usare i log di controllo di Azure AD, seguire questa procedura:

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).
1. Selezionare un criterio per le condizioni per l'utilizzo.
1. Fare clic su **Visualizza log di controllo**.

    ![Condizioni per l'utilizzo con l'opzione Visualizza log di controllo evidenziata](./media/terms-of-use/audit-tou.png)

1. Nella schermata dei log di controllo di Azure AD è possibile filtrare le informazioni usando i menu a discesa per visualizzare informazioni specifiche dei log di controllo.

    È inoltre possibile fare clic su **Download** per scaricare le informazioni in un file con estensione CSV per l'uso in locale.

   ![Azure AD schermata dei log di controllo che elenca la data, i criteri di destinazione, i criteri avviati da e l'attività](./media/terms-of-use/audit-logs-tou.png)

   Se si fa clic su un log, viene visualizzato un riquadro con i dettagli delle attività aggiuntive.

   ![Dettagli attività per un log che mostra l'attività, lo stato dell'attività, avviato da, i criteri di destinazione](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>Quali sono le condizioni per l'utilizzo per gli utenti?

Dopo la creazione e l'applicazione di un criterio toU, gli utenti che sono nell'ambito visualizzano la schermata seguente durante l'accesso.

![Condizioni per l'utilizzo di esempio visualizzate quando un utente esegue l'accesso](./media/terms-of-use/user-tou.png)

Gli utenti possono visualizzare i criteri relativi alle condizioni per l'utilizzo e, se necessario, usare i pulsanti per eseguire lo zoom avanti e indietro.

![Visualizzazione delle condizioni per l'utilizzo con i pulsanti di zoom](./media/terms-of-use/zoom-buttons.png)

La schermata seguente mostra l'aspetto dei criteri toU nei dispositivi mobili.

![Condizioni per l'utilizzo di esempio visualizzate quando un utente accede a un dispositivo mobile](./media/terms-of-use/mobile-tou.png)

Agli utenti viene richiesto di accettare i criteri delle condizioni per l'utilizzo una sola volta e non potranno più visualizzare i criteri relativi alle condizioni per l'utilizzo nei successivi tentativi di accesso.

### <a name="how-users-can-review-their-terms-of-use"></a>Come gli utenti possono esaminare le condizioni per l'utilizzo

Gli utenti possono esaminare e visualizzare i criteri delle condizioni per l'utilizzo accettati usando la procedura seguente.

1. Accedere a [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. Nell'angolo superiore destro fare clic sul proprio nome e selezionare **Profilo**.

    ![Sito MyApps con il riquadro dell'utente aperto](./media/terms-of-use/tou14.png)

1. Nella pagina Profilo fare clic su **Verificare le condizioni d'uso**.

    ![Pagina del profilo per un utente che mostra il collegamento Rivedi condizioni per l'utilizzo](./media/terms-of-use/tou13a.png)

1. Da qui è possibile esaminare i criteri delle condizioni per l'utilizzo accettati.

## <a name="edit-terms-of-use-details"></a>Modificare i dettagli delle condizioni per l'utilizzo

È possibile modificare alcuni dettagli dei criteri delle condizioni per l'utilizzo, ma non è possibile modificare un documento esistente. La procedura seguente descrive come modificare i dettagli.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).
1. Selezionare i criteri relativi alle condizioni per l'utilizzo da modificare.
1. Fare clic su **Modifica le condizioni**.
1. Nel riquadro Modifica condizioni per l'utilizzo è possibile modificare quanto segue:
    - **Nome:** nome interno della ToU che non è condiviso con gli utenti finali
    - **Nome visualizzato:** questo è il nome che gli utenti finali possono visualizzare quando visualizzano il tou
    - **Richiedi agli utenti di espandere le condizioni** per l'utilizzo: l'impostazione di questa opzione su **On** forza l'uso finale ad espandere il documento dei criteri delle condizioni per l'utilizzo prima di accettarlo.
    - (Anteprima) È possibile **aggiornare un documento delle condizioni per l'utilizzo** esistente
    - È possibile aggiungere una lingua a una ToU esistente

   Se sono presenti altre impostazioni che si desidera modificare, ad esempio documenti PDF, richiedono agli utenti il consenso su ogni dispositivo, scadono i consensi, la durata prima della riaccezione o i criteri di accesso condizionale, è necessario creare un nuovo criterio ToU.

    ![Modifica che mostra opzioni di lingua diverse ](./media/terms-of-use/edit-terms-use.png)

1. Al termine, fare clic su **Salva** per salvare le modifiche.

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>Aggiornare la versione o il pdf di condizioni per l'utilizzo esistenti

1.  Accedere ad Azure e passare a [Condizioni per l'utilizzo](https://aka.ms/catou)
2.  Selezionare i criteri relativi alle condizioni per l'utilizzo da modificare.
3.  Fare clic su **Modifica le condizioni**.
4.  Per la lingua in cui si vuole aggiornare una nuova versione, fare clic **su Aggiorna** nella colonna azione

    ![Riquadro Modifica condizioni per l'utilizzo con le opzioni relative al nome e all'espansione](./media/terms-of-use/edit-terms-use.png)

5.  Nel riquadro a destra caricare il file PDF per la nuova versione
6.  È disponibile anche un'opzione di attivazione/disattivazione qui Richiedi riaccetta se si vuole richiedere agli utenti di accettare questa nuova versione al successivo accesso.  Se è necessario che gli utenti esercitino nuovamente l'accettazione, al successivo tentativo di accesso alla risorsa definita nei criteri di accesso condizionale verrà richiesto di accettare questa nuova versione. Se non si richiede agli utenti di riaccettarsi, il consenso precedente rimarrà corrente e solo i nuovi utenti che non hanno dato il consenso prima o la cui scadenza del consenso visualizzano la nuova versione.

    ![Opzione Per la ricon accettazione delle condizioni per l'utilizzo evidenziata](./media/terms-of-use/re-accept.png)

7.  Dopo aver caricato il nuovo file PDF e aver deciso di riaccettarlo, fare clic su Aggiungi nella parte inferiore del riquadro.
8.  Verrà ora visualizzata la versione più recente nella colonna Documento.

## <a name="view-previous-versions-of-a-tou"></a>Visualizzare le versioni precedenti di un'toU

1.  Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo https://aka.ms/catou.
2.  Selezionare i criteri relativi alle condizioni per l'utilizzo per i quali si vuole visualizzare la cronologia delle versioni.
3.  Fare clic su **Lingue e cronologia versioni**
4.  Fare clic **su Visualizza versioni precedenti.**

    ![dettagli del documento, incluse le versioni del linguaggio](./media/terms-of-use/document-details.png)

5.  È possibile fare clic sul nome del documento per scaricare tale versione

## <a name="see-who-has-accepted-each-version"></a>Vedere chi ha accettato ogni versione

1.  Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo https://aka.ms/catou.
2.  Per vedere chi ha attualmente accettato le Condizioni per l'uso, fare clic sul numero sotto la **colonna Accettato** per le Condizioni d'uso desiderate.
3.  Per impostazione predefinita, la pagina successiva mostrerà lo stato corrente di ogni utente che ha accettato le toU
4.  Per visualizzare gli eventi di consenso precedenti, è possibile selezionare **Tutti** dall'elenco **a** discesa Stato corrente. È ora possibile visualizzare gli eventi di ogni utente in dettaglio su ogni versione e su cosa è successo.
5.  In alternativa, è possibile selezionare una versione specifica **dall'elenco**  a discesa Versione per vedere chi ha accettato tale versione specifica.


## <a name="add-a-tou-language"></a>Aggiungere una lingua toU

La procedura seguente descrive come aggiungere una lingua toU.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).
1. Selezionare i criteri relativi alle condizioni per l'utilizzo da modificare.
1. Fare clic **su Modifica termini**
1. Fare **clic su Aggiungi** lingua nella parte inferiore della pagina.
1. Nel riquadro Aggiungi la lingua delle condizioni per l'utilizzo caricare il PDF localizzato e selezionare la lingua.

    ![Condizioni per l'utilizzo selezionata e che mostra la scheda Lingue nel riquadro dei dettagli](./media/terms-of-use/select-language.png)

1. Fare clic su **Aggiungi lingua**.
1. Fare clic su **Salva**

1. Fare clic su **Aggiungi** per aggiungere la lingua.

## <a name="per-device-terms-of-use"></a>Condizioni per l'utilizzo per dispositivo

**L'impostazione Richiedi il consenso** degli utenti in ogni dispositivo consente di richiedere agli utenti finali di accettare i criteri relativi alle condizioni per l'utilizzo in ogni dispositivo da cui accedono. L'utente finale dovrà registrare il dispositivo in Azure AD. Quando il dispositivo viene registrato, l'ID dispositivo viene usato per applicare i criteri delle condizioni per l'utilizzo in ogni dispositivo.

Ecco un elenco dei software e delle piattaforme supportate.

> [!div class="mx-tableFixed"]
> |  | iOS | Telefoni | Windows 10 | Altro |
> | --- | --- | --- | --- | --- |
> | **App nativa** | Sì | Sì | Sì |  |
> | **Microsoft Edge** | Sì | Sì | Sì |  |
> | **Internet Explorer** | Sì | Sì | Sì |  |
> | **Chrome (con estensione)** | Sì | Sì | Sì |  |

Le condizioni per l'utilizzo per dispositivo hanno i vincoli seguenti:

- Un dispositivo può essere aggiunto a un solo tenant.
- Un utente deve disporre delle autorizzazioni per aggiungere il dispositivo.
- L'app di registrazione di Intune non è supportata. Assicurarsi che sia escluso dai criteri di accesso condizionale che richiedono i criteri delle condizioni per l'utilizzo.
- Azure AD gli utenti B2B non sono supportati.

Se il dispositivo dell'utente non è stato aggiunto, riceveranno un messaggio che indica che devono aggiungere il dispositivo. L'esperienza sarà dipendente dalla piattaforma e dal software.

### <a name="join-a-windows-10-device"></a>Aggiungere un dispositivo Windows 10

Se un utente sta usando Windows 10 e Microsoft Edge, riceverà un messaggio simile al seguente per [aggiungere il dispositivo](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device).

![Windows 10 e Microsoft Edge: messaggio che indica che il dispositivo deve essere registrato](./media/terms-of-use/per-device-win10-edge.png)

Se si usa Chrome, verrà richiesto di installare l'[estensione account di Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="register-an-ios-device"></a>Registrare un dispositivo iOS

Se un utente usa un dispositivo iOS, gli verrà richiesto di installare [l'app Microsoft Authenticator.](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="register-an-android-device"></a>Registrare un dispositivo Android

Se un utente usa un dispositivo Android, gli verrà richiesto di installare [l'Microsoft Authenticator app](https://play.google.com/store/apps/details?id=com.azure.authenticator).

### <a name="browsers"></a>Browser

Se un utente sta usando un browser che non è supportato, si dovrà usare un browser diverso.

![Messaggio che indica che il dispositivo deve essere registrato, ma il browser non è supportato](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Eliminare le condizioni per l'utilizzo

È possibile eliminare i criteri delle condizioni per l'utilizzo usando la procedura seguente.

1. Accedere ad Azure e passare a **Condizioni per l'utilizzo** all'indirizzo [https://aka.ms/catou](https://aka.ms/catou).
1. Selezionare i criteri relativi alle condizioni per l'utilizzo da rimuovere.
1. Fare clic su **Elimina le condizioni**.
1. Nel messaggio visualizzato in cui viene chiesto se si vuole continuare fare clic su **Sì**.

    ![Messaggio che richiede la conferma dell'eliminazione delle condizioni per l'utilizzo](./media/terms-of-use/delete-tou.png)

   I criteri relativi alle condizioni per l'utilizzo non dovrebbero più essere visualizzati.

## <a name="user-acceptance-record-deletion"></a>Eliminazione di record di accettazione utente

I record di accettazione utente vengono eliminati:

- Quando l'amministratore elimina in modo esplicito le toU. Quando ciò accade, vengono eliminati anche tutti i record di accettazione associati a tale toU specifica.
- Quando il tenant perde la licenza Azure Active Directory Premium licenza.
- Quando il tenant viene eliminato.

## <a name="policy-changes"></a>Modifiche dei criteri

I criteri di accesso condizionale vengono applicati immediatamente. In questo caso l'amministratore inizierà a vedere nuvolette "tristi" o messaggi relativi a problemi di token di Azure AD. Dovrà quindi disconnettersi e accedere nuovamente per soddisfare il nuovo criterio.

> [!IMPORTANT]
> Gli utenti inclusi nell'ambito devono disconnettersi e accedere per soddisfare un nuovo criterio se:
>
> - Criteri di accesso condizionale abilitati in base ai criteri delle condizioni per l'utilizzo
> - o viene creato un secondo criterio delle condizioni per l'utilizzo

## <a name="b2b-guests"></a>Guest B2B

La maggior parte delle organizzazioni ha un processo che consente ai dipendenti di dare il consenso alle condizioni per l'utilizzo e alle informative sulla privacy dell'organizzazione. Ma come è possibile applicare gli stessi consensi per i guest di Azure AD business-to-business (B2B) quando vengono aggiunti tramite SharePoint o i team? Usando l'accesso condizionale e i criteri di condizioni per l'utilizzo, è possibile applicare un criterio direttamente agli utenti guest B2B. Durante il flusso di riscatto dell'invito, all'utente vengono presentati i criteri delle condizioni per l'utilizzo. Questo supporto è attualmente disponibile in versione di anteprima.

Condizioni per l'utilizzo verranno visualizzati solo quando l'utente ha un account guest in Azure AD. SharePoint Online dispone attualmente di un'esperienza di destinatario di condivisione esterna [ad hoc](/sharepoint/what-s-new-in-sharing-in-targeted-release) per condividere un documento o una cartella che non richiede all'utente di avere un account guest. In questo caso, i criteri relativi alle condizioni per l'utilizzo non vengono visualizzati.

![Riquadro Utenti e gruppi : scheda Includi con l'opzione Tutti gli utenti guest selezionata](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>Supporto per le app cloud

Condizioni per l'utilizzo possono essere usati per app cloud diverse, ad esempio Azure Information Protection e Microsoft Intune. Questo supporto è attualmente disponibile in versione di anteprima.

### <a name="azure-information-protection"></a>Azure Information Protection

È possibile configurare criteri di accesso condizionale per l'app Azure Information Protection e richiedere criteri di condizioni per l'utilizzo quando un utente accede a un documento protetto. In questo modo verranno attivati i criteri relativi alle condizioni per l'utilizzo prima che un utente accedono a un documento protetto per la prima volta.

![Riquadro App cloud con l Microsoft Azure Information Protection app selezionata](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Registrazione di Microsoft Intune

È possibile configurare criteri di accesso condizionale per l'app Microsoft Intune Enrollment e richiedere criteri di condizioni per l'utilizzo prima della registrazione di un dispositivo in Intune. Per altre informazioni, vedere il Leggi il [post di blog sulla scelta della soluzione di Condizioni di utilizzo più adatta per l'organizzazione](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

![Riquadro App cloud con l Microsoft Intune app selezionata](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> L'app di registrazione di Intune non è supportata [per le condizioni per l'utilizzo per dispositivo.](#per-device-terms-of-use)

## <a name="frequently-asked-questions"></a>Domande frequenti

**D: Non è possibile accedere con PowerShell quando sono abilitate le condizioni per l'utilizzo.**<br />
A: i Condizioni per l'utilizzo possono essere accettati solo durante l'autenticazione interattiva.

**D: Ricerca per categorie quando/se un utente ha accettato le condizioni per l'utilizzo?**<br />
R: Nel pannello Condizioni per l'utilizzo fare clic sul numero sotto **Accettato**. È anche possibile visualizzare o cercare l'attività accettata nei log di controllo di Azure AD. Per altre informazioni, vedere Visualizzare il report degli utenti che hanno accettato e rifiutato e [Visualizzare i log di controllo di Azure AD](#view-azure-ad-audit-logs).

**D: per quanto tempo sono archiviate le informazioni?**<br />
A: L'utente conta nei report delle condizioni per l'utilizzo e chi ha accettato/rifiutato viene archiviato per la durata delle condizioni per l'utilizzo. I log di controllo di Azure AD vengono archiviati per 30 giorni.

**D: Perché viene visualizzato un numero diverso di consensi nei report delle condizioni per l'utilizzo rispetto Azure AD di controllo?**<br />
A: i report delle condizioni per l'utilizzo vengono archiviati per la durata dei criteri relativi alle condizioni per l'utilizzo, Azure AD log di controllo vengono archiviati per 30 giorni. Inoltre, il report delle condizioni per l'utilizzo visualizza solo lo stato di consenso corrente degli utenti. Ad esempio, se un utente rifiuta e quindi accetta, il report delle condizioni per l'utilizzo mostrerà solo l'accettazione dell'utente. Se è necessario visualizzare la cronologia, è possibile usare i log di controllo di Azure AD.

**D: Se si modificano i dettagli per i criteri relativi alle condizioni per l'utilizzo, è necessario che gli utenti accettino di nuovo?**<br />
A: No, se un amministratore modifica i dettagli per i criteri delle condizioni per l'utilizzo (nome, nome visualizzato, richiedere agli utenti di espandere o aggiungere una lingua), non richiede agli utenti di riaccept i nuovi termini.

**D: È possibile aggiornare un documento di criteri delle condizioni per l'utilizzo esistente?**<br />
A: Attualmente non è possibile aggiornare un documento di criteri delle condizioni per l'utilizzo esistente. Per modificare un documento dei criteri relativi alle condizioni per l'utilizzo, è necessario creare una nuova istanza dei criteri delle condizioni per l'utilizzo.

**D: Se i collegamenti ipertestuali sono nel documento PDF dei criteri di utilizzo, gli utenti finali potranno fare clic su di essi?**<br />
A: Sì, gli utenti finali possono selezionare collegamenti ipertestuali a pagine aggiuntive, ma i collegamenti alle sezioni all'interno del documento non sono supportati. Inoltre, i collegamenti ipertestuali in termini di utilizzo dei FILE PDF dei criteri non funzionano quando si accede Azure AD portale MyApps/MyAccount.

**D: I criteri relativi alle condizioni per l'utilizzo possono supportare più lingue?**<br />
R: Sì. Attualmente sono disponibili 108 lingue diverse che un amministratore può configurare per un singolo criterio delle condizioni per l'utilizzo. Un amministratore può caricare più documenti PDF e contrassegnare i documenti con una lingua corrispondente (fino a 108). Quando gli utenti finali accedono, vengono esaminate le preferenze della lingua del browser e viene visualizzato il documento corrispondente. Se non viene trovata alcuna corrispondenza, verrà visualizzato il documento predefinito, ovvero il primo documento che viene caricato.

**D: Quando vengono attivati i criteri relativi alle condizioni per l'utilizzo?**<br />
A: I criteri relativi alle condizioni per l'utilizzo vengono attivati durante l'esperienza di accesso.

**D: A quali applicazioni è possibile scegliere come destinazione i criteri per le condizioni per l'utilizzo?**<br />
A: È possibile creare criteri di accesso condizionale nelle applicazioni aziendali usando l'autenticazione moderna. Per altre informazioni, vedere le [applicazioni aziendali](./../manage-apps/view-applications-portal.md).

**D: È possibile aggiungere più criteri per le condizioni per l'utilizzo a un determinato utente o app?**<br />
A: Sì, creando più criteri di accesso condizionale per tali gruppi o applicazioni. Se un utente rientra nell'ambito di più criteri per le condizioni per l'utilizzo, accetta un criterio delle condizioni per l'utilizzo alla volta.

**D: Cosa accade se un utente rifiuta i criteri relativi alle condizioni per l'utilizzo?**<br />
R: ne viene bloccato l'accesso all'applicazione. L'utente deve accedere nuovamente e accettare le condizioni.

**D: È possibile non accettare criteri relativi alle condizioni per l'utilizzo accettati in precedenza?**<br />
A: È possibile [esaminare i criteri delle condizioni](#how-users-can-review-their-terms-of-use)per l'utilizzo accettati in precedenza, ma attualmente non esiste un modo per non accettare.

**D: Cosa succede se si usano anche termini e condizioni di Intune?**<br />
A: Se sono state configurate entrambe Azure AD condizioni per l'utilizzo e [Intune,](/intune/terms-and-conditions-create)l'utente dovrà accettare entrambe le condizioni. Per altre informazioni, vedere il [post di blog sulla scelta della soluzione di Condizioni di utilizzo più adatta per l'organizzazione](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409).

**D: Quali endpoint usano le condizioni per l'utilizzo del servizio per l'autenticazione?**<br />
A: Condizioni per l'utilizzo usa gli endpoint seguenti per l'autenticazione: https://tokenprovider.termsofuse.identitygovernance.azure.com e https://account.activedirectory.windowsazure.com . Se l'organizzazione ha un elenco di URL consentiti per la registrazione, è necessario aggiungere questi endpoint all'elenco consenti, insieme agli endpoint Azure AD per l'accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Avvio rapido: Richiedere l'accettazione di condizioni per l'utilizzo prima dell'accesso alle app cloud](require-tou.md)
