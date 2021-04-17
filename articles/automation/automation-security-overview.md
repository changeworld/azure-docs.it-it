---
title: Panoramica dell'autenticazione di account con Automazione di Azure
description: Questo articolo offre una panoramica dell'autenticazione di account con Automazione di Azure.
keywords: sicurezza in Automazione, proteggere Automazione; autenticazione in Automazione
services: automation
ms.subservice: process-automation
ms.date: 04/14/2021
ms.topic: conceptual
ms.openlocfilehash: 09aab71513b1152924de4eae91a718bad23d1012
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518002"
---
# <a name="azure-automation-account-authentication-overview"></a>Panoramica dell'autenticazione di account con Automazione di Azure

Automazione di Azure consente di automatizzare le attività sulle risorse in Azure, in locale e con altri provider di servizi cloud, ad esempio Amazon Web Services (AWS). È possibile usare i runbook per automatizzare le attività o un ruolo di lavoro ibrido per runbook se si dispone di processi aziendali o operativi da gestire all'esterno di Azure. L'uso di uno di questi ambienti richiede autorizzazioni per accedere in modo sicuro alle risorse con i diritti minimi necessari.

Questo articolo illustra i diversi scenari di autenticazione supportati da Automazione di Azure e descrive come iniziare partendo da uno o più ambienti che è necessario gestire.

## <a name="automation-account"></a>Account di Automazione

Al primo avvio di Automazione di Azure sarà necessario creare almeno un account di Automazione. Gli account di Automazione consentono di isolare le risorse di Automazione, i runbook, gli asset e le configurazioni dalle risorse di altri account. È possibile usare gli account di Automazione per separare le risorse in ambienti logici separati o responsabilità delegate. Ad esempio, è possibile usare un account per lo sviluppo, uno per la produzione e un altro per l'ambiente locale. Oppure è possibile dedicare un account di Automazione per gestire gli aggiornamenti del sistema operativo in tutti i computer [con Gestione aggiornamenti](update-management/overview.md). 

Un account di Automazione di Azure è diverso dagli account Microsoft creati nella sottoscrizione di Azure. Per un'introduzione alla creazione di un account di Automazione, vedere [Creare un account di Automazione](automation-quickstart-create-account.md).

## <a name="automation-resources"></a>Risorse di Automazione

Le risorse di Automazione per ogni account di Automazione sono associate a una singola area di Azure, ma l'account può gestire tutte le risorse nella sottoscrizione di Azure. Il motivo principale per cui creare gli account di Automazione in aree diverse è la presenza di criteri che richiedono che dati e risorse siano isolati in un'area specifica.

Tutte le attività eseguite sulle risorse con Azure Resource Manager e i cmdlet di PowerShell in Automazione di Azure devono eseguire l'autenticazione in Azure con l'autenticazione basata su credenziali dell'identità dell'organizzazione di Azure Active Directory (Azure AD).

## <a name="managed-identities-preview"></a>Identità gestite (anteprima)

Un'identità gestita Azure Active Directory (Azure AD) consente al runbook di accedere facilmente ad altre Azure AD protette. L'identità viene gestita dalla piattaforma Azure e non è necessario eseguire il provisioning o ruotare alcun segreto. Per altre informazioni sulle identità gestite in Azure AD, vedere [Identità gestite per le risorse di Azure.](/azure/active-directory/managed-identities-azure-resources/overview)

Ecco alcuni dei vantaggi dell'uso delle identità gestite:

- È possibile usare le identità gestite per eseguire l'autenticazione a qualsiasi servizio di Azure che supporta Azure AD autenticazione. Possono essere usati sia per i processi cloud che per i processi ibridi. I processi ibridi possono usare identità gestite quando vengono eseguiti in un ruolo di lavoro ibrido per runbook in esecuzione in una macchina virtuale di Azure o non di Azure.

- Le identità gestite possono essere usate senza costi aggiuntivi.

- Non è necessario rinnovare il certificato usato dall'account RunAs di Automazione.

- Non è necessario specificare l'oggetto connessione RunAs nel codice del runbook. È possibile accedere alle risorse usando l'identità gestita dell'account di Automazione da un runbook senza creare certificati, connessioni, account RunAs e così via.

A un account di Automazione possono essere concessi due tipi di identità:

- Un'identità assegnata dal sistema viene associata all'applicazione e viene eliminata in caso di eliminazione dell'app. A un'app può essere associata una sola identità assegnata dal sistema.

- Un'identità assegnata dall'utente è una risorsa di Azure autonoma che può essere assegnata all'app. Un'app può avere più identità assegnate dall'utente.

>[!NOTE]
> Le identità assegnate dall'utente non sono ancora supportate.

Per informazioni dettagliate sull'uso delle identità gestite, vedere [Abilitare l'identità gestita per Automazione di Azure (anteprima).](enable-managed-identity-for-automation.md)

## <a name="run-as-accounts"></a>Account RunAs

Gli account RunAs in Automazione di Azure l'autenticazione per la gestione Azure Resource Manager risorse o risorse distribuite nel modello di distribuzione classica. Esistono due tipi di account RunAs in Automazione di Azure:

* Account RunAs di Azure: consente di gestire le risorse di Azure in base Azure Resource Manager servizio di distribuzione e gestione per Azure.
* Account RunAs classico di Azure: consente di gestire le risorse classiche di Azure in base al modello di distribuzione classica.

Per altre informazioni sui modelli Azure Resource Manager distribuzione classica e classica, vedere Resource Manager [distribuzione classica.](../azure-resource-manager/management/deployment-models.md)

>[!NOTE]
>Le sottoscrizioni Azure Cloud Solution Provider (CSP) supportano solo il modello di Azure Resource Manager. I servi diversi da Azure Resource Manager non sono disponibili nel programma. Quando si usa una sottoscrizione CSP, non viene creato l'account RunAs classico di Azure, ma l'account RunAs di Azure. Per altre informazioni sulle sottoscrizioni CSP, vedere [Servizi disponibili nelle sottoscrizioni CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services).

Quando si crea un account di Automazione, l'account RunAs viene creato contemporaneamente per impostazione predefinita. Se si è scelto di non crearlo insieme all'account di Automazione, è possibile crearlo singolarmente in un secondo momento. Un account RunAs classico di Azure è facoltativo e viene creato separatamente se è necessario gestire le risorse classiche.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWwtF3]

### <a name="run-as-account"></a>account RunAs

Quando si crea un account RunAs, vengono eseguite le attività seguenti:

* Crea un'applicazione Azure AD con un certificato autofirmato, crea un account dell'entità servizio per l'applicazione in Azure AD e assegna il ruolo [Collaboratore](../role-based-access-control/built-in-roles.md#contributor) per l'account nella sottoscrizione corrente. È possibile modificare l'impostazione del certificato [in Lettore](../role-based-access-control/built-in-roles.md#reader) o in qualsiasi altro ruolo. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).

* Crea un asset del certificato di Automazione denominato `AzureRunAsCertificate` nell'account di Automazione specificato. L'asset del certificato contiene la chiave privata del certificato usata dall'applicazione Azure AD.

* Crea un asset della connessione di Automazione denominato `AzureRunAsConnection` nell'account di Automazione specificato. L'asset di connessione contiene l'ID applicazione, l'ID tenant, l'ID sottoscrizione e l'identificazione personale del certificato.

### <a name="azure-classic-run-as-account"></a>Account RunAs classico di Azure

Quando si crea un account RunAs classico di Azure, vengono eseguite le attività seguenti:

> [!NOTE]
> Per creare o rinnovare questo tipo di account RunAs, è necessario essere un co-amministratore della sottoscrizione.

* Crea un certificato di gestione nella sottoscrizione.

* Crea un asset del certificato di Automazione denominato `AzureClassicRunAsCertificate` nell'account di Automazione specificato. L'asset di certificato contiene la chiave privata del certificato usata dal certificato di gestione.

* Crea un asset della connessione di Automazione denominato `AzureClassicRunAsConnection` nell'account di Automazione specificato. L'asset della connessione contiene il nome della sottoscrizione, l'ID sottoscrizione e il nome dell'asset del certificato.

## <a name="service-principal-for-run-as-account"></a>Entità servizio per l'account RunAs

Per impostazione predefinita l'entità servizio per un account RunAs non ha le autorizzazioni di lettura per Azure AD. Se vogliono aggiungere autorizzazioni di lettura o gestione di Azure AD, è necessario concederle all'entità servizio in **Autorizzazioni API**. Per altre informazioni, vedere [Aggiungere autorizzazioni per accedere all'API Web.](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Autorizzazioni dell'account RunAs

Questa sezione definisce le autorizzazioni sia per i normali account RunAs che per gli account RunAs classici.

* Per creare o aggiornare un account RunAs, un amministratore dell'applicazione in Azure Active Directory e un proprietario nella sottoscrizione possono completare tutte le attività.
* Per configurare o rinnovare gli account RunAs classici, è necessario avere il ruolo di coamministratore a livello di sottoscrizione. Per altre informazioni sulle autorizzazioni della sottoscrizione classica, vedere [Amministratori della sottoscrizione classica di Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

Nel caso in cui i compiti siano separati, nella tabella seguente è disponibile un elenco delle attività, dei relativi cmdlet e delle autorizzazioni necessarie:

|Attività|Cmdlet  |Autorizzazioni minime  |Dove impostare le autorizzazioni|
|---|---------|---------|---|
|Creare un'applicazione Azure AD|[New-AzADApplication](/powershell/module/az.resources/new-azadapplication)     | Ruolo di Sviluppatore di applicazioni<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registrazioni app |
|Aggiungere una credenziale all'applicazione.|[New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential)     | Amministratore di applicazioni o Amministratore globale<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registrazioni app|
|Creare e ottenere un'entità servizio di Azure AD|[New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)     | Amministratore di applicazioni o Amministratore globale<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)</br>Home > Azure AD > Registrazioni app|
|Assegnare o ottenere il ruolo di Azure per l'entità di sicurezza specificata|[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Proprietario o Amministratore Accesso utenti oppure disporre delle autorizzazioni seguenti:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Sottoscrizione](../role-based-access-control/role-assignments-portal.md)</br>Home > sottoscrizioni > \<subscription name\> - Controllo di accesso (IAM)|
|Creare o rimuovere un certificato di Automazione|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](/powershell/module/az.automation/remove-azautomationcertificate)     | Collaboratore nel gruppo di risorse         |Gruppo di risorse dell'account di Automazione|
|Creare o rimuovere una connessione di Automazione|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](/powershell/module/az.automation/remove-azautomationconnection)|Collaboratore nel gruppo di risorse |Gruppo di risorse dell'account di Automazione|

<sup>1</sup> Gli utenti non amministratori nel tenant [](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app) di Azure AD possono registrare applicazioni AD se l'opzione  Gli utenti possono registrare applicazioni del tenant di Azure AD nella pagina Impostazioni utente è impostata su **Sì.**  Se l'impostazione di registrazione dell'applicazione è impostata su **No**, l'utente che esegue questa azione deve essere quello definito in questa tabella.

Se l'utente non è membro dell'istanza di Active Directory della sottoscrizione prima di essere aggiunto al ruolo di amministratore globale della sottoscrizione, viene aggiunto come guest. In questo caso, viene visualizzato un avviso `You do not have permissions to create…` nella pagina Aggiungi account di **Automazione.**

Per verificare che la situazione che ha prodotto il messaggio di errore è stata risolta:

1. Nel riquadro di Azure Active Directory del portale di Azure selezionare **Utenti e gruppi**.
2. Selezionare **Tutti gli utenti**.
3. Scegliere il nome, quindi selezionare **Profilo**.
4. Verificare che il valore dell'attributo **Tipo utente** nel profilo dell'utente non sia impostato su **Guest**.

## <a name="role-based-access-control"></a>Controllo degli accessi in base al ruolo

Il controllo degli accessi in base al ruolo è disponibile con Azure Resource Manager per concedere a un account utente di Azure AD e a un account RunAs l'autorizzazione per le azioni consentite e per l'autenticazione dell'entità servizio. Per altre informazioni utili per sviluppare il modello per la gestione delle autorizzazioni di Automazione, vedere [Controllo degli accessi in base al ruolo in Automazione di Azure](automation-role-based-access-control.md).

Se si dispone di controlli di sicurezza rigorosi per l'assegnazione delle autorizzazioni nei gruppi di risorse, è necessario assegnare l'appartenenza all'account RunAs al ruolo **Collaboratore** nel gruppo di risorse.

## <a name="runbook-authentication-with-hybrid-runbook-worker"></a>Autenticazione dei runbook con ruolo di lavoro ibrido per runbook

I runbook eseguiti in un ruolo di lavoro ibrido per runbook nel data center o in servizi di calcolo in altri ambienti cloud, come AWS, non possono usare lo stesso metodo usato solitamente per l'autenticazione dei runbook per le risorse di Azure. Il motivo è che queste risorse vengono eseguite all'esterno di Azure e di conseguenza devono usare le proprie credenziali di sicurezza definite in Automazione per autenticare le risorse cui accedono in locale. Per altre informazioni sull'autenticazione di runbook con i ruoli di lavoro per runbook, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

Per i runbook che usano i ruoli di lavoro ibridi per runbook nelle macchine virtuali di Azure, è possibile usare l'[autenticazione dei runbook con identità gestite](automation-hrw-run-runbooks.md#runbook-auth-managed-identities) invece degli account RunAs per l'autenticazione con le risorse di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per creare un account di Automazione dal portale di Azure, vedere [Creare un account di Automazione di Azure autonomo](automation-create-standalone-account.md).
* Se si preferisce creare l'account usando un modello, vedere [Creare un account di Automazione usando un modello di Azure Resource Manager](quickstart-create-automation-account-template.md).
* Per l'autenticazione con Amazon Web Services, vedere [Autenticare runbook con Amazon Web Services](automation-config-aws-account.md).
* Per un elenco dei servizi di Azure che supportano la funzionalità delle identità gestite per le risorse di Azure, vedere [Servizi che supportano le identità gestite per le risorse di Azure](/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities).
