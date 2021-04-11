---
title: Sincronizzare gli attributi Azure Active Directory per il mapping
description: Quando si configura il provisioning utenti con Azure Active Directory e app SaaS, usare la funzionalità di estensione della directory per aggiungere gli attributi di origine che non sono sincronizzati per impostazione predefinita.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: 102c0f7363b8d4f635762a33b82825e9ae71dfc6
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106120793"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Sincronizzazione degli attributi di estensione per il provisioning delle app

Azure Active Directory (Azure AD) deve contenere tutti i dati (attributi) necessari per creare un profilo utente durante il provisioning degli account utente da Azure AD a un' [app Saas](../saas-apps/tutorial-list.md). Quando si personalizzano i mapping degli attributi per il provisioning degli utenti, è possibile che l'attributo di cui si vuole eseguire il mapping non venga visualizzato nell'elenco di **attributi di origine** . Questo articolo illustra come aggiungere l'attributo mancante.

Per gli utenti solo in Azure AD, è possibile [creare estensioni dello schema usando PowerShell o Microsoft Graph](#create-an-extension-attribute-on-a-cloud-only-user).

Per gli utenti in Active Directory locali, è necessario sincronizzare gli utenti con Azure AD. È possibile sincronizzare utenti e attributi usando [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md). Azure AD Connect sincronizza automaticamente determinati attributi per Azure AD, ma non per tutti gli attributi. Inoltre, alcuni attributi, ad esempio SAMAccountName, sincronizzati per impostazione predefinita potrebbero non essere esposti tramite il Azure AD API Graph. In questi casi, è possibile [usare la funzionalità di estensione della directory Azure ad Connect per sincronizzare l'attributo con Azure ad](#create-an-extension-attribute-using-azure-ad-connect). In questo modo, l'attributo sarà visibile all'Azure AD API Graph e al servizio di provisioning di Azure AD.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Creare un attributo di estensione in un utente solo cloud
È possibile utilizzare Microsoft Graph e PowerShell per estendere lo schema utente per gli utenti Azure AD. Questi attributi di estensione vengono individuati automaticamente nella maggior parte dei casi.

Quando si hanno più di 1000 entità servizio, è possibile trovare estensioni mancanti nell'elenco di attributi di origine. Se un attributo creato non viene visualizzato automaticamente, verificare che l'attributo sia stato creato e aggiungerlo manualmente allo schema. Per verificare che sia stato creato, usare Microsoft Graph e [Graph Explorer](/graph/graph-explorer/graph-explorer-overview.md). Per aggiungerlo manualmente allo schema, vedere [modifica dell'elenco degli attributi supportati](customize-application-attributes.md#editing-the-list-of-supported-attributes).

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Creare un attributo di estensione in un utente solo cloud usando Microsoft Graph
È possibile estendere lo schema degli utenti Azure AD usando [Microsoft Graph](/graph/overview.md). 

Per prima cosa, elencare le app nel tenant per ottenere l'ID dell'app su cui si sta lavorando. Per altre informazioni, vedere [List extensionProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/applications
```

Successivamente, creare l'attributo di estensione. Sostituire la proprietà **ID** seguente con l' **ID** recuperato nel passaggio precedente. È necessario usare l'attributo **"ID"** e non il "AppID". Per altre informazioni, vedere [Create extensionProperty]/Graph/API/Application-post-extensionproperty.MD? View = Graph-REST-1.0&Tabs = http&Preserve-View = true).

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

La richiesta precedente ha creato un attributo di estensione con il formato `extension_appID_extensionName` . È ora possibile aggiornare un utente con questo attributo di estensione. Per altre informazioni, vedere [aggiornare un utente](/graph/api/user-update.md?view=graph-rest-1.0&tabs=http&preserve-view=true).
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Infine, verificare l'attributo per l'utente. Per altre informazioni, vedere [ottenere un utente](/graph/api/user-get.md?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true).

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


## <a name="next-steps"></a>Passaggi successivi

* [Definire gli utenti che rientrano nell'ambito del provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
