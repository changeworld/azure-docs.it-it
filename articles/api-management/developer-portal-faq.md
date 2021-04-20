---
title: Portale per sviluppatori - Domande frequenti
titleSuffix: Azure API Management
description: Domande frequenti sul portale per sviluppatori in API Management. Il portale per sviluppatori è un sito Web personalizzabile in cui i consumer di API possono esplorare le API.
services: api-management
documentationcenter: API Management
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: apimpm
ms.openlocfilehash: b4413bc53cf5c86c311d049046db790582737ca4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741681"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>API Management per sviluppatori - Domande frequenti

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>Cosa succede se sono necessarie funzionalità non supportate nel portale?

È possibile aprire una richiesta di funzionalità nel [repository GitHub](https://github.com/Azure/api-management-developer-portal) o [implementare manualmente la funzionalità mancante.](developer-portal-implement-widgets.md) Altre informazioni sull'estendibilità [del portale per sviluppatori.](api-management-howto-developer-portal.md#managed-vs-self-hosted)


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>È possibile avere più portali per sviluppatori in un API Management servizio?

È possibile avere un portale gestito e più portali self-hosted. Il contenuto di tutti i portali viene archiviato nello stesso API Management, quindi saranno identici. Se si vuole differenziare l'aspetto e le funzionalità dei portali, è possibile ospitarli autonomamente con widget personalizzati che personalizzano dinamicamente le pagine in fase di esecuzione, ad esempio in base all'URL.

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Il portale supporta Azure Resource Manager modelli e/o è compatibile con API Management Resource Kit devOps?

No.

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Il contenuto del portale viene salvato con la funzionalità di backup/ripristino in API Management?

No.

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>È necessario abilitare la connettività di rete virtuale aggiuntiva per le dipendenze del portale gestito?

Nella maggior parte dei casi, no.

Se il API Management si trova in una rete virtuale interna, il portale per sviluppatori è accessibile solo dall'interno della rete. Il nome host dell'endpoint di gestione deve essere risolto nell'indirizzo VIP interno del servizio dal computer in uso per accedere all'interfaccia amministrativa del portale. Assicurarsi che l'endpoint di gestione sia registrato nel DNS. In caso di configurazione errata, verrà visualizzato un errore: `Unable to start the portal. See if settings are specified correctly in the configuration (...)` .

Se il servizio API Management si trova in una rete virtuale interna e si accede tramite il gateway applicazione da Internet, assicurarsi di abilitare la connettività al portale per sviluppatori e agli endpoint di gestione di API Management. Potrebbe essere necessario disabilitare le regole di Web Application Firewall. Per [altri dettagli, vedere](api-management-howto-integrate-internal-vnet-appgateway.md) questo articolo della documentazione.

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>È stato assegnato un API Management personalizzato e il portale pubblicato non funziona

Dopo aver aggiornato il dominio, è necessario [ripubblicare il portale per](api-management-howto-developer-portal-customize.md#publish) l'applicazione delle modifiche.

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>È stato aggiunto un provider di identità che non è possibile visualizzare nel portale

Dopo aver configurato un provider di identità, ad esempio Azure AD, Azure AD B2C), è necessario ripubblicare il portale per l'applicazione delle modifiche. [](api-management-howto-developer-portal-customize.md#publish) Assicurarsi che le pagine del portale per sviluppatori includano il widget pulsanti OAuth.

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>La delega è stata impostata e il portale non la usa

Dopo aver configurato la delega, è necessario [ripubblicare il portale per](api-management-howto-developer-portal-customize.md#publish) l'applicazione delle modifiche.

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Le altre API Management di configurazione non sono state propagate nel portale per sviluppatori

La maggior parte delle modifiche di configurazione ( ad esempio, la rete virtuale, l'accesso, le condizioni del prodotto) richiede [la ripubblicazione del portale](api-management-howto-developer-portal-customize.md#publish).

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Viene visualizzato un errore CORS quando si usa la console interattiva

La console interattiva esegue una richiesta API lato client dal browser. Risolvere il problema CORS aggiungendo [un criterio CORS](api-management-cross-domain-policies.md#CORS) alle API.

È possibile controllare lo stato dei criteri CORS nella sezione **Panoramica** del portale del servizio API Management nel portale di Azure. Una casella di avviso indica un criterio assente o non configurato correttamente.

> [!NOTE]
> 
> Viene eseguito un solo criterio CORS. Se sono stati specificati più criteri CORS, ad esempio a livello di API e a livello di tutte le API, la console interattiva potrebbe non funzionare come previsto.

![Screenshot che mostra dove è possibile controllare lo stato dei criteri CORS.](media/developer-portal-faq/cors-azure-portal.png)

Applicare automaticamente i criteri CORS facendo clic sul **pulsante Abilita CORS.**

È anche possibile abilitare CORS manualmente.

1. Selezionare il **collegamento Applica manualmente a livello globale** per visualizzare il codice dei criteri generato.
2. Passare a **Tutte le API** nella sezione **API** del servizio API Management nel portale di Azure.
3. Selezionare **</>** l'icona nella **sezione Elaborazione in** ingresso.
4. Inserire i criteri nella **<inbound>** sezione del file XML. Assicurarsi che il **<origin>** valore corrisponda al dominio del portale per sviluppatori.

> [!NOTE]
> 
> Se si applicano i criteri CORS nell'ambito Product anziché nell'ambito DELLE API e l'API usa l'autenticazione della chiave di sottoscrizione tramite un'intestazione, la console non funzionerà.
>
> Il browser genera automaticamente `OPTIONS` una richiesta HTTP, che non contiene un'intestazione con la chiave di sottoscrizione. A causa della chiave di sottoscrizione mancante, API Management non è possibile associare la chiamata a un prodotto, quindi non può applicare `OPTIONS` i criteri CORS.
>
> Come soluzione alternativa, è possibile passare la chiave di sottoscrizione in un parametro di query.

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>Che cos'è la funzionalità proxy CORS e quando è consigliabile usarla?

Selezionare **l'opzione Usa proxy CORS** nella configurazione del widget Dettagli operazione API per instradare le chiamate API della console interattiva tramite il back-end del portale nel API Management servizio. In questa configurazione non è più necessario applicare un criterio CORS per le API e la connettività all'endpoint del gateway dal computer locale non è necessaria. Se le API vengono esposte tramite un gateway self-hosted o il servizio si trova in una rete virtuale, è necessaria la connettività dal servizio back-end del API Management al gateway. Se si usa il portale self-hosted, specificare l'endpoint back-end del portale usando `backendUrl` l'opzione nei file di configurazione. In caso contrario, il portale self-hosted non sarà a conoscenza della posizione del servizio back-end.

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Quali autorizzazioni sono necessarie per modificare il portale per sviluppatori?

Se viene visualizzato l'errore quando si apre il portale in modalità amministrativa, è possibile che non siano disponibili le autorizzazioni necessarie (Controllo degli accessi in base al `Oops. Something went wrong. Please try again later.` ruolo di Azure).

I portali legacy richiedeva l'autorizzazione nell'ambito del servizio ( ) per consentire `Microsoft.ApiManagement/service/getssotoken/action` `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>` all'amministratore utente l'accesso ai portali. Il nuovo portale richiede `Microsoft.ApiManagement/service/users/token/action` l'autorizzazione per l'ambito `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` .

È possibile usare lo script di PowerShell seguente per creare un ruolo con l'autorizzazione necessaria. Ricordarsi di modificare il `<subscription-id>` parametro . 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Una volta creato, il ruolo può essere concesso a qualsiasi utente dalla sezione Controllo di accesso **(IAM)** del portale di Azure. L'assegnazione di questo ruolo a un utente consente di assegnare l'autorizzazione nell'ambito del servizio. L'utente sarà in grado di generare token di firma di accesso condiviso per conto *di qualsiasi* utente nel servizio. Come minimo, questo ruolo deve essere assegnato all'amministratore del servizio. Il comando di PowerShell seguente illustra come assegnare il ruolo a un utente nell'ambito più basso per evitare di concedere `user1` autorizzazioni non necessarie all'utente: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Dopo aver concesso le autorizzazioni a un utente, l'utente deve disconnettersi e accedere di nuovo al portale di Azure per avere effetto sulle nuove autorizzazioni.

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Viene visualizzato `Unable to start the portal. See if settings are specified correctly (...)` l'errore

Questo errore viene visualizzato quando una chiamata a `GET` non `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` riesce. La chiamata viene emessa dal browser dall'interfaccia amministrativa del portale.

Se il servizio API Management si trova in una rete virtuale, vedere la domanda sulla [connettività della rete virtuale](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies).

L'errore di chiamata può anche essere causato da un certificato TLS/SSL, assegnato a un dominio personalizzato e non considerato attendibile dal browser. Come mitigazione, è possibile rimuovere il dominio personalizzato dell'endpoint di gestione API Management eseguire il fall back all'endpoint predefinito con un certificato attendibile.

## <a name="whats-the-browser-support-for-the-portal"></a>Qual è il supporto del browser per il portale?

| Browser                     | Supportato       |
|-----------------------------|-----------------|
| Apple Safari                | Sì<sup>1</sup> |
| Google Chrome               | Sì<sup>1</sup> |
| Microsoft Edge              | Sì<sup>1</sup> |
| Microsoft Internet Explorer | No              |
| Mozilla Firefox             | Sì<sup>1</sup> |

 <small><sup>1</sup> Supportato nelle due versioni di produzione più recenti.</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>Lo sviluppo locale del portale self-hosted non funziona più

Se la versione locale del portale per sviluppatori non è in grado di salvare o recuperare informazioni dall'account di archiviazione o dall'istanza API Management, i token di firma di accesso condiviso potrebbero essere scaduti. È possibile risolvere questo problema generando nuovi token. Per istruzioni, vedere l'esercitazione per [ospitare autonomamente il portale per sviluppatori.](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings)

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>Come è possibile rimuovere il contenuto del portale per sviluppatori di cui è stato effettuato il provisioning nel API Management servizio?

Specificare i parametri necessari nello `scripts.v3/cleanup.bat` script nel [repository GitHub](https://github.com/Azure/api-management-developer-portal)del portale per sviluppatori ed eseguire lo script

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>Ricerca per categorie l'autenticazione Single Sign-On (SSO) al portale per sviluppatori self-hosted?

Tra gli altri metodi di autenticazione, il portale per sviluppatori supporta l'accesso Single Sign-On (SSO). Per eseguire l'autenticazione con questo metodo, è necessario eseguire una chiamata a `/signin-sso` con il token nel parametro di query:

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>Generare token utente
È possibile generare *token specifici dell'utente* (inclusi i token di amministratore) usando l'operazione Get [Shared Access Token](/rest/api/apimanagement/2019-12-01/user/getsharedaccesstoken) (Ottieni token di accesso condiviso) dell'API REST [API Management.](/rest/api/apimanagement/apimanagementrest/api-management-rest)

> [!NOTE]
> Il token deve essere codificato con URL.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul nuovo portale per sviluppatori:

- [Accedere al portale per sviluppatori gestito e personalizzarlo](api-management-howto-developer-portal-customize.md)
- [Configurare la versione self-hosted del portale](developer-portal-self-host.md)
- [Implementare un widget personalizzato](developer-portal-implement-widgets.md)

Esplorare altre risorse:

- [Repository GitHub con il codice sorgente](https://github.com/Azure/api-management-developer-portal)

