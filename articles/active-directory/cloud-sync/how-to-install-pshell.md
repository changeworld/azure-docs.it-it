---
title: Installare l'agente di provisioning di Azure AD Connect Cloud con PowerShell
description: Informazioni su come installare l'agente di provisioning di Azure AD Connect Cloud usando i cmdlet di PowerShell.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c20cfb96b5cd6e1d05e332fa7157fe6e0cde8656
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98613699"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Installare l'agente di provisioning di Azure AD Connect usando i cmdlet di PowerShell 
Nel documento seguente viene illustrato come installare l'agente di provisioning di Azure AD Connect usando i cmdlet di PowerShell.
 

## <a name="prerequisite"></a>Prerequisito: 


>[!IMPORTANT]
>Nelle istruzioni di installazione seguenti si presuppone che siano stati soddisfatti tutti i [Prerequisiti](how-to-prerequisites.md).
>
> È necessario abilitare TLS 1,2 per Windows Server prima di installare l'agente di provisioning Azure AD Connect usando i cmdlet di PowerShell. Per abilitare TLS 1,2, è possibile usare i passaggi disponibili [qui](how-to-prerequisites.md#tls-requirements).

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>Installare l'agente di provisioning di Azure AD Connect usando i cmdlet di PowerShell 


 1. Accedere al portale di Azure, quindi passare a **Azure Active Directory**.
 2. Nel menu a sinistra selezionare **Azure ad Connect**.
 3. Selezionare **Gestisci il provisioning (anteprima)**  > **Verifica tutti gli agenti**.
 4. Scaricare l'agente di provisioning Azure AD Connect dal portale di Azure a un in locale.  

   ![Scarica agente locale](media/how-to-install/install-9.png)</br>
 5. Ai fini di queste istruzioni, l'agente è stato scaricato nella cartella seguente: "C:\ProvisioningSetup". 
 6. Installare ProvisioningAgent in modalità non interattiva

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. Importazione del modulo PS dell'agente di provisioning 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.Powershell.dll" 
   ```
 8. Connettersi a AzureAD usando le credenziali di amministratore globale è possibile personalizzare questa sezione per recuperare la password da un archivio protetto. 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   Connect-AADCloudSyncAzureAD-Credential $globalAdminCreds 

 9. Aggiungere l'account gMSA, fornire le credenziali dell'amministratore di dominio per creare l'account gMSA predefinito 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. In alternativa, usare il cmdlet precedente come indicato di seguito per fornire un account gMSA creato in precedenza 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. Aggiungi dominio 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. In alternativa, usare il cmdlet precedente come indicato di seguito per configurare i controller di dominio Preferiti 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. Ripetere il passaggio precedente per aggiungere altri domini. specificare i nomi di account e i nomi di dominio dei rispettivi domini 
 14. Riavviare il servizio. 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  Passare al portale di Azure per creare la configurazione di sincronizzazione cloud.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>Cmdlet di PowerShell per l'agente di provisioning gMSA
Ora che è stato installato l'agente, è possibile applicare autorizzazioni più granulari a gMSA.  Per informazioni e istruzioni dettagliate sulla configurazione delle autorizzazioni, vedere Azure AD Connect i cmdlet di PowerShell per gMSA per l' [agente di provisioning cloud](how-to-gmsa-cmdlets.md) .

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Cmdlet di PowerShell gMSA per l'agente di provisioning di Azure AD Connect Cloud](how-to-gmsa-cmdlets.md)
- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)