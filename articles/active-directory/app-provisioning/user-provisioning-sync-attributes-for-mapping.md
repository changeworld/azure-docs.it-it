---
title: Sincronizzare gli attributi con Azure Active Directory per il mapping
description: Quando si configura il provisioning utenti Azure Active Directory app SaaS, usare la funzionalità di estensione della directory per aggiungere attributi di origine non sincronizzati per impostazione predefinita.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: f7a2429161cebe867d844b4ca7aa08ec3613edcd
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388211"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Sincronizzazione degli attributi di estensione per il provisioning delle app

Azure Active Directory (Azure AD) deve contenere tutti i dati (attributi) necessari per creare un profilo utente durante il provisioning degli account utente da Azure AD a [un'app SaaS.](../saas-apps/tutorial-list.md) Quando si personalizzano i mapping degli attributi per il provisioning utenti, è possibile che l'attributo di cui si vuole eseguire il mapping non venga visualizzato **nell'elenco Attributo di** origine. Questo articolo illustra come aggiungere l'attributo mancante.

Per gli utenti solo in Azure AD, è possibile [creare estensioni dello schema usando PowerShell o Microsoft Graph](#create-an-extension-attribute-on-a-cloud-only-user).

Per gli utenti Active Directory locale, è necessario sincronizzare gli utenti con Azure AD. È possibile sincronizzare utenti e attributi [usando Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Azure AD Connect sincronizza automaticamente determinati attributi con Azure AD, ma non tutti gli attributi. Inoltre, alcuni attributi (ad esempio SAMAccountName) sincronizzati per impostazione predefinita potrebbero non essere esposti usando il Azure AD API Graph. In questi casi, è possibile usare la funzionalità di estensione [Azure AD Connect directory per sincronizzare l'attributo con Azure AD](#create-an-extension-attribute-using-azure-ad-connect). In questo modo, l'attributo sarà visibile al Azure AD API Graph e al Azure AD provisioning.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Creare un attributo di estensione in un utente solo cloud
È possibile usare Microsoft Graph e PowerShell per estendere lo schema utente per gli utenti in Azure AD. Questi attributi di estensione vengono individuati automaticamente nella maggior parte dei casi.

Se sono presenti più di 1000 entità servizio, è possibile che nell'elenco degli attributi di origine non sono presenti estensioni. Se un attributo creato non viene visualizzato automaticamente, verificare che l'attributo sia stato creato e aggiungerlo manualmente allo schema. Per verificare che sia stato creato, usare Microsoft Graph [e Graph Explorer.](/graph/graph-explorer/graph-explorer-overview) Per aggiungerlo manualmente allo schema, vedere [Modifica dell'elenco degli attributi supportati.](customize-application-attributes.md#editing-the-list-of-supported-attributes)

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Creare un attributo di estensione in un utente solo cloud usando Microsoft Graph
È possibile estendere lo schema di Azure AD utenti usando [Microsoft Graph](/graph/overview). 

Per prima cosa, elencare le app nel tenant per ottenere l'ID dell'app su cui si sta lavorando. Per altre informazioni, vedere [List extensionProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/applications
```

Creare quindi l'attributo di estensione. Sostituire la **proprietà ID** seguente con **l'ID** recuperato nel passaggio precedente. È necessario usare l'attributo **"ID"** e non "appId". Per altre informazioni, vedere [Creare extensionProperty]/graph/api/application-post-extensionproperty.md?view=graph-rest-1.0&tabs=http&preserve-view=true).

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

La richiesta precedente ha creato un attributo di estensione con il formato `extension_appID_extensionName` . È ora possibile aggiornare un utente con questo attributo di estensione. Per altre informazioni, vedere [Aggiornare l'utente.](/graph/api/user-update?view=graph-rest-1.0&tabs=http&preserve-view=true)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Infine, verificare l'attributo per l'utente. Per altre informazioni, vedere [Ottenere un utente.](/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true)

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

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Creare un attributo di estensione usando Azure AD Connect

1. Aprire la Azure AD Connect guidata, scegliere Attività, quindi scegliere **Personalizza opzioni di sincronizzazione.**

   ![Azure Active Directory Connect pagina Attività aggiuntive della procedura guidata](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Accedere come amministratore Azure AD globale. 

3. Nella pagina **Funzionalità facoltative** selezionare Sincronizzazione **degli attributi dell'estensione della directory.**
 
   ![Azure Active Directory Connect pagina Funzionalità facoltative della procedura guidata](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selezionare gli attributi da estendere a Azure AD.
   > [!NOTE]
   > La ricerca in Attributi **disponibili fa distinzione tra** maiuscole e minuscole.

   ![Screenshot che mostra la pagina di selezione "Estensioni della directory"](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Completare la Azure AD Connect guidata e consentire l'esecuzione di un ciclo di sincronizzazione completo. Al termine del ciclo, lo schema viene esteso e i nuovi valori vengono sincronizzati tra Active Directory locale e Azure AD.
 
6. Nell'portale di Azure, mentre si modificano i [mapping](customize-application-attributes.md)degli attributi utente, l'elenco **Attributo di** origine conterrà ora l'attributo aggiunto nel formato `<attributename> (extension_<appID>_<attributename>)` . Selezionare l'attributo ed eseguire il mapping all'applicazione di destinazione per il provisioning.

   ![Azure Active Directory Connect procedura guidata Pagina di selezione delle estensioni della directory](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> La possibilità di effettuare il provisioning degli attributi di riferimento da AD locale, ad esempio **managedby** o **DN/DistinguishedName,** non è attualmente supportata. È possibile richiedere questa funzionalità in [User Voice.](https://feedback.azure.com/forums/169401-azure-active-directory) 


## <a name="next-steps"></a>Passaggi successivi

* [Definire chi è nell'ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
