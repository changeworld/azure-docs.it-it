---
title: Controllo del codice sorgente in Synapse Studio
description: Informazioni su come configurare il controllo del codice sorgente in Azure Synapse Studio
author: liud
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 8f1b459c2644472463004c231f5827ff653d2da1
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567843"
---
# <a name="source-control-in-azure-synapse-studio"></a>Controllo del codice sorgente in Azure Synapse Studio

Per impostazione predefinita, Azure Synapse Studio crea direttamente nel servizio Synapse. Se è necessaria la collaborazione con Git per il controllo del codice sorgente, Synapse Studio consente di associare l'area di lavoro a un repository Git, Azure DevOps o GitHub. 

Questo articolo descrive come configurare e usare un'area di lavoro synapse con il repository Git abilitato. Vengono inoltre evidenziate alcune procedure consigliate e una guida alla risoluzione dei problemi.

> [!NOTE]
> Azure Synapse'integrazione git di Studio non è disponibile in Azure per enti pubblici Cloud.

## <a name="configure-git-repository-in-synapse-studio"></a>Configurare il repository Git in Synapse Studio 

Dopo aver avviato il Synapse Studio, è possibile configurare un repository Git nell'area di lavoro. Un Synapse Studio area di lavoro può essere associata a un solo repository Git alla volta. 

### <a name="configuration-method-1-global-bar"></a>Metodo di configurazione 1: barra globale

Nella barra Synapse Studio globale selezionare il menu a discesa **Synapse Live** e quindi selezionare **Set up code repository (Configura repository di codice).**

![Configurare le impostazioni del repository di codice dalla creazione](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Metodo di configurazione 2: Gestire l'hub

Passare all'hub Gestisci di Synapse Studio. Selezionare **Configurazione Git** nella sezione Controllo **del** codice sorgente. Se non è connesso alcun repository, fare clic su **Configura**.

![Configurare le impostazioni del repository di codice dall'hub di gestione](media/configure-repo-2.png)

> [!NOTE]
> Gli utenti concessi come collaboratore dell'area di lavoro, proprietario o ruoli di livello superiore possono configurare, modificare l'impostazione e disconnettere il repository Git in Azure Synapse Studio 

È possibile connettersi al Azure DevOps GitHub o al repository GitHub nell'area di lavoro.

## <a name="connect-with-azure-devops-git"></a>Connettersi con Azure DevOps Git 

È possibile associare un'area di lavoro Synapse a un repository Azure DevOps per il controllo del codice sorgente, la collaborazione, il controllo delle versioni e così via. Se non si ha un repository Azure DevOps, seguire [queste istruzioni](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) per creare le risorse del repository.

### <a name="azure-devops-git-repository-settings"></a>Azure DevOps repository Git

Quando ci si connette al repository Git, selezionare prima di tutto il tipo di repository Azure DevOps Git, quindi selezionare un tenant Azure AD dall'elenco Azure AD elenco a discesa e fare clic su **Continua**.

![Configurare le impostazioni del repository di codice](media/connect-with-azuredevops-repo-selected.png)

Il riquadro di configurazione mostra le impostazioni git Azure DevOps seguenti:

| Impostazione | Descrizione | valore |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos.<br/> | Azure DevOps Git o GitHub |
| **Azure Active Directory** | Nome del tenant di Azure AD. | `<your tenant name>` |
| **Azure DevOps account** | Il nome dell'organizzazione di Azure Repos. È possibile individuare il nome dell'organizzazione Azure Repos all'indirizzo `https://{organization name}.visualstudio.com`. È possibile [accedere all'organizzazione Azure Repos](https://www.visualstudio.com/team-services/git/) e quindi accedere al proprio profilo di Visual Studio e visualizzare i repository e i progetti. | `<your organization name>` |
| **Nome progetto** | Il nome del progetto Azure Repos. È possibile trovare il nome del progetto Azure Repos all'indirizzo `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **Nome repository** | Il nome del repository di codice Azure Repos. I progetti Azure Repos contengono repository GIT per gestire il codice sorgente man mano che aumentano le dimensioni del progetto. È possibile creare un nuovo repository o usare un repository già presente nel progetto. | `<your Azure Repos code repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di Azure Repos usato per la pubblicazione. L'impostazione predefinita è `master`. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. È possibile selezionare rami esistenti o crearne di nuovi | `<your collaboration branch name>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di Azure Repos. | `<your root folder name>` |
| **Importare le risorse esistenti nel repository** | Specifica se importare le risorse esistenti dal Synapse Studio in un repository Azure Repos Git. Selezionare la casella per importare le risorse dell'area di lavoro (ad eccezione dei pool) nel repository Git associato in formato JSON. Questa azione esporta ogni risorsa singolarmente. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionato (impostazione predefinita) |
| **Importare una risorsa in questo ramo** | Selezionare il ramo in cui vengono importate le risorse (script SQL, notebook, definizione del processo Spark, set di dati, flusso di dati e così via). 

È anche possibile usare il collegamento al repository per puntare rapidamente al repository Git con cui si vuole connettersi. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Usare un tenant di Azure Active Directory diverso

Il repository Azure Repos Git può essere posizionato in un diverso tenant di Azure Active Directory. Per specificare un tenant di Azure AD diverso, è necessario avere autorizzazioni di amministratore per la sottoscrizione di Azure usata. Per altre informazioni, vedere Modificare [l'amministratore della sottoscrizione](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)

> [!IMPORTANT]
> Per connettersi a un Azure Active Directory, l'utente connesso deve far parte di tale active directory. 

### <a name="use-your-personal-microsoft-account"></a>Usare l'account Microsoft personale

Per usare un account Microsoft personale per l'integrazione con Git, è possibile collegare il repository di Azure personale con l'istanza di Active Directory dell'organizzazione.

1. Aggiungere come guest l'account Microsoft personale all'istanza di Active Directory dell'organizzazione. Per altre informazioni, vedere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](../../active-directory/external-identities/add-users-administrator.md).

2. Accedere al portale di Azure con l'account Microsoft personale. Passare quindi all'istanza di Active Directory dell'organizzazione.

3. Passare alla sezione Azure DevOps, in cui è possibile visualizzare il repository personale. Selezionare il repository e connettersi ad Active Directory.

Dopo questi passaggi di configurazione, il repository personale è disponibile quando si configura l'integrazione di Git nel Synapse Studio.

Per altre informazioni sulla connessione Azure Repos ad Active Directory dell'organizzazione, vedere Connettere l'organizzazione [a Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="connect-with-github"></a>Connettersi con GitHub 

 È possibile associare un'area di lavoro a un repository GitHub per il controllo del codice sorgente, la collaborazione e il controllo delle versioni. Se non si ha un account o un repository GitHub, seguire [queste istruzioni](https://github.com/join) per creare le risorse.

L'integrazione di GitHub Synapse Studio supporta sia GitHub pubblico (ovvero ) che [https://github.com](https://github.com) GitHub Enterprise. È possibile usare repository GitHub pubblici e privati, purché si abbia l'autorizzazione di lettura e scrittura per il repository in GitHub.

### <a name="github-settings"></a>Impostazioni di GitHub

Quando ci si connette al repository Git, selezionare prima di tutto il tipo di repository GitHub e quindi specificare l'account GitHub o l'URL del server GitHub Enterprise se si usa GitHub Enterprise Server e fare clic su **Continua.**

![Impostazioni del repository GitHub](media/connect-with-github-repo-1.png)

Il riquadro di configurazione visualizza le impostazioni seguenti del repository GitHub:

| **Impostazione** | **Descrizione**  | **Valore**  |
|:--- |:--- |:--- |
| **Tipo di repository** | Tipo del repository di codice Azure Repos. | GitHub |
| **Use GitHub Enterprise** (Usa GitHub Enterprise) | Casella di controllo per selezionare GitHub Enterprise | non selezionato (impostazione predefinita) |
| **GitHub Enterprise URL** (URL GitHub Enterprise) | URL radice di GitHub Enterprise (deve essere HTTPS per il server GitHub Enterprise locale). Ad esempio: `https://github.mydomain.com`. Obbligatorio solo se si seleziona **Usa GitHub Enterprise** | `<your GitHub enterprise url>` |                                                           
| **Account GitHub** | Nome dell'account GitHub. Questo nome è indicato nella pagina https:\//github.com/{nome account}/{nome repository}. Se si passa a questa pagina, viene chiesto di immettere le credenziali OAuth di GitHub per l'account GitHub. | `<your GitHub account name>` |
| **Nome repository**  | Nome del repository del codice GitHub. Gli account GitHub contengono repository Git per la gestione del codice sorgente. È possibile creare un nuovo repository o usarne uno esistente già presente nell'account. | `<your repository name>` |
| **Ramo di collaborazione** | Ramo di collaborazione di GitHub usato per la pubblicazione. Per impostazione predefinita, si tratta del ramo master. Modificare questa impostazione se si vuole pubblicare risorse da un altro ramo. | `<your collaboration branch>` |
| **Cartella radice** | Cartella radice nel ramo di collaborazione di GitHub. |`<your root folder name>` |
| **Importare le risorse esistenti nel repository** | Specifica se importare le risorse esistenti dal Synapse Studio in un repository Git. Selezionare la casella per importare le risorse dell'area di lavoro (ad eccezione dei pool) nel repository Git associato in formato JSON. Questa azione esporta ogni risorsa singolarmente. Quando questa casella non è selezionata, le risorse esistenti non vengono importate. | Selezionata (impostazione predefinita) |
| **Importare la risorsa in questo ramo** | Selezionare il ramo in cui vengono importate le risorse (script SQL, notebook, definizione del processo Spark, set di dati, flusso di dati e così via). 

### <a name="github-organizations"></a>Organizzazioni GitHub

Per connettersi a un'organizzazione GitHub, è necessario che l'organizzazione concedi l'autorizzazione Synapse Studio. Un utente con autorizzazioni di amministratore per l'organizzazione deve eseguire la procedura seguente.

#### <a name="connecting-to-github-for-the-first-time"></a>Prima connessione a GitHub

Se ci si connette a GitHub da Synapse Studio per la prima volta, seguire questa procedura per connettersi a un'organizzazione GitHub.

1. Nel riquadro Configurazione Git immettere il nome dell'organizzazione nel *campo Account GitHub.* Verrà visualizzata una richiesta di accesso a GitHub. 

1. Accedere usando le credenziali utente.

1. Verrà chiesto di autorizzare Synapse come applicazione denominata *Azure Synapse*. In questa schermata verrà visualizzata un'opzione per concedere a Synapse l'autorizzazione per accedere all'organizzazione. Se non viene visualizzata l'opzione per concedere l'autorizzazione, chiedere a un amministratore di concedere manualmente l'autorizzazione tramite GitHub.

Dopo aver seguito questa procedura, l'area di lavoro sarà in grado di connettersi ai repository pubblici e privati all'interno dell'organizzazione. Se non è possibile connettersi, provare a cancellare la cache del browser e riprovare.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Già connesso a GitHub usando un account personale

Se si è già connessi a GitHub e si è concessa solo l'autorizzazione per accedere a un account personale, seguire questa procedura per concedere le autorizzazioni a un'organizzazione.

1. Passare a GitHub e aprire **Impostazioni**.

    ![Aprire le impostazioni di GitHub](media/github-settings.png)

1. Selezionare **Applicazioni**. Nella scheda **App OAuth autorizzate** verrà visualizzato *Azure Synapse*.

    ![Autorizzare le app OAuth](media/authorize-app.png)

1. Selezionare il *Azure Synapse* e concedere l'accesso all'organizzazione.

    ![Concedere l'autorizzazione dell'organizzazione](media/grant-organization-permission.png)

Dopo aver completato questi passaggi, l'area di lavoro sarà in grado di connettersi ai repository pubblici e privati all'interno dell'organizzazione.

## <a name="version-control"></a>Controllo della versione

I sistemi di controllo della versione (noti anche come _controllo del_ codice sorgente) consentono agli sviluppatori di collaborare al codice e di tenere traccia delle modifiche. Il controllo del codice sorgente è uno strumento essenziale per i progetti multi-sviluppatore.

### <a name="creating-feature-branches"></a>Creazione di rami di funzionalità

Ogni repository Git associato a un Synapse Studio ha un ramo di collaborazione. ( `main` o è il ramo di collaborazione `master` predefinito). Gli utenti possono creare rami di funzionalità facendo clic su **Aggiungi nuovo ramo** nel menu a discesa dei rami. Quando viene visualizzato il riquadro del nuovo ramo, immettere il nome del ramo di funzionalità.

![Creare un nuovo ramo](media/create-new-branch.png)

Quando si è pronti per unire le modifiche dal ramo di funzionalità al ramo di collaborazione, fare clic sull'elenco a discesa del ramo e selezionare **Crea richiesta pull**. Questa azione consente di accedere al provider Git in cui è possibile generare richieste pull, eseguire revisioni del codice e unire le modifiche al ramo di collaborazione. È possibile pubblicare nel servizio Synapse solo dal ramo di collaborazione. 

![Creare una nuova richiesta pull](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Configurare le impostazioni di pubblicazione

Per impostazione predefinita, Synapse Studio i modelli di area di lavoro e li salva in un ramo denominato `workspace_publish` . Per configurare un ramo di pubblicazione personalizzato, aggiungere un file `publish_config.json` alla cartella radice nel ramo di collaborazione. Durante la pubblicazione, Synapse Studio legge questo file, cerca il campo e salva i file del modello dell'area di `publishBranch` lavoro nel percorso specificato. Se il ramo non esiste, Synapse Studio lo creerà automaticamente. Un esempio dell'aspetto del file è il seguente:

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure Synapse Studio può avere un solo ramo di pubblicazione alla volta. Quando si specifica un nuovo ramo di pubblicazione, il ramo di pubblicazione precedente non viene eliminato. Se si intende rimuovere il ramo di pubblicazione precedente, eliminarlo manualmente.


### <a name="publish-code-changes"></a>Pubblicare le modifiche al codice

Dopo aver unito le modifiche al ramo di collaborazione, fare clic su **Pubblica** per pubblicare manualmente le modifiche al codice nel ramo di collaborazione nel servizio Synapse.

![Pubblicare modifiche](media/gitmode-publish.png)

Si apre un riquadro laterale in cui l'utente conferma che il ramo di pubblicazione e le modifiche in sospeso sono corretti. Dopo aver verificato le modifiche, fare clic su **OK** per confermare la pubblicazione.

![Confermare il ramo di pubblicazione corretto](media/publish-change.png)

> [!IMPORTANT]
> Il ramo di collaborazione non è rappresentativo di ciò che viene distribuito nel servizio. Le modifiche nel ramo di *collaborazione devono* essere pubblicate manualmente.

## <a name="switch-to-a-different-git-repository"></a>Passare a un repository Git diverso

Per passare a un repository Git diverso, passare alla pagina configurazione Git nell'hub di gestione in **Controllo del codice sorgente.** Selezionare **Disconnetti.** 

![Icona Git](media/remove-repository.png)

Immettere il nome dell'area di lavoro e fare clic **su** Disconnetti per rimuovere il repository Git associato all'area di lavoro.

Dopo aver rimosso l'associazione con il repository corrente, è possibile configurare le impostazioni Git per l'uso di un repository diverso e quindi importare le risorse esistenti nel nuovo repository.

> [!IMPORTANT]
> La rimozione della configurazione Git da un'area di lavoro non elimina alcun elemento dal repository. L'area di lavoro di Synapse conterrà tutte le risorse pubblicate. È possibile continuare a modificare l'area di lavoro direttamente rispetto al servizio.

## <a name="best-practices-for-git-integration"></a>Procedure consigliate per l'integrazione con GIT

-   **Autorizzazioni**. Dopo aver connesso un repository Git all'area di lavoro, chiunque possa accedere al repository Git con qualsiasi ruolo nell'area di lavoro sarà in grado di aggiornare gli artefatti, ad esempio script SQL, notebook, definizione del processo Spark, set di dati, flusso di dati e pipeline in modalità Git. In genere non si vuole che ogni membro del team abbia le autorizzazioni per aggiornare l'area di lavoro. Concedere l'autorizzazione del repository Git solo agli autori di artefatti dell'area di lavoro Synapse. 
-   **Collaborazione**. Si consiglia di non consentire le archiviazioni dirette nel ramo collaborazione. Questa limitazione può aiutare a prevenire i bug in quanto ogni archiviazione passa attraverso un processo di revisione delle richieste pull descritto in [Creazione di rami di funzionalità](source-control.md#creating-feature-branches).
-   **Modalità synapse live**. Dopo la pubblicazione in modalità Git, tutte le modifiche verranno riflesse in modalità synapse live. In modalità live Synapse la pubblicazione è disabilitata. È anche possibile visualizzare ed eseguire gli elementi in modalità live se è stata concessa l'autorizzazione appropriata. 
-   **Modificare gli elementi in Studio**. Synapse Studio è l'unica posizione in cui è possibile abilitare il controllo del codice sorgente dell'area di lavoro e sincronizzare automaticamente le modifiche a Git. Qualsiasi modifica tramite SDK, PowerShell, non verrà sincronizzata con Git. È consigliabile modificare sempre l'artefatto in Studio quando git è abilitato.

## <a name="troubleshooting-git-integration"></a>Risoluzione dei problemi di integrazione git

### <a name="access-to-git-mode"></a>Accesso alla modalità Git 

Se è stata concessa l'autorizzazione per il repository GitHub git collegato all'area di lavoro, ma non è possibile accedere alla modalità Git: 

1. Cancellare la cache e aggiornare la pagina. 

1. Accedere all'account GitHub.

### <a name="stale-publish-branch"></a>Ramo di pubblicazione non aggiornato

Se il ramo di pubblicazione non è sincronizzato con il ramo di collaborazione e contiene risorse non aggiornate nonostante una pubblicazione recente, provare a seguire questa procedura:

1. Rimuovere il repository Git corrente

1. Riconfigurare Git con le stesse impostazioni, ma assicurarsi che l'opzione Importa risorse esistenti nel **repository** sia selezionata e scegliere lo stesso ramo.  

1. Creare una richiesta pull per unire le modifiche al ramo di collaborazione 

## <a name="unsupported-features"></a>Funzionalità non supportate

- Synapse Studio non consente il cherry picking dei commit o la pubblicazione selettiva delle risorse. 
- Synapse Studio non supporta la personalizzazione del messaggio di commit.
- Per impostazione predefinita, l'azione di eliminazione in Studio verrà impegnata direttamente in Git

## <a name="next-steps"></a>Passaggi successivi

* Per implementare l'integrazione e la distribuzione continue, vedere [Integrazione e recapito continui (CI/CD).](continuous-integration-deployment.md)