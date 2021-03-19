---
title: Sincronizzare gli attributi Azure AD per il mapping
description: Quando si configura il provisioning utenti in app SaaS, usare la funzionalità di estensione della directory per aggiungere gli attributi di origine che non sono sincronizzati per impostazione predefinita.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/17/2021
ms.author: kenwith
ms.openlocfilehash: 52f34cdafac76a9bca2b4ff0b00e0b3efaa63f5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579434"
---
# <a name="syncing-extension-attributes-attributes"></a>Sincronizzazione degli attributi degli attributi di estensione

Quando si personalizzano i mapping degli attributi per il provisioning degli utenti, si potrebbe notare che l'attributo di cui si vuole eseguire il mapping non viene visualizzato nell'elenco di **attributi di origine** . Questo articolo illustra come aggiungere l'attributo mancante eseguendo la sincronizzazione dal Active Directory locale (AD) al Azure Active Directory (Azure AD) o creando gli attributi di estensione in Azure AD per un utente solo cloud. 

Azure AD deve contenere tutti i dati necessari per creare un profilo utente durante il provisioning degli account utente da Azure AD a un'app SaaS. In alcuni casi, per rendere disponibili i dati potrebbe essere necessario sincronizzare gli attributi dall'istanza locale di AD Azure AD. Azure AD Connect sincronizza automaticamente determinati attributi per Azure AD, ma non per tutti gli attributi. Inoltre, alcuni attributi, ad esempio SAMAccountName, sincronizzati per impostazione predefinita potrebbero non essere esposti tramite il Azure AD API Graph. In questi casi, è possibile usare la funzionalità di estensione della directory Azure AD Connect per sincronizzare l'attributo con Azure AD. In questo modo, l'attributo sarà visibile all'Azure AD API Graph e al servizio di provisioning di Azure AD. Se i dati necessari per il provisioning sono in Active Directory ma non sono disponibili per il provisioning a causa dei motivi descritti in precedenza, è possibile usare Azure AD Connect per creare attributi di estensione. 

Anche se la maggior parte degli utenti è probabilmente un utente ibrido sincronizzato da Active Directory, è anche possibile creare estensioni per gli utenti solo cloud senza usare Azure AD Connect. Usando PowerShell o Microsoft Graph è possibile estendere lo schema di un utente solo cloud. 

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Creare un attributo di estensione usando Azure AD Connect

1. Aprire la procedura guidata Azure AD Connect, scegliere attività, quindi scegliere **Personalizza opzioni di sincronizzazione**.

   ![Pagina attività aggiuntive della procedura guidata Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Accedere come amministratore globale Azure AD. 

3. Nella pagina **funzionalità facoltative** selezionare **sincronizzazione degli attributi di estensione della directory**.
 
   ![Pagina delle funzionalità facoltative della procedura guidata Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selezionare gli attributi che si desidera estendere Azure AD.
   > [!NOTE]
   > La ricerca sotto **gli attributi disponibili** fa distinzione tra maiuscole e minuscole.

   ![Screenshot che mostra la pagina di selezione delle estensioni di directory](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Terminare la procedura guidata Azure AD Connect e consentire l'esecuzione di un ciclo di sincronizzazione completo. Al termine del ciclo, lo schema viene esteso e i nuovi valori vengono sincronizzati tra AD locale e Azure AD.
 
6. Nel portale di Azure, mentre si [modificano i mapping degli attributi utente](customize-application-attributes.md), l'elenco di **attributi di origine** conterrà ora l'attributo aggiunto nel formato `<attributename> (extension_<appID>_<attributename>)` . Selezionare l'attributo ed eseguirne il mapping all'applicazione di destinazione per il provisioning.

   ![Pagina di selezione delle estensioni della directory della procedura guidata Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> La possibilità di eseguire il provisioning degli attributi di riferimento da AD locale, ad esempio **ManagedBy** o **DN/Distinguishname**, non è attualmente supportata. È possibile richiedere questa funzionalità in [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Creare un attributo di estensione in un utente solo cloud
I clienti possono usare Microsoft Graph e PowerShell per estendere lo schema utente. Questi attributi di estensione vengono individuati automaticamente nella maggior parte dei casi, ma i clienti con più di 1000 entità servizio possono trovare estensioni mancanti nell'elenco di attributi di origine. Se un attributo creato usando la procedura seguente non viene visualizzato automaticamente nell'elenco attributo di origine, verificare tramite Graph che l'attributo di estensione sia stato creato correttamente e quindi aggiungerlo [manualmente](https://docs.microsoft.com/azure/active-directory/app-provisioning/customize-application-attributes#editing-the-list-of-supported-attributes)allo schema. Quando si effettuano le richieste di grafi seguenti, fare clic su altre informazioni per verificare le autorizzazioni necessarie per eseguire le richieste. Per eseguire le richieste, è possibile usare [Graph Explorer](https://docs.microsoft.com/graph/graph-explorer/graph-explorer-overview) . 

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Creare un attributo di estensione in un utente solo cloud usando Microsoft Graph
Sarà necessario utilizzare un'applicazione per estendere lo schema degli utenti. Elencare le app nel tenant per identificare l'ID dell'applicazione che si vuole usare per estendere lo schema utente. [Altre informazioni.](https://docs.microsoft.com/graph/api/application-list?view=graph-rest-1.0&tabs=http)

```json
GET https://graph.microsoft.com/v1.0/applications
```

Creare l'attributo di estensione. Sostituire la proprietà **ID** seguente con l' **ID** recuperato nel passaggio precedente. È necessario usare l'attributo **"ID"** e non il "AppID". [Altre informazioni.](https://docs.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-1.0&tabs=http)
```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

La richiesta precedente ha creato un attributo di estensione con il formato "extension_appID_extensionName". Aggiornare un utente con l'attributo di estensione. [Altre informazioni.](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0&tabs=http)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Controllare l'utente per assicurarsi che l'attributo sia stato aggiornato correttamente. [Altre informazioni.](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select)

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Creare un attributo di estensione in un utente solo cloud usando PowerShell
Creare un'estensione personalizzata usando PowerShell e assegnare un valore a un utente. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the id of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="next-steps"></a>Passaggi successivi

* [Definire gli utenti che rientrano nell'ambito del provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
