---
title: Identità gestita nell'attività ACR
description: Abilitare un'identità gestita per le risorse di Azure in un'attività Registro Azure Container per consentire all'attività di accedere ad altre risorse di Azure, inclusi altri registri contenitori privati.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 19d63861a98884ff2f5103946c19e2226c4b14b7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781164"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Usare un'identità gestita di Azure nelle attività di Controllo di accesso 

Abilitare [un'identità gestita](../active-directory/managed-identities-azure-resources/overview.md) per le risorse di Azure in un'attività [ACR,](container-registry-tasks-overview.md)in modo che l'attività possa accedere ad altre risorse di Azure, senza dover fornire o gestire le credenziali. Ad esempio, usare un'identità gestita per abilitare un passaggio di attività per eseguire il pull o il push di immagini del contenitore in un altro registro.

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per abilitare un'identità gestita assegnata dall'utente o assegnata dal sistema in un'attività di Registro Azure. È possibile usare il Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure. Se si desidera usarlo in locale, è necessaria la versione 2.0.68 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

A scopo illustrativo, i comandi di esempio in questo articolo usano [az acr task create][az-acr-task-create] per creare un'attività di compilazione di immagini di base che abilita un'identità gestita. Per scenari di esempio per l'accesso alle risorse protette da un'attività di Controllo di accesso tramite un'identità gestita, vedere:

* [Autenticazione tra più registri](container-registry-tasks-cross-registry-authentication.md)
* [Accedere alle risorse esterne con i segreti archiviati in Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Perché usare un'identità gestita?

Un'identità gestita per le risorse di Azure fornisce ai servizi di Azure selezionati un'identità gestita automaticamente Azure Active Directory. È possibile configurare un'attività ACR con un'identità gestita in modo che l'attività possa accedere ad altre risorse di Azure protette, senza passare le credenziali nei passaggi dell'attività.

Esistono due tipi di identità gestite:

* *Identità assegnate dall'utente,* che è possibile assegnare a più risorse e rendere persistenti per tutto il tempo desiderato. Le identità assegnate dall'utente sono attualmente in anteprima.

* Identità *assegnata dal sistema,* che è univoca per una risorsa specifica, ad esempio un'attività ACR, e dura per la durata di tale risorsa.

È possibile abilitare uno o entrambi i tipi di identità in un'attività ACR. Concedere all'identità l'accesso a un'altra risorsa, proprio come qualsiasi entità di sicurezza. Quando l'attività viene eseguita, usa l'identità per accedere alla risorsa in tutti i passaggi dell'attività che richiedono l'accesso.

## <a name="steps-to-use-a-managed-identity"></a>Passaggi per usare un'identità gestita

Seguire questi passaggi di alto livello per usare un'identità gestita con un'attività di ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Facoltativo) Creare un'identità assegnata dall'utente

Se si prevede di usare un'identità assegnata dall'utente, usare un'identità esistente o crearne una usando l'interfaccia della riga di comando di Azure o altri strumenti di Azure. Ad esempio, usare il [comando az identity create.][az-identity-create] 

Se si prevede di usare solo un'identità assegnata dal sistema, ignorare questo passaggio. Quando si crea l'attività di Registro Controllo di accesso, si crea un'identità assegnata dal sistema.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Abilitare l'identità in un'attività di ACR

Quando si crea un'attività di Registro Controllo di accesso, abilitare facoltativamente un'identità assegnata dall'utente, un'identità assegnata dal sistema o entrambe. Ad esempio, passare il parametro `--assign-identity` quando si esegue il comando [az acr task create][az-acr-task-create] nell'interfaccia della riga di comando di Azure.

Per abilitare un'identità assegnata dal sistema, `--assign-identity` passare senza valore o `assign-identity [system]` . Il comando di esempio seguente crea un'attività Linux da un repository GitHub pubblico che compila l'immagine e abilita `hello-world` un'identità gestita assegnata dal sistema:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Per abilitare un'identità assegnata dall'utente, `--assign-identity` passare con un valore dell'ID *risorsa* dell'identità. Il comando di esempio seguente crea un'attività Linux da un repository GitHub pubblico che compila l'immagine e abilita `hello-world` un'identità gestita assegnata dall'utente:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

È possibile ottenere l'ID risorsa dell'identità eseguendo il [comando az identity show.][az-identity-show] L'ID risorsa per l'ID *myUserAssignedIdentity nel* gruppo di risorse *myResourceGroup* è nel formato seguente: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Concedere all'identità le autorizzazioni per accedere ad altre risorse di Azure

A seconda dei requisiti dell'attività, concedere all'identità le autorizzazioni per accedere ad altre risorse di Azure. Alcuni esempi:

* Assegnare all'identità gestita un ruolo con autorizzazioni pull, push e pull o altre autorizzazioni a un registro contenitori di destinazione in Azure. Per un elenco completo dei ruoli del Registro di sistema, [Registro Azure Container ruoli e autorizzazioni.](container-registry-roles.md) 
* Assegnare all'identità gestita un ruolo per leggere i segreti in un insieme di credenziali delle chiavi di Azure.

Usare [l'interfaccia della riga di](../role-based-access-control/role-assignments-cli.md) comando di Azure o altri strumenti di Azure per gestire l'accesso in base al ruolo alle risorse. Ad esempio, eseguire il [comando az role assignment create][az-role-assignment-create] per assegnare all'identità un ruolo alla risorsa. 

Nell'esempio seguente vengono assegnate a un'identità gestita le autorizzazioni per eseguire il pull da un registro contenitori. Il comando specifica *l'ID entità dell'identità* dell'attività e l'ID *risorsa* del Registro di sistema di destinazione.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Facoltativo) Aggiungere credenziali all'attività

Se l'attività richiede credenziali per eseguire il pull o il push di immagini in un altro registro personalizzato o per accedere ad altre risorse, aggiungere le credenziali all'attività. Eseguire il [comando az acr task credential add][az-acr-task-credential-add] per aggiungere le credenziali e passare il parametro per indicare che l'identità può accedere alle `--use-identity` credenziali. 

Ad esempio, per aggiungere le credenziali per un'identità assegnata dal sistema per l'autenticazione con il registro Azure Container *targetregistry,* passare `use-identity [system]` :

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Per aggiungere le credenziali per un'identità assegnata dall'utente per l'autenticazione con il registro di *destinazioneregistry*, passare con un valore `use-identity` *dell'ID client* dell'identità. Ad esempio:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

È possibile ottenere l'ID client dell'identità eseguendo il [comando az identity show.][az-identity-show] L'ID client è un GUID nel formato `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .

### <a name="5-run-the-task"></a>5. Eseguire l'attività

Dopo aver configurato un'attività con un'identità gestita, eseguire l'attività. Ad esempio, per testare una delle attività create in questo articolo, attivarla manualmente usando il [comando az acr task run.][az-acr-task-run] Se sono stati configurati trigger di attività automatizzati aggiuntivi, l'attività viene eseguita quando viene attivata automaticamente.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come abilitare e usare un'identità gestita assegnata dall'utente o assegnata dal sistema in un'attività ACR. Per gli scenari di accesso alle risorse protette da un'attività ACR usando un'identità gestita, vedere:

* [Autenticazione tra più registri](container-registry-tasks-cross-registry-authentication.md)
* [Accedere alle risorse esterne con i segreti archiviati in Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[azure-cli-install]: /cli/azure/install-azure-cli
