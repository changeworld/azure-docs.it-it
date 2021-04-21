---
title: Controllare l'integrità del Registro di sistema
description: Informazioni su come eseguire un comando di diagnostica rapido per identificare i problemi comuni durante l'uso di un Registro Azure Container, tra cui la configurazione e la connettività di Docker locale al Registro di sistema
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: fec05efe67f5c502f36ee90eec57ba283b15a4a0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761746"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Controllare l'integrità di un Registro Azure Container

Quando si usa un Registro Azure Container, è possibile che si verifichino occasionalmente problemi. Ad esempio, potrebbe non essere possibile eseguire il pull di un'immagine del contenitore a causa di un problema con Docker nell'ambiente locale. In caso contrario, un problema di rete potrebbe impedire la connessione al Registro di sistema. 

Come primo passaggio di diagnostica, eseguire il [comando az acr check-health][az-acr-check-health] per ottenere informazioni sull'integrità dell'ambiente e, facoltativamente, accedere a un registro di destinazione. Questo comando è disponibile nell'interfaccia della riga di comando di Azure versione 2.0.67 o successiva. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli].

Per altre indicazioni sulla risoluzione dei problemi del Registro di sistema, vedere:
* [Risolvere i problemi di accesso al Registro di sistema](container-registry-troubleshoot-login.md)
* [Risolvere i problemi di rete con il Registro di sistema](container-registry-troubleshoot-access.md)
* [Risolvere i problemi relativi alle prestazioni del Registro di sistema](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>Eseguire az acr check-health

Gli esempi seguenti illustrano diversi modi per eseguire il `az acr check-health` comando.

> [!NOTE]
> Se si esegue il comando in Azure Cloud Shell, l'ambiente locale non viene controllato. Tuttavia, è possibile controllare l'accesso a un registro di destinazione.

### <a name="check-the-environment-only"></a>Controllare solo l'ambiente

Per controllare il daemon Docker locale, la versione dell'interfaccia della riga di comando e la configurazione del client Helm, eseguire il comando senza parametri aggiuntivi:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Controllare l'ambiente e un registro di destinazione

Per controllare l'accesso a un registro ed eseguire controlli dell'ambiente locale, passare il nome di un registro di destinazione. Ad esempio:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Errore di segnalazione

Il comando registra le informazioni nell'output standard. Se viene rilevato un problema, vengono forniti un codice di errore e una descrizione. Per altre informazioni sui codici e sulle possibili soluzioni, vedere le informazioni [di riferimento sugli errori](container-registry-health-error-reference.md).

Per impostazione predefinita, il comando si arresta ogni volta che viene rilevato un errore. È anche possibile eseguire il comando in modo da ottenere l'output per tutti i controlli di integrità, anche se vengono rilevati errori. Aggiungere il `--ignore-errors` parametro , come illustrato negli esempi seguenti:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Output di esempio:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate sui codici di errore restituiti dal [comando az acr check-health,][az-acr-check-health] vedere le informazioni [di riferimento sugli errori del controllo integrità.](container-registry-health-error-reference.md)

Vedere le [domande](container-registry-faq.md) frequenti per le domande frequenti e altri problemi noti relativi Registro Azure Container.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
