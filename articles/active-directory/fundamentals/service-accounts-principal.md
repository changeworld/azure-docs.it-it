---
title: Protezione delle entità servizio in Azure Active Directory
description: Trovare, valutare e proteggere le entità servizio.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 297c0a53fb2ab4ee0b2c5291cabf5a63c8841664
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604703"
---
# <a name="securing-service-principals"></a>Protezione delle entità servizio

Un' [entità servizio](../develop/app-objects-and-service-principals.md) Azure Active Directory (Azure ad) è la rappresentazione locale di un oggetto applicazione in un singolo tenant o directory.  Funge da identità dell'istanza dell'applicazione. Le entità servizio definiscono gli utenti che possono accedere all'applicazione e le risorse a cui l'applicazione può accedere. Viene creata un'entità servizio in ogni tenant in cui viene usata l'applicazione e fa riferimento all'oggetto applicazione univoco globale. Il tenant protegge l'accesso dell'entità servizio e l'accesso alle risorse.  

### <a name="tenant-service-principal-relationships"></a>Tenant-relazioni entità servizio
Un'applicazione a tenant singolo ha una sola entità servizio nel tenant principale. Un'applicazione o un'API Web multi-tenant richiede un'entità servizio in ogni tenant. Un'entità servizio viene creata quando un utente del tenant ha acconsentito all'uso dell'applicazione o dell'API. Questo consenso crea una relazione uno-a-molti tra l'applicazione multi-tenant e le entità servizio associate.

Un'applicazione multi-tenant è ospitata in un singolo tenant ed è progettata per avere istanze in altri tenant. La maggior parte delle applicazioni SaaS (software-as-a-Service) è progettata per il multi-tenant. Usare le entità servizio per garantire la correttezza del comportamento di sicurezza per l'applicazione e i relativi utenti nei casi di utilizzo a tenant singolo e multi-tenant.

## <a name="applicationid-and-objectid"></a>ApplicationID e ObjectID

Una determinata istanza dell'applicazione dispone di due proprietà distinte: ApplicationID (noto anche come ClientID) e ObjectID.

> [!NOTE] 
> Si noterà che i termini applicazione e entità servizio vengono usati in modo interscambiabile quando si fa riferimento a un'applicazione nel contesto di attività correlate all'autenticazione. Tuttavia, si tratta di due rappresentazioni diverse di applicazioni in Azure AD.
 

Il ApplicationID rappresenta l'applicazione globale ed è lo stesso per tutte le istanze dell'applicazione tra i tenant. ObjectID è un valore univoco per un oggetto applicazione e rappresenta l'entità servizio. Come per utenti, gruppi e altre risorse, ObjectID consente di identificare in modo univoco un'istanza dell'applicazione in Azure AD.

Per informazioni più dettagliate su questo argomento, vedere [relazione tra l'applicazione e l'entità servizio](../develop/app-objects-and-service-principals.md).

È anche possibile creare un'applicazione e il relativo oggetto entità servizio (ObjectID) in un tenant usando Azure PowerShell, l'interfaccia della riga di comando di Azure, Microsoft Graph, il portale di Azure e altri strumenti. 

![Screenshot che mostra una nuova registrazione dell'applicazione, con i campi ID applicazione e ID oggetto evidenziati.](./media/securing-service-accounts/secure-principal-image-1.png)

## <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Esistono due meccanismi per l'autenticazione tramite entità servizio: i certificati client e i segreti client. 

![ Screenshot della pagina nuova app che mostra i certificati e le aree dei segreti client evidenziati.](./media/securing-service-accounts/secure-principal-certificates.png)

I certificati sono più sicuri: usare i certificati client, se possibile. A differenza dei segreti client, i certificati client non possono essere incorporati accidentalmente nel codice. Usare Azure Key Vault per la gestione dei certificati e dei segreti, quando possibile, per crittografare le risorse seguenti usando le chiavi protette dai moduli di protezione hardware:

* chiavi di autenticazione

* chiavi dell'account di archiviazione

* chiavi di crittografia dei dati

* .pfx (file)

* password 

Per altre informazioni su Azure Key Vault e su come usarlo per la gestione dei certificati e dei segreti, vedere [informazioni su Azure Key Vault](../../key-vault/general/overview.md) e [assegnare un criterio di accesso Key Vault usando il portale di Azure](../../key-vault/general/assign-access-policy-portal.md). 

 ### <a name="challenges-and-mitigations"></a>Problemi e mitigazioni
Nella tabella seguente vengono illustrate le mitigazioni per le eventuali problemi che possono verificarsi quando si utilizzano le entità servizio.


| Problematiche| Soluzioni di prevenzione |
| - | - |
| Verifiche di accesso per le entità servizio assegnate ai ruoli con privilegi.| Questa funzionalità è in anteprima e non è ancora ampiamente disponibile. |
| Esamina l'accesso delle entità servizio| Controllo manuale dell'elenco di controllo di accesso della risorsa usando il portale di Azure. |
| Su entità servizio con autorizzazioni| Quando si creano account di servizio o entità servizio di automazione, fornire solo le autorizzazioni necessarie per l'attività. Valutare le entità servizio esistenti per verificare se è possibile ridurre i privilegi. |
|Identificare le modifiche alle credenziali delle entità servizio o ai metodi di autenticazione |Utilizzare la cartella di lavoro report operazioni sensibili, che consente di attenuare il problema. [Vedere la spiegazione in questo post di Blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718).|

## <a name="find-accounts-using-service-principals"></a>Trovare account usando entità servizio
Eseguire i comandi seguenti per trovare gli account che usano le entità servizio.

Utilizzare l'interfaccia della riga di comando di Azure


`az ad sp list`

Utilizzo di PowerShell

`Get-AzureADServicePrincipal -All:$true` 


Per ulteriori informazioni [, vedere Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal)

## <a name="assess-service-principal-security"></a>Valutazione della sicurezza dell'entità servizio

Per valutare la sicurezza delle entità servizio, assicurarsi di valutare i privilegi e l'archiviazione delle credenziali.

Attenuare potenziali problemi usando le informazioni seguenti.

|Problematiche | Soluzioni di prevenzione|
| - | - |
| Rilevare l'utente che ha acconsentito a un'app multi-tenant e rilevare le concessioni di consenso illecito a un'app multi-tenant | Eseguire il comando PowerShell seguente per trovare le app multi-tenant.<br>`Get-AzureADServicePrincipal -All:$true ? {$_.Tags -eq WindowsAzureActiveDirectoryIntegratedApp"}`<br>Disabilitare il consenso dell'utente. <br>Consenti autorizzazione utente da autori verificati, per le autorizzazioni selezionate (scelta consigliata) <br> Usare l'accesso condizionale per bloccare le entità servizio da percorsi non attendibili. Configurarli nel contesto utente e i relativi token devono essere usati per attivare l'entità servizio.|
|Uso di un segreto condiviso hardcoded in uno script usando un'entità servizio.|Usare un certificato o un Azure Key Vault.|
|Rilevamento dell'utilizzo del certificato o del segreto| Monitorare gli accessi dell'entità servizio usando i registri di accesso Azure AD.|
Non è possibile gestire l'accesso alle entità servizio con accesso condizionale.| Monitorare gli accessi usando i registri di accesso Azure AD
| Il ruolo predefinito RBAC di Azure è collaboratore. |Valutare le esigenze e applicare il ruolo con le autorizzazioni minime per soddisfare tale necessità.|

## <a name="move-from-a-user-account-to-a-service-principal"></a>Passare da un account utente a un'entità servizio  
Se si usa un account utente di Azure come entità servizio, valutare se è possibile passare a un' [identità gestita](../../app-service/overview-managed-identity.md?tabs=dotnet) o a un'entità servizio. Se non è possibile usare un'identità gestita, effettuare il provisioning di un'entità servizio con autorizzazioni e ambito sufficienti per eseguire le attività necessarie. È possibile creare un'entità servizio [registrando un'applicazione](../develop/howto-create-service-principal-portal.md)o con [PowerShell](../develop/howto-authenticate-service-principal-powershell.md).

Quando si usa Microsoft Graph, controllare la documentazione dell'API specifica, [come in questo esempio](/powershell/azure/create-azure-service-principal-azureps), e verificare che il tipo di autorizzazione per l'applicazione venga visualizzato come supportato.

## <a name="next-steps"></a>Passaggi successivi

**Per ulteriori informazioni sulle entità servizio:**

[Creare un'entità servizio](../develop/howto-create-service-principal-portal.md)

 [Monitorare gli accessi dell'entità servizio](../reports-monitoring/concept-sign-ins.md#sign-ins-report)

**Per ulteriori informazioni sulla protezione degli account del servizio:**

[Introduzione agli account del servizio di Azure](service-accounts-introduction-azure.md)

[Protezione delle identità gestite](service-accounts-managed-identities.md)

[Gestione degli account del servizio di Azure](service-accounts-governing-azure.md)

[Introduzione agli account del servizio locali](service-accounts-on-premises.md)
