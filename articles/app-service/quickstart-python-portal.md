---
title: "Guida introduttiva: creare un'app Python nel portale di Azure"
description: Iniziare a usare app Azure servizio distribuendo la prima app Python in un contenitore Linux nel servizio app usando il portale di Azure.
ms.topic: quickstart
ms.date: 04/01/2021
ms.custom: devx-track-python
robots: noindex
ms.openlocfilehash: 42f29152521da7bf90a550248e8429e51b728b24
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012357"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux-azure-portal"></a>Guida introduttiva: creare un'app Python usando app Azure servizio in Linux (portale di Azure)

In questa esercitazione dell'avvio rapido si distribuirà una semplice app Web Python nel [Servizio app in Linux](overview.md#app-service-on-linux), il servizio di hosting Web con scalabilità elevata e applicazione automatica di patch. Usare il portale di Azure per distribuire un esempio con i Framework Flask o Django. L'app Web configurata usa un livello di servizio app di base che comporta un costo ridotto nella sottoscrizione di Azure.

## <a name="configure-accounts"></a>Configurare gli account

- Se non si dispone ancora di un account Azure con una sottoscrizione attiva, [creare un account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Se non si ha un account GitHub, visitare [github.com](https://github.com) per crearne uno. 

## <a name="fork-the-sample-github-repository"></a>Creare una copia tramite fork del repository GitHub di esempio

1. Aprire [github.com](https://github.com) e accedere.

1. Passare a uno dei repository di esempio seguenti:
    - [Hello World Flask](https://github.com/Azure-Samples/python-docs-hello-world)
    - [Hello World Django](https://github.com/Azure-Samples/python-docs-hello-django)

1. Nella parte superiore destra della pagina GitHub selezionare **fork** per creare una copia del repository nel proprio account github:

    ![Comando fork di GitHub](media/quickstart-python-portal/github-fork-command.png)

    Azure richiede l'accesso all'organizzazione GitHub che contiene il repository. Tramite la divisione dell'esempio con il proprio account GitHub, l'utente dispone automaticamente dell'accesso necessario e può anche apportare modifiche al codice.

## <a name="provision-the-app-service-web-app"></a>Eseguire il provisioning dell'app Web del servizio app

Un'app Web del servizio app è il server Web in cui si distribuisce il codice.

1. Aprire il portale di Azure in [https://portal.azure.com](https://portal.azure.com) e accedere se necessario.

1. Nella barra di ricerca nella parte superiore del portale di Azure immettere "servizio app" e quindi selezionare **Servizi app**.

    ![Barra di ricerca del portale e selezione del servizio app](media/quickstart-python-portal/portal-search-bar.png)

1. Nella pagina **Servizi app** selezionare "**+ Aggiungi**:

    ![Aggiungi comando del servizio app](media/quickstart-python-portal/add-app-service.png)

1. Nella pagina **Crea app Web** eseguire le azioni seguenti:
    
    | Campo | Azione |
    | --- | --- |
    | Subscription | Selezionare la sottoscrizione di Azure da usare. |
    | Gruppo di risorse | Selezionare **Crea nuovo** sotto l'elenco a discesa. Nella finestra popup immettere "AppService-PythonQuickstart" e selezionare "**OK**". |
    | Nome | Immettere un nome univoco in tutte le piattaforme di Azure, in genere usando una combinazione dei nomi personali o aziendali, ad esempio *Contoso-TestApp-123*. |
    | Pubblica | Selezionare **Codice**. |
    | Stack di runtime | Selezionare **Python 3,8**. |
    | Sistema operativo | Selezionare **Linux** (Python è supportato solo in Linux). |
    | Region | Selezionare un'area nelle vicinanze. |
    | Piano Linux | Selezionare un piano di servizio app in uscita o usare **Crea nuovo** per crearne uno nuovo. Si consiglia di usare il piano **B1 di base** . |

    ![Crea il modulo dell'app Web nel portale di Azure](media/quickstart-python-portal/create-web-app.png)

1. Nella parte inferiore della pagina selezionare **Verifica + crea**, esaminare i dettagli e quindi selezionare **Crea**.

1. Al termine del provisioning, selezionare **Vai alla risorsa** per passare alla pagina nuovo servizio app. L'app Web a questo punto contiene solo una pagina predefinita, quindi il passaggio successivo distribuisce il codice di esempio.

Problemi? [Segnalarli](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="deploy-the-sample-code"></a>Distribuire il codice di esempio

1. Nella pagina dell'app Web nella portale di Azure selezionare **centro distribuzione**:
    
    ![Comando del centro distribuzione nel menu del servizio app](media/quickstart-python-portal/deployment-center-command.png)


1. Nella pagina **centro distribuzione** selezionare la scheda **Impostazioni** se non è già aperta:

    ![Scheda Impostazioni Centro distribuzione](media/quickstart-python-portal/deployment-center-settings-tab.png)

1. In **origine** selezionare **GitHub**, quindi nel modulo **GitHub** visualizzato eseguire le operazioni seguenti:

    | Campo | Azione |
    | --- | --- |
    | Signed in as | Se non è già stato effettuato l'accesso a GitHub, accedere ora oppure selezionare **Cambia account* , se necessario. |
    | Organization | Se necessario, selezionare l'organizzazione GitHub. |
    | Archivio | Selezionare il nome del repository di esempio di cui si è diramato in precedenza, ovvero **Python-docs-Hello-World** (Flask) o **Python-docs-Hello-Django** (Django). |
    | Ramo | Selezionare **principale**. |

    ![Configurazione origine GitHub di Deployment Center](media/quickstart-python-portal/deployment-center-configure-github-source.png)

1. Nella parte superiore della pagina selezionare **Salva** per applicare le impostazioni.:

    ![Salvare la configurazione dell'origine GitHub in Deployment Center](media/quickstart-python-portal/deployment-center-configure-save.png)

1. Selezionare la scheda **logs** per visualizzare lo stato della distribuzione. Sono necessari alcuni minuti per compilare e distribuire l'esempio e vengono visualizzati log aggiuntivi durante il processo. Al termine, i log devono riflettere lo stato di **esito positivo (attivo)**:

    ![Scheda log del centro distribuzione](media/quickstart-python-portal/deployment-center-logs.png)

Problemi? [Segnalarli](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="browse-to-the-app"></a>Passare all'app

1. Al termine della distribuzione, scegliere **Panoramica** dal menu a sinistra per tornare alla pagina principale dell'app Web.

1. Selezionare l' **URL** che contiene l'indirizzo dell'app Web:

    ![URL dell'app Web nella pagina Panoramica](media/quickstart-python-portal/web-app-url.png)

1. Verificare che l'output dell'app sia "Hello, World!":

    ![App in esecuzione dopo la distribuzione iniziale](media/quickstart-python-portal/web-app-first-deploy-results.png)

Problemi? Per prima cosa, vedere la [guida alla risoluzione dei problemi](configure-language-python.md#troubleshooting). Se i problemi persistono, [segnalarli](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="make-a-change-and-redeploy"></a>Apportare una modifica e ridistribuire

Poiché il servizio app è stato connesso al repository, le modifiche di cui si esegue il commit nel repository di origine vengono distribuite automaticamente nell'app Web.

1. È possibile apportare modifiche direttamente nel repository con fork in GitHub oppure clonare il repository in locale, apportare ed eseguire il commit delle modifiche e quindi effettuare il push delle modifiche in GitHub. Entrambi i metodi comportano una modifica al repository connesso al servizio app.

1. **Nel repository con fork**, modificare il messaggio dell'app da "Hello, World!" per "Hello, Azure!" come indicato di seguito:
    - Flask (esempio Python-docs-Hello-World): modificare la stringa di testo alla riga 6 del file *Application.py* .
    - Django (esempio Python-docs-Hello-Django): modificare la stringa di testo nella riga 5 del file *views.py* all'interno della cartella *Hello* .

1. Eseguire il commit della modifica nel repository.

    Se usi un clone locale, Esegui anche il push delle modifiche in GitHub.

1. Nella portale di Azure per l'app Web tornare al **centro distribuzione**, selezionare la scheda **log** e prendere nota della nuova attività di distribuzione che dovrebbe essere in corso.

1. Al termine della distribuzione, tornare alla pagina **Panoramica** dell'app Web, aprire di nuovo l'URL dell'app Web e osservare le modifiche nell'app:

    ![App in esecuzione dopo la ridistribuzione](media/quickstart-python-portal/web-app-second-deploy-results.png)

Problemi? Per prima cosa, vedere la [guida alla risoluzione dei problemi](configure-language-python.md#troubleshooting). Se i problemi persistono, [segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Pulire le risorse

Nei passaggi precedenti sono state create le risorse di Azure in un gruppo di risorse denominato "AppService-PythonQuickstart", visualizzato nella pagina *Panoramica* dell'app Web. Se si mantiene l'app Web in esecuzione, verranno addebitati costi ricorrenti. Vedere [Prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/linux/).

Se non si prevede di dover utilizzare queste risorse in futuro, selezionare il nome del gruppo di risorse nella pagina **Panoramica** dell'app Web per passare alla panoramica dei gruppi di risorse. Selezionare **Elimina gruppo di risorse** e seguire le istruzioni.

![Eliminazione del gruppo di risorse](media/quickstart-python-portal/delete-resource-group.png)


Problemi? [Segnalarli](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: App Web Python (Django) con PostgreSQL](/azure/developer/python/tutorial-python-postgresql-app-portal)

> [!div class="nextstepaction"]
> [Configurare un'app Python](configure-language-python.md)

> [!div class="nextstepaction"]
> [Aggiungere l'accesso utente a un'app Web Python](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire un'app Python in un contenitore personalizzato](tutorial-custom-container.md)
