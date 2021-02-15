---
title: 'Guida introduttiva: creare un gruppo di gestione con Python'
description: In questa Guida introduttiva si userà Python per creare un gruppo di gestione per organizzare le risorse in una gerarchia di risorse.
ms.date: 01/29/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e3c55cc14a8ac980318fd0de9485a3e0ca31b582
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101765"
---
# <a name="quickstart-create-a-management-group-with-python"></a>Guida introduttiva: creare un gruppo di gestione con Python

I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../policy/overview.md) e [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](overview.md).

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica. Per altre informazioni, vedere [Configurazione iniziale dei gruppi di gestione](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Se la [protezione della gerarchia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) non è abilitata, qualsiasi utente di Azure AD nel tenant può creare un gruppo di gestione senza avere autorizzazioni di scrittura assegnate per tale gruppo di gestione. Questo nuovo gruppo di gestione diventa un elemento figlio del gruppo di gestione radice o del [gruppo di gestione predefinito](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e all'autore viene assegnato il ruolo "Proprietario". Il servizio del gruppo di gestione offre questa possibilità per evitare che siano necessarie assegnazioni di ruolo a livello di radice. Nessun utente ha accesso al gruppo di gestione radice quando viene creato. Per evitare il problema di reperire gli amministratori globali di Azure AD per iniziare a usare i gruppi di gestione, è consentita la creazione dei gruppi di gestione iniziali al livello radice.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>Aggiungere la libreria Resource Graph

Per abilitare Python per la gestione dei gruppi di gestione, è necessario aggiungere la libreria. Questa libreria funziona ovunque sia possibile usare Python, sia con [bash in Windows 10](/windows/wsl/install-win10) o con installazione locale.

1. Controllare che sia installata la versione di Python più recente o almeno la versione **3.8**. Se non è ancora installata, scaricarla dal sito [Python.org](https://www.python.org/downloads/).

1. Controllare che sia installata l'interfaccia della riga di comando di Azure più recente o almeno la versione **2.5.1**. Se non è ancora installata, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > L'interfaccia della riga di comando di Azure è necessaria per consentire a Python di usare **l'autenticazione basata sull'interfaccia della riga di comando** negli esempi seguenti. Per informazioni su altre opzioni, vedere [Eseguire l'autenticazione tramite le librerie di gestione di Azure per Python](/azure/developer/python/azure-sdk-authenticate).

1. Eseguire l'autenticazione tramite l'interfaccia della riga di comando di Azure.

   ```azurecli
   az login
   ```

1. Nell'ambiente Python scelto installare le librerie necessarie per i gruppi di gestione:

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Se Python è installato per tutti gli utenti, questi comandi devono essere eseguiti da una console con privilegi elevati.

1. Verificare che le librerie siano state installate. `azure-mgmt-managementgroups` deve essere **0.2.0** o una versione successiva, `azure-mgmt-resource` deve essere **9.0.0** o una versione successiva e `azure-cli-core` deve essere **2.5.0** o una versione successiva.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>Creare il gruppo di gestione

1. Creare lo script Python e salvare l'origine seguente come `mgCreate.py` :

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
   
   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}
       
       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)
       
       # Show results
       print(mg)
   
   createmanagementgroup("MyNewMG")
   ```

1. Eseguire l'autenticazione con l'interfaccia della riga di `az login` comando di Azure

1. Immettere il comando seguente nel terminale:

   ```bash
   py mgCreate.py
   ```

Il risultato della creazione del gruppo di gestione viene restituito alla console come `LROPoller` oggetto.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende rimuovere le librerie installate dall'ambiente Python, è possibile farlo con il comando seguente:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un gruppo di gestione per organizzare la gerarchia delle risorse. Il gruppo di gestione può contenere sottoscrizioni o altri gruppi di gestione.

Per altre informazioni sui gruppi di gestione e su come gestire la gerarchia delle risorse, continuare con:

> [!div class="nextstepaction"]
> Gestire le risorse con i gruppi di gestione