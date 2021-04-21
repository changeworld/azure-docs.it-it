---
title: Opzioni di autenticazione del Registro di sistema
description: Opzioni di autenticazione per un registro azure container privato, tra cui l'accesso con un'identità Azure Active Directory, l'uso di entità servizio e l'uso di credenziali di amministratore facoltative.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: 097a322260e4c4f55d4e0d7e3e107abdd15a3b8a
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831637"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Eseguire l'autenticazione con un Registro Azure Container

Esistono diversi modi per eseguire l'autenticazione con un Registro Azure Container, ognuno dei quali è applicabile a uno o più scenari di utilizzo del registro.

I modi consigliati includono l'autenticazione a un registro direttamente tramite un singolo account di accesso oppure le applicazioni e gli orchestratori di contenitori possono eseguire l'autenticazione automatica o "headless" usando un'entità servizio Azure Active Directory [](#individual-login-with-azure-ad)(Azure AD). [](#service-principal)

## <a name="authentication-options"></a>Opzioni di autenticazione

Nella tabella seguente sono elencati i metodi di autenticazione disponibili e gli scenari tipici. Per informazioni dettagliate, vedere contenuto collegato.

| Metodo                               | Come eseguire l'autenticazione                                           | Scenari                                                            | Controllo degli accessi in base al ruolo di Azure                             | Limitazioni                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Identità ad Active Directory singola](#individual-login-with-azure-ad)                | `az acr login` nell'interfaccia della riga di comando di Azure                             | Push/pull interattivo di sviluppatori, tester                                    | Sì                              | Il token AD deve essere rinnovato ogni 3 ore     |
| [Entità servizio di Active Directory](#service-principal)                  | `docker login`<br/><br/>`az acr login` nell'interfaccia della riga di comando di Azure<br/><br/> Impostazioni di accesso al Registro di sistema nelle API o negli strumenti<br/><br/> [Segreto pull kubernetes](container-registry-auth-kubernetes.md)                                           | Push automatico dalla pipeline CI/CD<br/><br/> Pull automatico in Azure o servizi esterni  | Sì                              | La scadenza predefinita della password SP è 1 anno       |                                                           
| [Eseguire l'integrazione con il server del server del codice AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Collegare il Registro di sistema al momento della creazione o dell'aggiornamento del cluster del servizio Web Dia  | Pull automatico nel cluster del servizio Web Disattesa                                                  | No, solo accesso pull             | Disponibile solo con il cluster del servizio Web Disack            |
| [Identità gestita per le risorse di Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` nell'interfaccia della riga di comando di Azure                                       | Push automatico dalla pipeline CI/CD di Azure<br/><br/> Pull automatico nei servizi di Azure<br/><br/>   | Sì                              | Usare solo da servizi di Azure selezionati che [supportano le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Utente amministratore](#admin-account)                            | `docker login`                                          | Push/pull interattivo da parte di singoli sviluppatori o tester<br/><br/>Distribuzione dell'immagine nel portale dal registro Servizio app di Azure o Istanze di Azure Container                      | No, sempre accesso pull e push  | Singolo account per registro, non consigliato per più utenti         |
| [Token di accesso con ambito repository](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` nell'interfaccia della riga di comando di Azure   | Push interattivo/pull nel repository da parte di singoli sviluppatori o tester<br/><br/> Push automatico/pull nel repository per singolo sistema o dispositivo esterno                  | Sì                              | Non attualmente integrato con l'identità di Active Directory  |

## <a name="individual-login-with-azure-ad"></a>Accesso individuale con Azure AD

Quando si usa direttamente il registro, ad esempio per eseguire il pull e il push delle immagini da una workstation di sviluppo a un registro creato, eseguire l'autenticazione usando la singola identità di Azure. Accedere all'interfaccia [della riga di](/cli/azure/install-azure-cli) comando di Azure con [az login](/cli/azure/reference-index#az_login)e quindi eseguire il comando [az acr login:](/cli/azure/acr#az_acr_login)

```azurecli
az login
az acr login --name <acrName>
```

Quando si esegue l'accesso con `az acr login`, l'interfaccia della riga di comando usa il token creato con l'esecuzione di `az login` per l'autenticazione della sessione con il registro. Per completare il flusso di autenticazione, l'interfaccia della riga di comando di Docker e il daemon Docker devono essere installati e in esecuzione nell'ambiente. `az acr login` usa il client Docker per impostare un token Azure Active Directory nel `docker.config` file . Dopo aver effettuato l'accesso in questo modo, le credenziali vengono memorizzate nella cache e i successivi comandi `docker` nella sessione non richiedono il nome utente o la password.

> [!TIP]
> Usare anche per autenticare una singola identità quando si vuole eseguire il push o il pull di artefatti diversi da immagini Docker nel registro, ad esempio `az acr login` [artefatti OCI.](container-registry-oci-artifacts.md)  

Per l'accesso al Registro di sistema, il token usato da è valido per 3 ore, quindi è consigliabile accedere sempre al Registro di sistema prima di `az acr login` eseguire un  `docker` comando. In caso di scadenza del token, è possibile aggiornarlo usando di nuovo il comando `az acr login` per eseguire nuovamente l'autenticazione. 

L'uso di con le identità di Azure offre il controllo degli accessi in base al ruolo di `az acr login` [Azure.](../role-based-access-control/role-assignments-portal.md) Per alcuni scenari, è possibile accedere a un registro con la propria identità personale in Azure AD o configurare altri utenti di Azure con autorizzazioni e ruoli di [Azure specifici.](container-registry-roles.md) Per scenari tra servizi o per gestire le esigenze di un gruppo di lavoro o di un flusso di lavoro di sviluppo in cui non si vuole gestire l'accesso singolo, è anche possibile accedere con un'identità gestita per le risorse di [Azure.](container-registry-authentication-managed-identity.md)

### <a name="az-acr-login-with---expose-token"></a>az acr login with --expose-token

In alcuni casi, potrebbe essere necessario eseguire `az acr login` l'autenticazione con quando il daemon Docker non è in esecuzione nell'ambiente. Ad esempio, potrebbe essere necessario eseguire in uno script in Azure Cloud Shell, che fornisce l'interfaccia della riga di comando di Docker, ma non esegue il `az acr login` daemon Docker.

Per questo scenario, eseguire `az acr login` prima con il parametro `--expose-token` . Questa opzione espone un token di accesso anziché accedere tramite l'interfaccia della riga di comando di Docker.

```azurecli
az acr login --name <acrName> --expose-token
```

L'output visualizza il token di accesso abbreviato qui:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 
Per l'autenticazione del Registro di sistema, è consigliabile archiviare le credenziali del token in un percorso sicuro e seguire le procedure consigliate per gestire le credenziali [di accesso docker.](https://docs.docker.com/engine/reference/commandline/login/) Ad esempio, archiviare il valore del token in una variabile di ambiente:

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

Eseguire quindi `docker login` , passando come nome utente e usando il token di accesso come `00000000-0000-0000-0000-000000000000` password:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```

## <a name="service-principal"></a>Entità servizio

Se si assegna un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) al registro, l'applicazione o il servizio può usarla per eseguire l'autenticazione headless. Le entità servizio consentono il [controllo degli](../role-based-access-control/role-assignments-portal.md) accessi in base al ruolo di Azure a un registro ed è possibile assegnare più entità servizio a un registro. L'uso di più entità servizio consente di definire un accesso diverso per applicazioni diverse.

I ruoli disponibili per un registro contenitori includono:

* **AcrPull**: pull

* **AcrPush**: pull e push

* **Proprietario:** pull, push e assegnazione di ruoli ad altri utenti

Per un elenco completo dei ruoli, vedere [Ruoli e autorizzazioni di Registro Azure Container](container-registry-roles.md).

Per gli script dell'interfaccia della riga di comando per creare un'entità servizio per l'autenticazione con un Registro Azure Container e altre indicazioni, vedere [Registro Azure Container'autenticazione](container-registry-auth-service-principal.md)con entità servizio .

## <a name="admin-account"></a>Account amministratore

Ogni registro contenitori include un account utente amministratore che, per impostazione predefinita, è disabilitato. È possibile abilitare l'utente amministratore e gestirne le credenziali nel portale di Azure oppure tramite l'interfaccia della riga di comando o altri strumenti di Azure. L'account amministratore dispone di autorizzazioni complete per il Registro di sistema.

L'account amministratore è attualmente necessario per alcuni scenari per distribuire un'immagine da un registro contenitori a determinati servizi di Azure. Ad esempio, l'account amministratore è necessario quando si usa il portale di Azure per distribuire un'immagine del contenitore da un registro direttamente in Istanze di Azure Container [o](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) app Web di Azure [per contenitori](container-registry-tutorial-deploy-app.md).

> [!IMPORTANT]
> L'account amministratore è pensato per consentire l'accesso al registro a un singolo utente, principalmente a scopo di test. Non è consigliabile condividere le credenziali dell'account amministratore tra più utenti. Tutti gli utenti che si autenticano con l'account amministratore vengono visualizzati come un unico utente con accesso di tipo push e pull al registro. Se si modifica o si disattiva questo account, tutti gli utenti che ne usano le credenziali non potranno più accedere al registro. Negli scenari di tipo headless è consigliabile che gli utenti e le entità servizio abbiano una propria identità.
>

L'account amministratore è dotato di due password, entrambe rigenerabili. L'uso di due password consente di mantenere la connessione al registro usando una password mentre si rigenera l'altra. Se l'account amministratore è abilitato, è possibile passare il nome utente e una password al comando `docker login` quando richiesto, per eseguire l'autenticazione di base al registro. Ad esempio:

```
docker login myregistry.azurecr.io 
```

Per le procedure consigliate per gestire le credenziali di accesso, vedere le informazioni di riferimento sul [comando docker login.](https://docs.docker.com/engine/reference/commandline/login/)

Per consentire a un utente amministratore di accedere a un registro esistente, è possibile usare il parametro `--admin-enabled` del comando [az acr update](/cli/azure/acr#az_acr_update) nell'interfaccia della riga di comando di Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Per abilitare l'utente amministratore nel portale di Azure, passare al registro interessato, selezionare **Chiavi di accesso** in **IMPOSTAZIONI**, quindi selezionare **Abilita** in **Utente amministratore**.

![Abilitare l'interfaccia utente dell'utente amministratore nel portale di Azure][auth-portal-01]

## <a name="next-steps"></a>Passaggi successivi

* [Effettuare il push della prima immagine tramite l'interfaccia della riga di comando di Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
