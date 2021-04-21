---
title: Risolvere i problemi di accesso al Registro di sistema
description: Sintomi, cause e risoluzione dei problemi comuni durante l'accesso a un Registro Azure Container
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 47186cc8256836e5367ecee520787b67662eb42f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780732"
---
# <a name="troubleshoot-registry-login"></a>Risolvere i problemi di accesso al Registro di sistema

Questo articolo consente di risolvere i problemi che possono verificarsi durante l'accesso a un Registro Azure Container. 

## <a name="symptoms"></a>Sintomi

Può includere uno o più degli elementi seguenti:

* Impossibile accedere al Registro di sistema usando `docker login` `az acr login` , o entrambi
* Non è possibile accedere al Registro di sistema e viene visualizzato un errore `unauthorized: authentication required` o `unauthorized: Application not registered with AAD`
* Impossibile accedere al Registro di sistema e viene visualizzato un errore dell'interfaccia della riga di comando di Azure `Could not connect to the registry login server`
* Non è possibile eseguire il push o il pull di immagini e viene visualizzato l'errore Docker `unauthorized: authentication required`
* Impossibile accedere al Registro di sistema da servizio Azure Kubernetes, Azure DevOps o da un altro servizio di Azure
* Non è possibile accedere al Registro di sistema e viene visualizzato `Error response from daemon: login attempt failed with status: 403 Forbidden` l'errore - Vedere Risolvere i problemi di rete con il Registro di [sistema](container-registry-troubleshoot-access.md)
* Non è possibile accedere o visualizzare le impostazioni del Registro di sistema in portale di Azure o gestire il Registro di sistema usando l'interfaccia della riga di comando di Azure

## <a name="causes"></a>Cause

* Docker non è configurato correttamente nell'ambiente - [soluzione](#check-docker-configuration)
* Il Registro di sistema non esiste o il nome non è corretto- [Soluzione](#specify-correct-registry-name)
* Le credenziali del Registro di sistema non sono valide - [Soluzione](#confirm-credentials-to-access-registry)
* Le credenziali non sono autorizzate per le operazioni push, pull o Azure Resource Manager- [soluzione](#confirm-credentials-are-authorized-to-access-registry)
* Le credenziali sono scadute - [Soluzione](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>Ulteriore diagnosi 

Eseguire il [comando az acr check-health](/cli/azure/acr#az_acr_check_health) per ottenere altre informazioni sull'integrità dell'ambiente del Registro di sistema e, facoltativamente, per accedere a un registro di destinazione. Ad esempio, diagnosticare gli errori di configurazione di Docker o Azure Active Directory di accesso. 

Per esempi di comandi, vedere Controllare [l'integrità](container-registry-check-health.md) di un Registro Azure Container. Se vengono segnalati errori, esaminare le informazioni [di riferimento sull'errore](container-registry-health-error-reference.md) e le sezioni seguenti per le soluzioni consigliate.

Se si verificano problemi durante l'uso del registro wih servizio Azure Kubernetes, eseguire il comando [az aks check-acr](/cli/azure/aks#az_aks_check_acr) per verificare che il Registro di sistema sia accessibile dal cluster del servizio Web Diaz.

> [!NOTE]
> Alcuni errori di autenticazione o autorizzazione possono verificarsi anche se sono presenti configurazioni di rete o firewall che impediscono l'accesso al Registro di sistema. Vedere Risolvere [i problemi di rete con il Registro di sistema.](container-registry-troubleshoot-access.md)

## <a name="potential-solutions"></a>Possibili soluzioni

### <a name="check-docker-configuration"></a>Controllare la configurazione di Docker

La maggior Registro Azure Container di autenticazione richiede un'installazione locale di Docker per poter eseguire l'autenticazione con il registro per operazioni quali il push e il pull di immagini. Verificare che il client e il daemon dell'interfaccia della riga di comando di Docker (motore Docker) siano in esecuzione nell'ambiente in uso. È necessario il client Docker versione 18.03 o successiva.

Collegamenti correlati:

* [Panoramica dell'autenticazione](container-registry-authentication.md#authentication-options)
* [Domande frequenti sul registro contenitori](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Specificare il nome del registro corretto

Quando si usa , specificare il nome completo del server di accesso del Registro di `docker login` sistema, ad esempio *myregistry.azurecr.io*. Assicurarsi di usare solo lettere minuscole. Esempio:

```console
docker login myregistry.azurecr.io
```

Quando si usa [az acr login](/cli/azure/acr#az_acr_login) con un'identità Azure Active Directory, accedere prima di tutto all'interfaccia della riga di comando di [Azure](/cli/azure/authenticate-azure-cli)e quindi specificare il nome della risorsa di Azure del registro. Il nome della risorsa è il nome specificato al momento della creazione del registro, ad esempio *myregistry* (senza un suffisso di dominio). Esempio:

```azurecli
az acr login --name myregistry
```

Collegamenti correlati:

* [az acr login ha esito positivo, ma i comandi di Docker hanno esito negativo con errore: non autorizzato: autenticazione obbligatoria](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)

### <a name="confirm-credentials-to-access-registry"></a>Confermare le credenziali per accedere al Registro di sistema

Controllare la validità delle credenziali usate per lo scenario o che sono state fornite da un proprietario del registro. Alcuni problemi possibili:

* Se si usa un'entità servizio di Active Directory, assicurarsi di usare le credenziali corrette nel tenant di Active Directory:
  * Nome utente- ID applicazione dell'entità servizio (detto *anche ID client)*
  * Password : password dell'entità servizio (detta *anche segreto client)*
* Se si usa un servizio di Azure come servizio Azure Kubernetes o Azure DevOps accedere al Registro di sistema, verificare la configurazione del Registro di sistema per il servizio. 
* Se è stata eseguita con l'opzione , che consente l'accesso al Registro di sistema senza usare il `az acr login` daemon Docker, assicurarsi di eseguire `--expose-token` l'autenticazione con il nome utente `00000000-0000-0000-0000-000000000000` .
* Se il Registro di sistema è configurato per [l'accesso pull](container-registry-faq.md#how-do-i-enable-anonymous-pull-access)anonimo, le credenziali Docker esistenti archiviate da un accesso Docker precedente possono impedire l'accesso anonimo. Eseguire `docker logout` prima di tentare un'operazione pull anonima nel Registro di sistema.

Collegamenti correlati:

* [Panoramica dell'autenticazione](container-registry-authentication.md#authentication-options)
* [Accesso individuale con Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)
* [Accedere con l'entità servizio](container-registry-auth-service-principal.md)
* [Account di accesso con identità gestita](container-registry-authentication-managed-identity.md)
* [Account di accesso con token con ambito repository](container-registry-repository-scoped-permissions.md)
* [Accedere con l'account amministratore](container-registry-authentication.md#admin-account)
* [Azure AD codici di errore di autenticazione e autorizzazione](../active-directory/develop/reference-aadsts-error-codes.md)
* [az acr login](/cli/azure/acr#az_acr_login) reference

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Verificare che le credenziali siano autorizzate ad accedere al Registro di sistema

Verificare le autorizzazioni del Registro di sistema associate alle credenziali, ad esempio il ruolo di Azure per eseguire il pull delle immagini dal Registro di sistema o il `AcrPull` ruolo per eseguire il push delle `AcrPush` immagini. 

L'accesso a un registro nel portale o nella gestione del registro tramite l'interfaccia della riga di comando di Azure richiede almeno il ruolo o le autorizzazioni equivalenti per eseguire Azure Resource Manager `Reader` operazioni.

Se le autorizzazioni sono state modificate di recente per consentire l'accesso al Registro di sistema tramite il portale, potrebbe essere necessario provare una sessione in incognito o privata nel browser per evitare eventuali cookie o cache del browser non obsoleti.

L'utente o un proprietario del Registro di sistema deve disporre di privilegi sufficienti nella sottoscrizione per aggiungere o rimuovere assegnazioni di ruolo.

Collegamenti correlati:

* [Ruoli e autorizzazioni di Azure - Registro Azure Container](container-registry-roles.md)
* [Accedere con un token con ambito repository](container-registry-repository-scoped-permissions.md)
* [Aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../role-based-access-control/role-assignments-portal.md)
* [Usare il portale per creare un'applicazione Azure AD un'entità servizio che può accedere alle risorse](../active-directory/develop/howto-create-service-principal-portal.md)
* [Crea un nuovo segreto dell'applicazione](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Azure AD di autenticazione e autorizzazione](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Verificare che le credenziali non siano scadute

I token e le credenziali di Active Directory possono scadere dopo periodi definiti, impedendo l'accesso al Registro di sistema. Per abilitare l'accesso, potrebbe essere necessario reimpostare o rigenerare le credenziali.

* Se si usa una singola identità di AD, un'identità gestita o un'entità servizio per l'accesso al registro, il token ad Active Directory scade dopo 3 ore. Accedere di nuovo al Registro di sistema.  
* Se si usa un'entità servizio di Active Directory con un segreto client scaduto, un proprietario della sottoscrizione o un amministratore dell'account deve reimpostare le credenziali o generare una nuova entità servizio.
* Se si usa un [token con ambito repository,](container-registry-repository-scoped-permissions.md)un proprietario del registro potrebbe dover reimpostare una password o generare un nuovo token.

Collegamenti correlati:

* [Reimpostare le credenziali dell'entità servizio](/cli/azure/ad/sp/credential#az_ad_sp_credential_reset)
* [Rigenerare le password dei token](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Accesso individuale con Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Risoluzione dei problemi avanzata

Se [la raccolta di log delle](container-registry-diagnostics-audit-logs.md) risorse è abilitata nel Registro di sistema, esaminare il log ContainterRegistryLoginEvents. Questo log archivia gli eventi di autenticazione e lo stato, inclusi l'identità in ingresso e l'indirizzo IP. Eseguire una query sul log per [trovare gli errori di autenticazione del Registro di sistema.](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) 

Collegamenti correlati:

* [Log per la valutazione e il controllo diagnostici](container-registry-diagnostics-audit-logs.md)
* [Domande frequenti sul registro contenitori](container-registry-faq.md)
* [Procedure consigliate per Registro Azure Container](container-registry-best-practices.md)

## <a name="next-steps"></a>Passaggi successivi

Se il problema persiste, vedere le opzioni seguenti.

* Altri argomenti relativi alla risoluzione dei problemi del Registro di sistema includono:
  * [Risolvere i problemi di rete con il Registro di sistema](container-registry-troubleshoot-access.md)
  * [Risolvere i problemi relativi alle prestazioni del Registro di sistema](container-registry-troubleshoot-performance.md)
* [Opzioni di supporto della](https://azure.microsoft.com/support/community/) community
* [Domande e risposte Microsoft](/answers/products/)
* [Aprire un ticket di supporto:](https://azure.microsoft.com/support/create-ticket/) in base alle informazioni fornite, potrebbe essere eseguita una diagnostica rapida per gli errori di autenticazione nel registro