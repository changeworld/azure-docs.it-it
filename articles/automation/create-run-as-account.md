---
title: Creare un account RunAs di Automazione di Azure
description: Questo articolo descrive come creare un account RunAs con PowerShell o dalla portale di Azure.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: d6c8e96c325cfa6422ceb4efc55ef0c5cc149ae3
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99055899"
---
# <a name="how-to-create-an-azure-automation-run-as-account"></a>Come creare un account RunAs di automazione di Azure

Gli account RunAs in automazione di Azure forniscono l'autenticazione per la gestione delle risorse nel modello di distribuzione Azure Resource Manager o di Azure classico usando manuali operativi di automazione e altre funzionalità di automazione. Questo articolo descrive come creare un account RunAs o RunAs classico dal portale di Azure o Azure PowerShell.

## <a name="create-account-in-azure-portal"></a>Crea account in portale di Azure

Eseguire la procedura seguente per aggiornare l'account di Automazione di Azure nel portale di Azure. Gli account RunAs e RunAs classico vengono creati separatamente. Se non è necessario gestire le risorse classiche, è sufficiente creare l'account RunAs di Azure.

1. Accedere al Portale di Azure con un account membro del ruolo Amministratori della sottoscrizione e coamministratore della sottoscrizione.

2. Cercare e selezionare **Account di Automazione**.

3. Nella pagina **account di automazione** selezionare l'account di automazione dall'elenco.

4. Nel riquadro sinistro selezionare **account RunAs** nella sezione **Impostazioni account** .

    :::image type="content" source="media/create-run-as-account/automation-account-properties-pane.png" alt-text="Selezionare l'opzione account RunAs.":::

5. A seconda dell'account necessario, usare l' **account RunAs di Azure** o il riquadro **account RunAs classico di Azure** . Dopo aver esaminato le informazioni generali, fare clic su **Crea**.

    :::image type="content" source="media/create-run-as-account/automation-account-create-run-as.png" alt-text="Selezionare l'opzione per creare un account RunAs":::

6. Mentre Azure crea l'account RunAs, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu. Viene anche visualizzato un banner che indica che l'account è in fase di creazione. Il completamento del processo potrebbe richiedere alcuni minuti.

## <a name="create-account-using-powershell"></a>Creare un account con PowerShell

L'elenco seguente include i requisiti per creare un account RunAs in PowerShell usando uno script fornito. Questi requisiti si applicano a entrambi i tipi di account RunAs.

* Windows 10 o Windows Server 2016 con i moduli di Azure Resource Manager 3.4.1 e versioni successive. Lo script di PowerShell non supporta versioni precedenti di Windows.
* Azure PowerShell PowerShell 6.2.4 o versione successiva. Per informazioni, vedere [come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).
* Un account di Automazione, a cui si fa riferimento con il valore per i parametri `AutomationAccountName` e `ApplicationDisplayName`.
* Autorizzazioni equivalenti a quanto indicato in [Autorizzazioni necessarie per la configurazione degli account RunAs](automation-security-overview.md#permissions).

Per ottenere i valori per `AutomationAccountName` , `SubscriptionId` e `ResourceGroupName` , che sono parametri obbligatori per lo script di PowerShell, completare i passaggi seguenti.

1. Accedere al portale di Azure.

1. Cercare e selezionare **Account di Automazione**.

1. Nella pagina Account di automazione selezionare l'account di Automazione dall'elenco.

1. Nel riquadro di sinistra selezionare **Proprietà**.

1. Prendere nota dei valori per **nome**, **ID sottoscrizione** e **gruppo di risorse** nella pagina delle **Proprietà** .

   ![Pagina delle proprietà dell'account di Automazione](media/create-run-as-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Script di PowerShell per creare un account RunAs

Lo script di PowerShell include il supporto per diverse configurazioni.

* Creare un account RunAs usando un certificato autofirmato.
* Creare un account RunAs e/o un account RunAs classico usando un certificato autofirmato.
* Creare un account RunAs e/o un account RunAs classico usando un certificato emesso dall'autorità di certificazione (CA) dell'organizzazione (Enterprise).
* Creare un account RunAs e/o un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici.

1. Scaricare e salvare lo script in una cartella locale usando il comando seguente.

    ```powershell
    wget https://raw.githubusercontent.com/azureautomation/runbooks/master/Utility/AzRunAs/Create-RunAsAccount.ps1 -outfile Create-RunAsAccount.ps1
    ```

2. Avviare PowerShell con diritti utente elevati e passare alla cartella che contiene lo script.

3. Eseguire uno dei comandi seguenti per creare un account RunAs e/o un account RunAs classico in base ai propri requisiti.

    * Creare un account RunAs usando un certificato autofirmato.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
        ```

    * Creare un account RunAs e un account RunAs classico usando un certificato autofirmato.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
        ```

    * Creare un account RunAs e un account RunAs classico usando un certificato enterprise.

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
        ```

        Se è stato creato un account RunAs classico con un certificato pubblico globale (enterprise), ovvero un file con estensione CER, usare questo certificato. Lo script lo crea e lo salva nella cartella dei file temporanei nel computer, nel profilo utente `%USERPROFILE%\AppData\Local\Temp` usato per eseguire la sessione di PowerShell. Vedere [Caricamento di un certificato dell'API di gestione nel portale di Azure](../cloud-services/cloud-services-configure-ssl-certificate-portal.md).

    * Creare un account RunAs e un account RunAs classico usando un certificato autofirmato nel cloud di Azure per enti pubblici

        ```powershell
        .\Create-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnvironmentName AzureUSGovernment
        ```

4. Dopo l'esecuzione dello script, all'utente viene richiesto di autenticarsi con Azure. Accedere con un account membro del ruolo amministratori della sottoscrizione. Se si sta creando un account RunAs classico, è necessario che l'account sia un coamministratore della sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione grafica, vedere [Creazione grafica in Automazione di Azure](automation-graphical-authoring-intro.md).
* Per iniziare a usare i runbook PowerShell, vedere [Esercitazione: Creare un runbook di PowerShell](learn/automation-tutorial-runbook-textual-powershell.md).
* Per iniziare a usare un Runbook Python 3, vedere [esercitazione: creare un Runbook Python 3](learn/automation-tutorial-runbook-textual-python-3.md).