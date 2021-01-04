---
title: Gestire i pacchetti Python 3 in automazione di Azure
description: Questo articolo descrive come gestire i pacchetti Python 3 (anteprima) in automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: 3f39f49ff47b935da7ffc777ee45bd219f5740b5
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734302"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Gestire i pacchetti Python 3 (anteprima) in automazione di Azure

Automazione di Azure consente di eseguire Python 3 manuali operativi (anteprima) in Azure e nei ruoli di lavoro ibridi per Runbook Linux. Per favorire la semplificazione dei runbook, è possibile importare i moduli necessari usando pacchetti Python. Questo articolo descrive come gestire e usare i pacchetti Python 3 (anteprima) in automazione di Azure.

## <a name="import-packages"></a>Importare pacchetti

Nell'account di automazione selezionare **pacchetti Python** in **risorse condivise**. Selezionare **+ Aggiungi un pacchetto python**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Screenshot della pagina dei pacchetti Python 3 Mostra i pacchetti Python 3 nel menu a sinistra e aggiungere un pacchetto python 2 evidenziato.":::

Nella pagina **Aggiungi pacchetto python** selezionare Python 3 per la **versione** e selezionare un pacchetto locale da caricare. Il pacchetto può essere un file con estensione **whl** o **tar.gz**. Quando il pacchetto è selezionato, fare clic su **OK** per caricarlo.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Screenshot mostra la pagina Aggiungi pacchetto Python 3 con un file tar. gz caricato selezionato.":::

Una volta importato, un pacchetto viene elencato nella pagina pacchetti Python nell'account di automazione, nella scheda **Python 3 Packages (anteprima)** . Se è necessario rimuovere un pacchetto, selezionare il pacchetto e fare clic su **Elimina**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Screenshot che mostra la pagina dei pacchetti Python 3 dopo l'importazione di un pacchetto.":::

## <a name="import-packages-with-dependencies"></a>Importare pacchetti con dipendenze

Automazione di Azure non risolve le dipendenze per i pacchetti Python durante il processo di importazione. Tuttavia, esiste un modo per importare un pacchetto con tutte le relative dipendenze.

### <a name="manually-download"></a>Download manuale

In un computer Windows a 64 bit con [Python 3,8](https://www.python.org/downloads/release/python-380/) e [PIP](https://pip.pypa.io/en/stable/) installati, eseguire il comando seguente per scaricare un pacchetto e tutte le relative dipendenze:

```cmd
C:\Python38\Scripts\pip3.8.exe download -d <output dir> <package name>
```

Una volta scaricati i pacchetti, è possibile importarli nell'account di automazione.

## <a name="use-a-package-in-a-runbook"></a>Usare un pacchetto in un runbook

Con il pacchetto importato, è possibile usarlo in un Runbook. Aggiungere il codice seguente per elencare tutti i gruppi di risorse in una sottoscrizione di Azure.

```python
import os  
import azure.mgmt.resource  
import automationassets  

def get_automation_runas_credential(runas_connection):  
    from OpenSSL import crypto  
    import binascii  
    from msrestazure import azure_active_directory  
    import adal 

    # Get the Azure Automation RunAs service principal certificate  
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")  
    pks12_cert = crypto.load_pkcs12(cert)  
    pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())  

    # Get run as connection information for the Azure Automation service principal 
    application_id = runas_connection["ApplicationId"]  
    thumbprint = runas_connection["CertificateThumbprint"]  
    tenant_id = runas_connection["TenantId"]  

    # Authenticate with service principal certificate  
    resource ="https://management.core.windows.net/"  
    authority_url = ("https://login.microsoftonline.com/"+tenant_id)  
    context = adal.AuthenticationContext(authority_url)  
    return azure_active_directory.AdalAuthentication(  
    lambda: context.acquire_token_with_client_certificate(  
            resource,  
            application_id,  
            pem_pkey,  
            thumbprint) 
    ) 

# Authenticate to Azure using the Azure Automation RunAs service principal  
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")  
azure_credential = get_automation_runas_credential(runas_connection)  

# Intialize the resource management client with the RunAs credential and subscription  
resource_client = azure.mgmt.resource.ResourceManagementClient(  
    azure_credential,  
    str(runas_connection["SubscriptionId"]))  

# Get list of resource groups and print them out  
groups = resource_client.resource_groups.list()  
for group in groups:  
    print(group.name) 
```

## <a name="next-steps"></a>Passaggi successivi

Per preparare un runbook Python, vedere [Creare un runbook di Python](learn/automation-tutorial-runbook-textual-python-3.md).
