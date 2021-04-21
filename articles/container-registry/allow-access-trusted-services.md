---
title: Accedere al registro con restrizioni di rete usando un servizio di Azure attendibile
description: Abilitare un'istanza del servizio di Azure attendibile per accedere in modo sicuro a un registro contenitori con restrizioni di rete per eseguire il pull o il push di immagini
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 4b0d7feb223bcfcec4e8b2c786b211f4e3c3c3eb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785871"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Consentire ai servizi attendibili di accedere in modo sicuro a un registro contenitori con restrizioni di rete (anteprima)

Registro Azure Container possibile consentire a servizi di Azure attendibili di accedere a un registro configurato con regole di accesso alla rete. Quando sono consentiti servizi attendibili, un'istanza del servizio attendibile può ignorare in modo sicuro le regole di rete del Registro di sistema ed eseguire operazioni come le immagini pull o push. L'identità gestita dell'istanza del servizio viene usata per l'accesso e deve essere assegnata un ruolo di Azure ed eseguire l'autenticazione con il Registro di sistema.

Usare il Azure Cloud Shell o un'installazione locale dell'interfaccia della riga di comando di Azure per eseguire gli esempi di comando in questo articolo. Se si desidera usarlo in locale, è necessaria la versione 2.18 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Consentire l'accesso al Registro di sistema da servizi di Azure attendibili è una **funzionalità di** anteprima.

## <a name="limitations"></a>Limitazioni

* È necessario usare un'identità gestita assegnata dal sistema abilitata in un [servizio attendibile](#trusted-services) per accedere a un registro contenitori con restrizioni di rete. Le identità gestite assegnate dall'utente non sono attualmente supportate.
* L'autorizzazione dei servizi attendibili non si applica a un registro contenitori configurato con un [endpoint di servizio](container-registry-vnet.md). La funzionalità influisce solo sui registri limitati a un [endpoint privato](container-registry-private-link.md) o a cui sono applicate regole di [accesso IP](container-registry-access-selected-networks.md) pubblico. 

## <a name="about-trusted-services"></a>Informazioni sui servizi attendibili

Registro Azure Container ha un modello di sicurezza a più livelli, che supporta più configurazioni di rete che limitano l'accesso a un registro, tra cui:

* [Endpoint privato con collegamento privato di Azure](container-registry-private-link.md). Se configurato, l'endpoint privato di un registro è accessibile solo alle risorse all'interno della rete virtuale, usando indirizzi IP privati.  
* [Regole del firewall del](container-registry-access-selected-networks.md)Registro di sistema che consentono l'accesso all'endpoint pubblico del Registro di sistema solo da indirizzi IP pubblici o intervalli di indirizzi specifici. È anche possibile configurare il firewall per bloccare tutti gli accessi all'endpoint pubblico quando si usano endpoint privati.

Quando viene distribuito in una rete virtuale o configurato con regole del firewall, un registro nega l'accesso per impostazione predefinita a utenti o servizi esterni a tali origini. 

Diversi servizi di Azure multi-tenant operano da reti che non possono essere incluse in queste impostazioni di rete del Registro di sistema, impedendo loro di eseguire il pull o il push delle immagini nel registro. Designando alcune istanze del servizio come "attendibili", un proprietario del registro può consentire a determinate risorse di Azure di ignorare in modo sicuro le impostazioni di rete del registro per eseguire il pull o il push delle immagini. 

### <a name="trusted-services"></a>Servizi attendibili

Le istanze dei servizi seguenti possono accedere a un registro  contenitori con restrizioni di rete se l'impostazione Consenti servizi attendibili del registro è abilitata (impostazione predefinita). Nel corso del tempo verranno aggiunti altri servizi.

|Servizio attendibile  |Scenari di utilizzo supportati  |
|---------|---------|
|Attività di Registro Azure Container     | [Accedere a un registro diverso da un'attività di Registro Registro Controllo di accesso](container-registry-tasks-cross-registry-authentication.md)       |
|Machine Learning | [Distribuire o](../machine-learning/how-to-deploy-custom-docker-image.md) eseguire [il training](../machine-learning/how-to-train-with-custom-image.md) di un modello in un'area Machine Learning lavoro usando un'immagine del contenitore Docker personalizzata |
|Registro Azure Container | [Importare immagini da un altro Registro Azure Container](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> L'abilitazione dell'impostazione Consenti servizi attendibili non consente alle istanze di altri servizi gestiti di Azure, tra cui servizio app, Istanze di Azure Container e Centro sicurezza di Azure, di accedere a un registro contenitori con restrizioni di rete.

## <a name="allow-trusted-services---cli"></a>Consenti servizi attendibili - Interfaccia della riga di comando

Per impostazione predefinita, l'impostazione Consenti servizi attendibili è abilitata in un nuovo Registro Azure Container. Disabilitare o abilitare l'impostazione eseguendo [il comando az acr update.](/cli/azure/acr#az_acr_update)

Per disabilitare:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

Per abilitare l'impostazione in un registro esistente o in un registro in cui è già disabilitato:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Consenti servizi attendibili - Portale

Per impostazione predefinita, l'impostazione Consenti servizi attendibili è abilitata in un nuovo Registro Azure Container. 

Per disabilitare o riattivare l'impostazione nel portale:

1. Nel portale passare al registro contenitori.
1. In **Impostazioni** selezionare **Rete**. 
1. In **Consenti accesso alla rete** pubblica selezionare Reti **selezionate** o **Disabilitato.**
1. Eseguire una delle operazioni seguenti:
    * Per disabilitare l'accesso da parte di servizi attendibili, in **Eccezione del firewall** deselezionare Consenti ai servizi Microsoft attendibili di accedere a questo registro **contenitori.** 
    * Per consentire i servizi attendibili, in **Eccezione del firewall** selezionare Consenti ai servizi Microsoft attendibili di accedere a questo registro **contenitori.**
1. Selezionare **Salva**.

## <a name="trusted-services-workflow"></a>Flusso di lavoro dei servizi attendibili

Ecco un flusso di lavoro tipico per consentire a un'istanza di un servizio attendibile di accedere a un registro contenitori con restrizioni di rete.

1. Abilitare un'identità gestita [assegnata dal sistema per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) in un'istanza di uno dei servizi attendibili [per](#trusted-services) Registro Azure Container.
1. Assegnare all'identità [un ruolo di Azure](container-registry-roles.md) al registro. Ad esempio, assegnare il ruolo ACRPull per eseguire il pull delle immagini del contenitore.
1. Nel Registro di sistema con restrizioni di rete configurare l'impostazione per consentire l'accesso da parte di servizi attendibili.
1. Usare le credenziali dell'identità per eseguire l'autenticazione con il registro con restrizioni di rete. 
1. Eseguire il pull delle immagini dal registro o eseguire altre operazioni consentite dal ruolo.

### <a name="example-acr-tasks"></a>Esempio: Attività di ACR

Nell'esempio seguente viene illustrato l'utilizzo di Attività di ACR come servizio attendibile. Per informazioni dettagliate sull'attività, vedere Autenticazione tra più registro [in un'attività di Registro AzureCr usando un'identità](container-registry-tasks-cross-registry-authentication.md) gestita da Azure.

1. Creare o aggiornare un Registro Azure Container ed eseguire il [push di un'immagine di base](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) di esempio nel registro. Questo registro è il registro *di base* per lo scenario.
1. In un secondo Registro Azure Container definire [e](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) creare [un'attività](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) di Registro Azure Container per eseguire il pull di un'immagine dal registro di base. Abilitare un'identità gestita assegnata dal sistema durante la creazione dell'attività.
1. Assegnare all'identità [dell'attività un ruolo di Azure per accedere al registro di base.](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources) Ad esempio, assegnare il ruolo AcrPull, che dispone delle autorizzazioni per eseguire il pull delle immagini.
1. [Aggiungere le credenziali dell'identità gestita](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) all'attività.
1. Per verificare che l'attività bypassi le restrizioni di rete, [disabilitare l'accesso pubblico](container-registry-access-selected-networks.md#disable-public-network-access) nel Registro di sistema di base.
1. Eseguire l'attività. Se il registro di base e l'attività sono configurati correttamente, l'attività viene eseguita correttamente, perché il registro di base consente l'accesso.

Per testare la disabilitazione dell'accesso da parte di servizi attendibili:

1. Nel Registro di sistema di base disabilitare l'impostazione per consentire l'accesso da parte di servizi attendibili.
1. Eseguire di nuovo l'attività. In questo caso, l'esecuzione dell'attività ha esito negativo, perché il Registro di sistema di base non consente più l'accesso da parte dell'attività.

## <a name="next-steps"></a>Passaggi successivi

* Per limitare l'accesso a un registro usando un endpoint privato in una rete virtuale, vedere [Configurare un collegamento privato di Azure per un registro contenitori di Azure](container-registry-private-link.md).
* Per configurare le regole del firewall del Registro di sistema, vedere [Configurare le regole di rete IP pubblico.](container-registry-access-selected-networks.md)
