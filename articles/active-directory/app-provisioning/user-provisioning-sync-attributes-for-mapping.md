---
title: Sincronizzare gli attributi Azure AD per il mapping
description: Informazioni su come sincronizzare gli attributi dalla Active Directory locale a Azure AD. Quando si configura il provisioning utenti in app SaaS, usare la funzionalità di estensione della directory per aggiungere gli attributi di origine che non sono sincronizzati per impostazione predefinita.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/12/2021
ms.author: kenwith
ms.openlocfilehash: 0f8369c80a7a219b159f31aacb7d10a0dd009d00
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418675"
---
# <a name="sync-an-attribute-from-your-on-premises-active-directory-to-azure-ad-for-provisioning-to-an-application"></a>Sincronizzare un attributo dal Active Directory locale a Azure AD per il provisioning in un'applicazione

Quando si personalizzano i mapping degli attributi per il provisioning degli utenti, si potrebbe notare che l'attributo di cui si vuole eseguire il mapping non viene visualizzato nell'elenco di **attributi di origine** . Questo articolo illustra come aggiungere l'attributo mancante eseguendo la sincronizzazione dal Active Directory locale (AD) al Azure Active Directory (Azure AD).

Azure AD deve contenere tutti i dati necessari per creare un profilo utente durante il provisioning degli account utente da Azure AD a un'app SaaS. In alcuni casi, per rendere disponibili i dati potrebbe essere necessario sincronizzare gli attributi dall'istanza locale di AD Azure AD. Azure AD Connect sincronizza automaticamente determinati attributi per Azure AD, ma non per tutti gli attributi. Inoltre, alcuni attributi, ad esempio SAMAccountName, sincronizzati per impostazione predefinita potrebbero non essere esposti tramite l'API Microsoft Graph. In questi casi, è possibile usare la funzionalità di estensione della directory Azure AD Connect per sincronizzare l'attributo con Azure AD. In questo modo, l'attributo sarà visibile all'API Microsoft Graph e al servizio di provisioning Azure AD.

Se i dati necessari per il provisioning sono in Active Directory ma non sono disponibili per il provisioning a causa dei motivi descritti in precedenza, è possibile usare Azure AD Connect o PowerShell per creare attributi di estensione. 
 
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

## <a name="create-an-extension-attribute-using-powershell"></a>Creare un attributo di estensione con PowerShell
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
