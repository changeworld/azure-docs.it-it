---
title: 'Guida introduttiva: creare un account di competenza con Python'
description: Creare un account Azure per la competenza usando Python.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: python
ms.topic: quickstart
ms.date: 04/02/2021
ms.openlocfilehash: f8ac611d25507913d6d5f2e2dd289ea52ce4ae9f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387509"
---
# <a name="quickstart-create-a-purview-account-using-python"></a>Guida introduttiva: creare un account di competenza con Python

In questa Guida introduttiva viene creato un account di competenza con Python. 

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Un [tenant di Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* L'account deve avere l'autorizzazione per creare risorse nella sottoscrizione

* Se sono impostati **Criteri di Azure** che impediscono a tutte le applicazioni di creare un **account di archiviazione** e uno **spazio dei nomi EventHub**, è necessario creare un'eccezione dei criteri usando un tag, che può essere immesso durante il processo di creazione di un account Purview. Il motivo principale è che per ogni account Purview creato occorre creare un gruppo di risorse gestito e, all'interno di tale gruppo, un account di archiviazione e uno spazio dei nomi EventHub. Per altre informazioni, vedere [creare il portale del catalogo](create-catalog-portal.md)


## <a name="install-the-python-package"></a>Installare il pacchetto Python

1. Aprire un terminale o un prompt dei comandi con privilegi di amministratore. 
2. Per prima cosa, installare il pacchetto Python per le risorse di gestione di Azure:

    ```python
    pip install azure-mgmt-resource
    ```
3. Per installare il pacchetto python per la competenza, eseguire il comando seguente:

    ```python
    pip install azure-mgmt-purview
    ```

    [Python SDK per competenze](https://github.com/Azure/azure-sdk-for-python) supporta Python 2,7, 3,3, 3,4, 3,5, 3,6 e 3,7.

4. Per installare il pacchetto Python per l'autenticazione di identità di Azure, eseguire questo comando:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > Il pacchetto "azure-identity" potrebbe avere conflitti con "azure-cli" in alcune dipendenze comuni. Se si verifica un problema di autenticazione, rimuovere "azure-cli" e le relative dipendenze oppure usare un computer pulito senza installare il pacchetto "azure-cli" per renderlo funzionante.
    
## <a name="create-a-purview-client"></a>Creazione di un client di competenza

1. Creare un file denominato **PURVIEW.py**. Aggiungere le istruzioni seguenti per aggiungere riferimenti a spazi dei nomi.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    ```

2. Aggiungere il codice seguente al metodo **Main** per creare un'istanza della classe PurviewManagementClient. Questo oggetto viene usato per creare un account di competenza, eliminare un account di competenza, controllare la disponibilità del nome e altre operazioni del provider di risorse.
 
 ```python
    def main():
    
    # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Location name, where Purview account must be created.
    location = '<location name>'    

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    ```

## <a name="create-a-purview-account"></a>Creare un account di competenza

Aggiungere il codice seguente al metodo **Main** per creare un **account di competenza**. Se il gruppo di risorse esiste già, impostare come commento la prima istruzione `create_or_update`.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location =location )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", pa.name, " Id: " , pa.id ," tags: " , pa.tags)  
    except:
        print("Error")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)      
        
```

Aggiungere quindi l'istruzione seguente per richiamare il metodo **main** quando viene eseguito il programma:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Script completo

Ecco il codice Python completo:

```python
    
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.purview import PurviewManagementClient
    from azure.mgmt.purview.models import *
    from datetime import datetime, timedelta
    import time
    
     # Azure subscription ID
    subscription_id = '<subscription ID>'
    
    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The purview name. It must be globally unique.
    purview_name = '<purview account name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    # resource_client = ResourceManagementClient(credentials, subscription_id)
    purview_client = PurviewManagementClient(credentials, subscription_id)
    
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a purview
    identity = Identity(type= "SystemAssigned")
    sku = AccountSku(name= 'Standard', capacity= 4)
    purview_resource = Account(identity=identity,sku=sku,location ="southcentralus" )
       
    try:
        pa = (purview_client.accounts.begin_create_or_update(rg_name, purview_name, purview_resource)).result()
        print("location:", pa.location, " Purview Account Name: ", purview_name, " Id: " , pa.id ," tags: " , pa.tags) 
    except:
        print("Error in submitting job to create account")
        print_item(pa)
 
    while (getattr(pa,'provisioning_state')) != "Succeeded" :
        pa = (purview_client.accounts.get(rg_name, purview_name))  
        print(getattr(pa,'provisioning_state'))
        if getattr(pa,'provisioning_state') != "Failed" :
            print("Error in creating Purview account")
            break
        time.sleep(30)    

# Start the main method
main()
```

## <a name="run-the-code"></a>Eseguire il codice

Compilare e avviare l'applicazione, quindi verificare l'esecuzione della pipeline.

La console stampa lo stato di creazione della data factory, del servizio collegato, dei set di dati, della pipeline e dell'esecuzione della pipeline. Attendere fino a quando non vengono visualizzati i dettagli sull'esecuzione dell'attività di copia con le dimensioni dei dati letti/scritti. Usare quindi strumenti come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per verificare che i BLOB siano stati copiati da "inputBlobPath" a "outputBlobPath", come specificato nelle variabili.

Di seguito è riportato l'output di esempio:

```console
location: southcentralus  Purview Account Name:  purviewpython7  Id:  /subscriptions/8c2c7b23-848d-40fe-b817-690d79ad9dfd/resourceGroups/Demo_Catalog/providers/Microsoft.Purview/accounts/purviewpython7  tags:  None
Creating
Creating
Succeeded
```

## <a name="verify-the-output"></a>Verificare l'output

Passare alla pagina **account di competenza** nella portale di Azure e verificare l'account creato con il codice precedente. 

## <a name="delete-purview-account"></a>Elimina account di competenza

Per eliminare l'account di competenza, aggiungere il codice seguente al programma:

```python
pa = purview_client.accounts.begin_delete(rg_name, purview_name).result()
```

## <a name="next-steps"></a>Passaggi successivi

Il codice in questa esercitazione crea un account di competenza ed elimina un account di competenza. È ora possibile scaricare Python SDK e ottenere informazioni sulle altre azioni del provider di risorse che è possibile eseguire per un account di competenza.

Passare all'articolo successivo per informazioni su come consentire agli utenti di accedere all'account Azure Purview. 

> [!div class="nextstepaction"]
> [Aggiungere utenti all'account Azure Purview](catalog-permissions.md)
