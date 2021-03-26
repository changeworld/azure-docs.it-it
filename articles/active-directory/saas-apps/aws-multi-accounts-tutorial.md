---
title: 'Esercitazione: integrazione di Azure Active Directory con Amazon Web Services per la connessione di più account | Microsoft Docs'
description: Informazioni su come configurare Single Sign-On tra Azure AD e Amazon Web Services (esercitazione legacy).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 0ddcb239ba106bcdc2f0a29d68eb395876fadc06
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "100384113"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>Esercitazione: integrazione di Azure Active Directory con Amazon Web Services

Questa esercitazione illustra come integrare Azure Active Directory (Azure AD) con Amazon Web Services (AWS) (esercitazione legacy).

Questa integrazione offre i vantaggi seguenti:

- È possibile controllare in Azure AD chi ha accesso a AWS.
- È possibile abilitare gli utenti per l'accesso automatico a AWS usando Single Sign-On (SSO) con i propri account di Azure AD.
- È possibile gestire gli account da una posizione centrale, il portale di Azure.

![Diagramma dell'integrazione Azure AD con AWS.](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Si consiglia di *non* connettere un'app AWS a tutti gli account AWS. È invece consigliabile usare [Azure ad integrazione SSO con AWS](./amazon-web-service-tutorial.md) per configurare più istanze dell'account AWS in più istanze delle app aws in Azure ad. 

Si consiglia di *non* connettere un'app AWS a tutti gli account AWS, per i motivi seguenti:

* Usare questo approccio solo se si dispone di un numero ridotto di account e ruoli AWS, perché questo modello non è scalabile come il numero di account AWS e i ruoli in essi contenuti aumentano. L'approccio non usa la funzionalità di importazione dei ruoli AWS con Azure AD il provisioning degli utenti, quindi è necessario aggiungere, aggiornare o eliminare manualmente i ruoli. 

* È necessario usare l'approccio Microsoft Graph Explorer per applicare la patch a tutti i ruoli dell'app. Non è consigliabile usare l'approccio del file manifesto.

* I clienti segnalano che dopo aver aggiunto ~ 1.200 ruoli app per una singola app AWS, qualsiasi altra operazione nell'app inizia a generare gli errori correlati alla dimensione. È previsto un limite di dimensioni rigide per l'oggetto applicazione.

* È necessario aggiornare manualmente i ruoli Man mano che vengono aggiunti in uno degli account. Si tratta purtroppo di un approccio di *sostituzione* , non di un approccio di *Accodamento* . Inoltre, se i numeri di account aumentano, diventa una relazione *n* &times; *n* con account e ruoli.

* Tutti gli account AWS utilizzano lo stesso file XML di metadati della Federazione. Al momento del rollover del certificato, l'aggiornamento del certificato in tutti gli account AWS allo stesso tempo può essere un esercizio massiccio.

## <a name="prerequisites"></a>Prerequisiti

Per configurare Azure AD l'integrazione con AWS, sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure AD. Se non si ha una sottoscrizione di Azure AD, è possibile ottenere una [versione di valutazione di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Sottoscrizione di AWS abilitata per l'accesso SSO.

> [!NOTE]
> Non è consigliabile testare i passaggi di questa esercitazione in un ambiente di produzione, a meno che non sia necessario.

## <a name="scenario-description"></a>Descrizione dello scenario

In questa esercitazione vengono eseguiti la configurazione e il test dell'accesso Single Sign-On di Azure AD in un ambiente di test.

AWS supporta l'accesso SSO avviato da SP e da IDP.

## <a name="add-aws-from-the-gallery"></a>Aggiungere AWS dalla raccolta

Per configurare l'integrazione di AWS in Azure AD, è necessario aggiungere AWS dalla raccolta al proprio elenco di App Software as a Service gestite (SaaS).

1. Accedere al portale di Azure con un account aziendale o dell'istituto di istruzione oppure con un account Microsoft personale.
1. Nel riquadro sinistro selezionare il servizio Azure AD che si desidera utilizzare.
1. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.
1. Per aggiungere un'applicazione, selezionare **Nuova applicazione**.
1. Nella sezione **Aggiungi dalla raccolta** Digitare **Amazon Web Services** nella casella di ricerca.
1. Nell'elenco dei risultati selezionare **Amazon Web Services** e quindi aggiungere l'app. Entro pochi secondi l'app viene aggiunta al tenant.

1. Passare al riquadro **Proprietà** , quindi copiare il valore visualizzato nella casella **ID oggetto** .

    ![Screenshot della casella ID oggetto nel riquadro proprietà.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Configurare e testare l'accesso SSO di Azure AD

In questa sezione viene configurato e testato Azure AD Single Sign-On con AWS in base a un utente test di nome "Britta Simon".

Per il funzionamento di Single Sign-On, Azure AD necessario conoscere qual è l'utente di AWS che corrisponde al Azure AD utente. In altre parole, deve essere stabilita una relazione di collegamento tra l'utente Azure AD e lo stesso utente in AWS.

Per stabilire la relazione di collegamento, in AWS assegnare il valore del **nome utente** in Azure ad come valore di AWS **nomeutente** .

Per configurare e testare Azure AD Single Sign-On con AWS, seguire questa procedura:

1. **[Configurare l'accesso Single Sign-On di Azure AD](#configure-azure-ad-sso)** per consentire agli utenti di usare questa funzionalità.
1. **[Configurare AWS SSO](#configure-aws-sso)** per configurare le impostazioni SSO sul lato applicazione.
1. **[Testare l'accesso SSO](#test-sso)** per verificare se la configurazione funziona.

### <a name="configure-azure-ad-sso"></a>Configurare l'accesso SSO di Azure AD

In questa sezione viene abilitato Azure AD SSO nell'portale di Azure e viene configurato l'accesso SSO nell'applicazione AWS eseguendo le operazioni seguenti:

1. Nel portale di Azure, nel riquadro sinistro della pagina di integrazione dell'applicazione **Amazon Web Services (AWS)** , selezionare **Single Sign-on**.

    ![Screenshot del comando "Single Sign-on".](common/select-sso.png)

1. Nel riquadro **Selezionare un metodo di accesso Single Sign-On** selezionare la modalità **SAML/WS-Fed** per abilitare l'accesso Single Sign-On.

    ![Screenshot del riquadro "selezionare un metodo di Single Sign-On".](common/select-saml-option.png)

1. Nel riquadro **imposta Sign-On singolo con SAML** selezionare il pulsante  **modifica** (icona a matita).

    ![Screenshot del pulsante modifica nel riquadro "Configura Sign-On singolo con SAML".](common/edit-urls.png)

1. Viene visualizzato il riquadro **configurazione SAML di base** . Ignorare questa sezione, perché l'app è preintegrata con Azure. Selezionare **Salva**.

   L'applicazione AWS prevede che le asserzioni SAML abbiano un formato specifico. È possibile gestire i valori di questi attributi dalla sezione **attributi utente & attestazioni** nella pagina di **integrazione dell'applicazione** . 
   
1. Nella pagina **Configura singolo Sign-On con SAML** selezionare il pulsante **modifica** .

    ![Screenshot del pulsante modifica nel riquadro "attributi utente".](common/edit-attribute.png)

1. Nella sezione **attestazioni utente** del riquadro **attributi utente** configurare l'attributo del token SAML usando i valori riportati nella tabella seguente:

    | Nome  | Attributo di origine  | Spazio dei nomi |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | `https://aws.amazon.com/SAML/Attributes` |
    | Ruolo | user.assignedroles | `https://aws.amazon.com/SAML/Attributes`|
    | SessionDuration | "specificare un valore compreso tra 900 secondi (15 minuti) e 43200 secondi (12 ore)" |  `https://aws.amazon.com/SAML/Attributes` |
  
   a. Selezionare **Aggiungi nuova attestazione** , quindi nel riquadro **Gestisci attestazioni utente** eseguire le operazioni seguenti:

   ![Screenshot dei pulsanti "Aggiungi nuova attestazione" e "Salva" nel riquadro "attestazioni utente".](common/new-save-attribute.png)

   ![Screenshot del riquadro "Gestisci attestazioni utente".](common/new-attribute-details.png)

   b. Nella casella **nome** immettere il nome dell'attributo.  

   c. Nella casella **Namespace (spazio dei nomi** ) immettere il valore dello spazio dei nomi.

   d. Per **Origine**, selezionare **Attributo**.

   e. Nell'elenco a discesa **attributo di origine** selezionare l'attributo.

   f. Selezionare **OK** e quindi fare clic su **Salva**.

   >[!NOTE]
   >Per altre informazioni sui ruoli in Azure AD, vedere [aggiungere i ruoli dell'app all'applicazione e riceverli nel token](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. Nella sezione **certificato di firma SAML** della pagina **Configura singolo Sign-On con SAML** selezionare **Scarica** per scaricare il file XML dei metadati di Federazione e quindi salvarlo nel computer.

   ![Screenshot del collegamento per il download del file XML dei metadati di Federazione nel riquadro "certificato di firma SAML".](common/metadataxml.png)

### <a name="configure-aws-sso"></a>Configurare AWS SSO

1. In una nuova finestra del browser accedere al sito aziendale di AWS come amministratore.

1. Selezionare l'icona **Home di AWS** .

   ![Screenshot dell'icona "AWS Home".][11]

1. Nel riquadro **Servizi AWS** , in **sicurezza, Identity & Compliance**, selezionare **IAM (Identity & Access Management)**.

    ![Screenshot del collegamento "gestione di identità e accessi" nel riquadro "Servizi AWS".][12]

1. Nel riquadro sinistro selezionare provider di **identità** e quindi fare clic su **crea provider**.

    ![Screenshot del pulsante "crea provider".][13]

1. Nel riquadro **Configura provider** eseguire le operazioni seguenti:

    ![Screenshot del riquadro "Configura provider".][14]

    a. Nell'elenco a discesa **tipo di provider** selezionare **SAML**.

    b. Nella casella **Nome provider** immettere un nome di provider, ad esempio. *WAAD*.

    c. Accanto alla casella **documento metadati** selezionare **Choose file (Scegli file** ) per caricare il file XML dei metadati di Federazione scaricato nel portale di Azure.

    d. Selezionare **Next Step** (Passaggio successivo).

1. Nel riquadro **Verifica informazioni provider** selezionare **Crea**.

    ![Screenshot del riquadro "verifica informazioni provider".][15]

1. Nel riquadro sinistro selezionare **ruoli** e quindi fare clic su **Crea ruolo**.

    ![Screenshot del pulsante "Crea ruolo" nel riquadro ruoli.][16]

    > [!NOTE]
    > La lunghezza combinata del nome del ruolo Amazon Resource Name (ARN) e del provider SAML per un ruolo che viene importato deve essere di 240 o un numero inferiore di caratteri.

1. Nella pagina **Crea ruolo** eseguire le operazioni seguenti:  

    ![Screenshot del pulsante dell'entità attendibile "SAML 2,0 Federation" nella pagina "Crea ruolo".][19]

    a. In **Select type of trusted entity** (Seleziona il tipo di entità attendibile) selezionare **SAML 2.0 federation** (Federazione SAML 2.0).

    b. In **scegliere un provider saml 2,0** selezionare il provider SAML creato in precedenza (ad esempio, *WAAD*)

    c. Selezionare **Allow programmatic and AWS Management Console access** (Consenti accesso a livello di codice e tramite AWS Management Console).

    d. Selezionare **Avanti: Permissions** (Avanti: Autorizzazioni).

1. Nella casella di ricerca immettere **accesso amministratore**, selezionare la casella di controllo **AdministratorAccess** e quindi fare clic su **Avanti: Tag**.

    ![Screenshot dell'elenco "nome criterio" con i criteri AdministratorAccess selezionati.](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. Nel riquadro **Aggiungi tag (facoltativo)** eseguire le operazioni seguenti:

    ![Screenshot del riquadro "Aggiungi tag (facoltativo)".](./media/aws-multi-accounts-tutorial/config2.png)

    a. Nella casella **chiave** immettere il nome della chiave, ad esempio *Azureadtest*.

    b. Nella casella **valore (facoltativo)** immettere il valore della chiave nel formato seguente: `<accountname-aws-admin>` . Il nome dell'account deve essere composto da lettere minuscole.

    c. Selezionare **Avanti: Review** (Avanti: Rivedi).

1. Nel riquadro **Verifica** eseguire le operazioni seguenti:

    ![Screenshot del riquadro Revisione con le caselle "nome ruolo" e "Descrizione ruolo" evidenziate.][34]

    a. Nella casella **nome ruolo** immettere il valore nel formato seguente: `<accountname-aws-admin>` .

    b. Nella casella **Descrizione ruolo** immettere il valore usato per il nome del ruolo.

    c. Selezionare **Create role** (Crea ruolo).

    d. Creare tutti i ruoli necessari ed eseguirne il mapping al provider di identità.

    > [!NOTE]
    > Analogamente, è possibile creare altri ruoli, ad esempio *AccountName-Finance-admin*, *AccountName-Read-Only-User*, *AccountName-DevOps-utente* o *AccountName-TPM-utente*, ognuno dei quali è associato a un criterio diverso. È possibile modificare questi criteri del ruolo in un secondo momento, in base ai requisiti per ogni account AWS. È consigliabile rispettare gli stessi criteri per ogni ruolo negli account AWS.

1. Assicurarsi di annotare l'ID account per l'account AWS dal riquadro delle proprietà Amazon Elastic Compute Cloud (Amazon EC2) o dal dashboard IAM, come illustrato nello screenshot seguente:

    ![Screenshot che mostra la posizione in cui viene visualizzato l'ID dell'account nel riquadro "Gestione identità e accesso".](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Accedere al portale di Azure, quindi andare a **gruppi**.

1. Creare nuovi gruppi con lo stesso nome dei ruoli IAM creati in precedenza, quindi prendere nota del valore nella casella **ID oggetto** di ognuno di questi nuovi gruppi.

    ![Screenshot dei dettagli dell'account per un nuovo gruppo.](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Disconnettersi dall'account AWS corrente, quindi accedere a un altro account in cui si vuole configurare SSO con Azure AD.

1. Dopo aver creato tutti i ruoli negli account, vengono visualizzati nell'elenco dei **ruoli** per tali account.

    ![Screenshot dell'elenco dei ruoli, che mostra il nome, la descrizione e le entità attendibili di ogni ruolo.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

Sarà quindi necessario acquisire tutti i ruoli ARNs e le entità attendibili per tutti i ruoli in tutti gli account. Sarà necessario eseguirne il mapping manualmente con l'applicazione Azure AD. A tale scopo, procedere nel seguente modo:

1. Selezionare ogni ruolo per copiare il ruolo ARN e i valori dell'entità attendibile. Saranno necessari per tutti i ruoli che verranno creati in Azure AD.

    ![Screenshot del riquadro Riepilogo per il ruolo ARNs e le entità attendibili.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Ripetere il passaggio precedente per tutti i ruoli in tutti gli account, quindi archiviarli in un file di testo nel formato seguente: `<Role ARN>,<Trusted entities>` .

1. Aprire [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer), quindi eseguire le operazioni seguenti:

    a. Accedere al sito di Microsoft Graph Explorer con le credenziali di amministratore globale o di coamministratore per il tenant.

    b. Sono necessarie autorizzazioni sufficienti per creare i ruoli. Selezionare **modifica autorizzazioni**.

      ![Screenshot del collegamento "modifica autorizzazioni" nel riquadro di autenticazione di Microsoft Graph Explorer.](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Nell'elenco delle autorizzazioni, se non si dispone già delle autorizzazioni mostrate nella schermata seguente, selezionare ciascuna di esse, quindi selezionare **modifica autorizzazioni**. 

      ![Screenshot dell'elenco di autorizzazioni di Microsoft Graph Explorer con le autorizzazioni appropriate evidenziate.](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Accedere di nuovo a Graph Explorer e accettare le condizioni di utilizzo del sito. 

    e. Nella parte superiore del riquadro selezionare **Get** per il metodo, selezionare **beta** per la versione e quindi nella casella query immettere uno dei valori seguenti: 
    
    * Per recuperare tutte le entità servizio dal tenant, usare `https://graph.microsoft.com/beta/servicePrincipals` . 
    * Se si usano più directory, usare `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` , che contiene il dominio primario.

    ![Screenshot del riquadro "corpo della richiesta" della query Esplora Microsoft Graph.](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Dall'elenco delle entità servizio, ottenere quello che è necessario modificare. 
    
    È anche possibile eseguire una ricerca nell'applicazione per tutte le entità servizio elencate selezionando CTRL + F. Per ottenere un'entità servizio specifica, includere nella query l'ID oggetto entità servizio copiato in precedenza dal riquadro Proprietà Azure AD, come illustrato di seguito:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

      ![Screenshot che mostra una query dell'entità servizio che include l'ID oggetto.](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Estrarre la proprietà appRoles dall'oggetto entità servizio.

    ![Screenshot del codice per l'estrazione della proprietà appRoles dall'oggetto entità servizio.](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. È ora necessario generare nuovi ruoli per l'applicazione. 

    i. Il codice JSON seguente è un esempio di oggetto appRoles. Creare un oggetto simile per aggiungere i ruoli desiderati per l'applicazione.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > È possibile aggiungere nuovi ruoli solo dopo aver aggiunto *msiam_access* per l'operazione patch. È anche possibile aggiungere tutti i ruoli desiderati, a seconda delle esigenze dell'organizzazione. Azure AD invia il *valore* di questi ruoli come valore attestazione nella risposta SAML.

    j. In Microsoft Graph Explorer modificare il metodo da **Get** a **patch**. Applicare la patch all'oggetto entità servizio con i ruoli desiderati aggiornando la proprietà appRoles, come quella illustrata nell'esempio precedente. Selezionare **Esegui query** per eseguire l'operazione patch. Un messaggio di operazione completata conferma la creazione del ruolo per l'applicazione AWS.

      ![Screenshot del riquadro Microsoft Graph Explorer, con il metodo modificato in PATCH.](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Dopo che l'entità servizio è stata patchata con più ruoli, è possibile assegnare utenti e gruppi ai rispettivi ruoli. Questa operazione viene eseguita nel portale di Azure passando all'applicazione AWS e quindi selezionando la scheda **utenti e gruppi** nella parte superiore.

1. Si consiglia di creare un nuovo gruppo per ogni ruolo AWS, in modo da poter assegnare quel particolare ruolo al gruppo. Questo mapping uno-a-uno indica che un gruppo è assegnato a un ruolo. È quindi possibile aggiungere i membri che appartengono a tale gruppo.

1. Dopo aver creato i gruppi, selezionare il gruppo e assegnarlo all'applicazione.

    ![Screenshot del riquadro "utenti e gruppi".](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > I gruppi annidati non sono supportati quando si assegnano gruppi.

1. Per assegnare il ruolo al gruppo, selezionare il ruolo e quindi selezionare **assegna**.

    ![Screenshot del riquadro "Aggiungi assegnazione".](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Dopo aver assegnato i ruoli, è possibile visualizzarli aggiornando la sessione di portale di Azure.

### <a name="test-sso"></a>Testare l'accesso SSO

In questa sezione viene testato il Azure AD Single Sign-On configurazione usando le app personali di Microsoft.

Quando si seleziona il riquadro **AWS** in app personali, viene visualizzata la pagina applicazione AWS con un'opzione per selezionare il ruolo.

![Screenshot della pagina di AWS per il test di SSO.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

È anche possibile verificare la risposta SAML per visualizzare i ruoli passati come attestazioni.

![Screenshot della risposta SAML.](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Per altre informazioni sulle app personali, vedere [accedere e avviare app dal portale app personali](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato AWS, è possibile applicare il controllo della sessione, che protegge i exfiltration e l'infiltrazione dei dati sensibili dell'organizzazione in tempo reale. Il controllo sessione costituisce un'estensione dell'accesso condizionale. Per altre informazioni, vedere [Informazioni su come applicare il controllo sessione con Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
