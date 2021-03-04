---
title: Gestire i pacchetti Python 3 in automazione di Azure
description: Questo articolo descrive come gestire i pacchetti Python 3 (anteprima) in automazione di Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/19/2021
ms.topic: conceptual
ms.openlocfilehash: fd4d8ee92b670bc2544619a0dce16a26d9342c13
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122035"
---
# <a name="manage-python-3-packages-preview-in-azure-automation"></a>Gestire i pacchetti Python 3 (anteprima) in automazione di Azure

Automazione di Azure consente di eseguire Python 3 manuali operativi (anteprima) in Azure e nei ruoli di lavoro ibridi per Runbook Linux. Per favorire la semplificazione dei runbook, è possibile importare i moduli necessari usando pacchetti Python. Per importare un singolo pacchetto, vedere [importare un pacchetto](#import-a-package). Per importare un pacchetto con più pacchetti, vedere [importare un pacchetto con dipendenze](#import-a-package-with-dependencies). Questo articolo descrive come gestire e usare i pacchetti Python 3 (anteprima) in automazione di Azure.

## <a name="import-a-package"></a>Importare un pacchetto

Nell'account di automazione selezionare **pacchetti Python** in **risorse condivise**. Selezionare **+ Aggiungi un pacchetto python**.

:::image type="content" source="media/python-3-packages/add-python-3-package.png" alt-text="Screenshot della pagina dei pacchetti Python 3 Mostra i pacchetti Python 3 nel menu a sinistra e aggiungere un pacchetto python 2 evidenziato.":::

Nella pagina **Aggiungi pacchetto python** selezionare **Python 3** per la **versione** e selezionare un pacchetto locale da caricare. Il pacchetto può essere un file con estensione **whl** o **tar.gz**. Quando il pacchetto è selezionato, fare clic su **OK** per caricarlo.

:::image type="content" source="media/python-3-packages/upload-package.png" alt-text="Screenshot mostra la pagina Aggiungi pacchetto Python 3 con un file tar. gz caricato selezionato.":::

Una volta importato, un pacchetto viene elencato nella pagina pacchetti Python nell'account di automazione, nella scheda **Python 3 Packages (anteprima)** . Se è necessario rimuovere un pacchetto, selezionare il pacchetto e fare clic su **Elimina**.

:::image type="content" source="media/python-3-packages/python-3-packages-list.png" alt-text="Screenshot che mostra la pagina dei pacchetti Python 3 dopo l'importazione di un pacchetto.":::

### <a name="import-a-package-with-dependencies"></a>Importare un pacchetto con dipendenze

È possibile importare un pacchetto Python 3 e le relative dipendenze importando lo script Python seguente in un Runbook Python 3 e quindi eseguendolo.

```cmd
https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py
```

#### <a name="importing-the-script-into-a-runbook"></a>Importazione dello script in un Runbook
Per informazioni sull'importazione del Runbook, vedere [importare un Runbook dal portale di Azure](manage-runbooks.md#import-a-runbook-from-the-azure-portal). Copiare il file da GitHub nello spazio di archiviazione a cui il portale può accedere prima di eseguire l'importazione.

Per impostazione predefinita, la pagina **Importa un Runbook** il nome Runbook in modo che corrisponda al nome dello script. Se si dispone dell'accesso al campo, è possibile modificare il nome. Il **tipo di Runbook** può essere impostato su **Python 2**. In caso contrario, assicurarsi di modificarlo in **Python 3**.

:::image type="content" source="media/python-3-packages/import-python-3-package.png" alt-text="Screenshot che mostra la pagina di importazione Runbook Python 3.":::

#### <a name="executing-the-runbook-to-import-the-package-and-dependencies"></a>Esecuzione di Runbook per importare il pacchetto e le dipendenze

Dopo aver creato e pubblicato il Runbook, eseguirlo per importare il pacchetto. Per informazioni dettagliate sull'esecuzione di Runbook, vedere [avviare un Runbook in automazione di Azure](start-runbooks.md) .

Lo script ( `import_py3package_from_pypi.py` ) richiede i seguenti parametri.

| Parametro | Descrizione |
|---------------|-----------------|
|subscription_id | ID sottoscrizione dell'account di automazione |
| resource_group | Nome del gruppo di risorse in cui è definito l'account di automazione |
| automation_account | Nome dell'account di automazione |
| module_name | Nome del modulo da cui eseguire l'importazione `pypi.org` |

Per altre informazioni sull'uso dei parametri con manuali operativi, vedere usare i parametri di [Runbook](start-runbooks.md#work-with-runbook-parameters).

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
