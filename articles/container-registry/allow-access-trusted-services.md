---
title: Accedere al registro di sistema con restrizioni di rete usando un servizio di Azure attendibile
description: Abilitare un'istanza del servizio di Azure attendibile per accedere in modo sicuro a un registro contenitori con restrizioni di rete per eseguire il pull o il push di immagini
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 2e6b6ee3736f98f53ebb0aa43d707d42ba4cc058
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716483"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Consenti ai servizi attendibili di accedere in modo sicuro a un registro contenitori con restrizioni di rete (anteprima)

Azure Container Registry può consentire ai servizi di Azure attendibili Select di accedere a un registro configurato con le regole di accesso alla rete. Quando i servizi attendibili sono consentiti, un'istanza del servizio attendibile può ignorare in modo sicuro le regole di rete del registro di sistema ed eseguire operazioni quali le immagini pull o push. L'identità gestita dell'istanza del servizio viene usata per l'accesso e deve essere assegnata a un ruolo di Azure ed eseguire l'autenticazione con il registro di sistema.

Usare il Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure per eseguire gli esempi di comando in questo articolo. Se si vuole usarlo localmente, è richiesta la versione 2,18 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Consentire l'accesso al registro da servizi di Azure attendibili è una funzionalità di **Anteprima** .

## <a name="limitations"></a>Limitazioni

* Per accedere a un registro contenitori con restrizioni di rete, è necessario usare un'identità gestita assegnata dal sistema abilitata in un [servizio attendibile](#trusted-services) . Le identità gestite assegnate dall'utente non sono attualmente supportate.
* Consentire i servizi attendibili non si applica a un registro contenitori configurato con un [endpoint del servizio](container-registry-vnet.md). La funzionalità interessa solo i registri limitati con un [endpoint privato](container-registry-private-link.md) o con [regole di accesso IP pubblico](container-registry-access-selected-networks.md) applicate. 

## <a name="about-trusted-services"></a>Informazioni sui servizi attendibili

Azure Container Registry dispone di un modello di sicurezza a più livelli, che supporta più configurazioni di rete che limitano l'accesso a un registro, tra cui:

* [Endpoint privato con collegamento privato di Azure](container-registry-private-link.md). Quando è configurato, un endpoint privato del registro di sistema è accessibile solo alle risorse all'interno della rete virtuale, usando indirizzi IP privati.  
* [Regole del firewall del registro di sistema](container-registry-access-selected-networks.md), che consentono l'accesso all'endpoint pubblico del registro di sistema solo da indirizzi IP pubblici o intervalli di indirizzi specifici. È anche possibile configurare il firewall in modo da bloccare tutti gli accessi all'endpoint pubblico quando si usano endpoint privati.

Quando viene distribuita in una rete virtuale o configurata con regole del firewall, un registro di sistema nega l'accesso per impostazione predefinita a utenti o servizi esterni a tali origini. 

Molti servizi di Azure multi-tenant operano da reti che non possono essere incluse nelle impostazioni di rete del registro di sistema, impedendo loro di eseguire il pull o il push delle immagini nel registro di sistema. Designando determinate istanze del servizio come "attendibili", un proprietario del registro di sistema può consentire le risorse di Azure SELECT per ignorare in modo sicuro le impostazioni di rete del registro di sistema per eseguire il pull o il push delle immagini. 

### <a name="trusted-services"></a>Servizi attendibili

Le istanze dei servizi seguenti possono accedere a un registro contenitori con restrizioni di rete se l'impostazione **Consenti servizi attendibili** del registro di sistema è abilitata (impostazione predefinita). Verranno aggiunti altri servizi nel tempo.

|Servizio attendibile  |Scenari di utilizzo supportati  |
|---------|---------|
|Attività di Registro Azure Container     | [Accedere a un registro diverso da un'attività ACR](container-registry-tasks-cross-registry-authentication.md)       |
|Machine Learning | [Distribuire](../machine-learning/how-to-deploy-custom-docker-image.md) o eseguire il [Training](../machine-learning/how-to-train-with-custom-image.md) di un modello in un'area di lavoro Machine Learning usando un'immagine del contenitore Docker personalizzata |
|Registro Azure Container | [Importa immagini da un altro registro contenitori di Azure](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Attualmente, abilitando l'impostazione Consenti servizi attendibili, non sono consentite istanze di altri servizi di Azure gestiti, tra cui il servizio app, istanze di contenitore di Azure e il Centro sicurezza di Azure per accedere a un registro contenitori con restrizioni di rete.

## <a name="allow-trusted-services---cli"></a>Consenti servizi attendibili-interfaccia della riga di comando

Per impostazione predefinita, l'impostazione Consenti servizi attendibili è abilitata in un nuovo registro contenitori di Azure. Disabilitare o abilitare l'impostazione eseguendo il comando [AZ ACR Update](/cli/azure/acr#az-acr-update) .

Per disabilitare:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

Per abilitare l'impostazione in un registro di sistema esistente o in un registro di sistema in cui è già disabilitata:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Consenti servizi attendibili-portale

Per impostazione predefinita, l'impostazione Consenti servizi attendibili è abilitata in un nuovo registro contenitori di Azure. 

Per disabilitare o riabilitare l'impostazione nel portale:

1. Nel portale passare al registro contenitori.
1. In **Impostazioni** selezionare **Rete**. 
1. In **Consenti accesso alla rete pubblica** selezionare **reti selezionate** o **disabilitate**.
1. Eseguire una delle operazioni seguenti:
    * Per disabilitare l'accesso da servizi attendibili, in **eccezione firewall** deselezionare **Consenti ai servizi Microsoft attendibili di accedere al registro contenitori**. 
    * Per consentire servizi attendibili, in **eccezione firewall** selezionare **Consenti ai servizi Microsoft attendibili di accedere al registro contenitori**.
1. Selezionare **Salva**.

## <a name="trusted-services-workflow"></a>Flusso di lavoro dei servizi attendibili

Di seguito è riportato un flusso di lavoro tipico per consentire a un'istanza di un servizio attendibile di accedere a un registro contenitori con restrizioni di rete.

1. Abilitare un'identità gestita assegnata dal sistema [per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) in un'istanza di uno dei [Servizi attendibili](#trusted-services) per Azure container Registry.
1. Assegnare l'identità a un [ruolo di Azure](container-registry-roles.md) al registro di sistema. Ad esempio, assegnare il ruolo ACRPull per le immagini del contenitore di pull.
1. Nel registro di sistema con restrizioni di rete configurare l'impostazione per consentire l'accesso da parte di servizi attendibili.
1. Usare le credenziali di identità per l'autenticazione con il registro di sistema con restrizioni di rete. 
1. Estrarre le immagini dal registro di sistema o eseguire altre operazioni consentite dal ruolo.

### <a name="example-acr-tasks"></a>Esempio: attività ACR

Nell'esempio seguente viene illustrato l'uso di attività ACR come servizio attendibile. Per informazioni dettagliate sulle attività, vedere [autenticazione tra registri in un'attività ACR usando un'identità gestita da Azure](container-registry-tasks-cross-registry-authentication.md) .

1. Creare o aggiornare un registro contenitori di Azure ed effettuare il [push di un'immagine di base di esempio](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) nel registro di sistema. Questo registro di sistema è il *Registro di base* per lo scenario.
1. In un secondo registro contenitori di Azure, [definire](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) e [creare](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) un'attività ACR per eseguire il pull di un'immagine dal registro di sistema di base. Abilitare un'identità gestita assegnata dal sistema durante la creazione dell'attività.
1. Assegnare l'identità dell'attività [a un ruolo di Azure per accedere al registro di sistema di base](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources). Ad esempio, assegnare il ruolo AcrPull, che dispone delle autorizzazioni per le immagini pull.
1. [Aggiungere le credenziali di identità gestite](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) all'attività.
1. Per verificare che l'attività ignori le restrizioni di rete, [disabilitare l'accesso pubblico](container-registry-access-selected-networks.md#disable-public-network-access) nel registro di sistema di base.
1. Eseguire l'attività. Se il registro di sistema di base e l'attività sono configurati correttamente, l'attività viene eseguita correttamente, perché il registro di base consente l'accesso.

Per testare la disabilitazione dell'accesso da servizi attendibili:

1. Nel registro di sistema di base disabilitare l'impostazione per consentire l'accesso da parte di servizi attendibili.
1. Eseguire di nuovo l'attività. In questo caso, l'esecuzione dell'attività non riesce perché il registro di sistema di base non consente più l'accesso da parte dell'attività.

## <a name="next-steps"></a>Passaggi successivi

* Per limitare l'accesso a un registro usando un endpoint privato in una rete virtuale, vedere [Configurare un collegamento privato di Azure per un registro contenitori di Azure](container-registry-private-link.md).
* Per configurare le regole del firewall del registro di sistema, vedere [configurare le regole di rete IP pubblico](container-registry-access-selected-networks.md).
