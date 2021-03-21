---
title: Proteggere i servizi Web usando TLS
titleSuffix: Azure Machine Learning
description: Informazioni su come abilitare HTTPS con TLS versione 1,2 per proteggere un servizio Web distribuito tramite Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.date: 03/11/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 71cb2e9e112c49d77a2a0b47c24c49cabfa86589
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149019"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Usare TLS per proteggere un servizio Web tramite Azure Machine Learning


Questo articolo illustra come proteggere un servizio Web distribuito tramite Azure Machine Learning.

Usare [https](https://en.wikipedia.org/wiki/HTTPS) per limitare l'accesso ai servizi Web e proteggere i dati che i client inviano. HTTPS consente di proteggere le comunicazioni tra un client e un servizio Web mediante la crittografia delle comunicazioni tra i due. La crittografia USA [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS è talvolta ancora indicato come *Secure Sockets Layer* (SSL), che era il predecessore di TLS.

> [!TIP]
> Il Azure Machine Learning SDK usa il termine "SSL" per le proprietà correlate alle comunicazioni sicure. Questo non significa che il servizio Web non usa *TLS*. SSL è semplicemente un termine riconosciuto in modo più comune.
>
> In particolare, i servizi Web distribuiti tramite Azure Machine Learning supportano TLS versione 1,2 per AKS e ACI. Per le distribuzioni ACI, se si utilizza una versione precedente di TLS, è consigliabile eseguire nuovamente la distribuzione per ottenere la versione più recente di TLS.
>
> Il protocollo TLS versione 1,3 per l'inferenza Azure Machine Learning-AKS non è supportato.

TLS e SSL si basano entrambi sui *certificati digitali*, che contribuiscono alla crittografia e alla verifica dell'identità. Per ulteriori informazioni sul funzionamento dei certificati digitali, vedere l'argomento di Wikipedia [infrastruttura a chiave pubblica](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Se non si usa HTTPS per il servizio Web, i dati inviati da e verso il servizio potrebbero essere visibili ad altri utenti su Internet.
>
> HTTPS consente inoltre al client di verificare l'autenticità del server a cui si connette. Questa funzionalità protegge i client [da attacchi man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) .

Questo è il processo generale per proteggere un servizio Web:

1. Immettere un nome di dominio.

2. Ottenere un certificato digitale.

3. Distribuire o aggiornare il servizio Web con la funzionalità TLS abilitata.

4. Aggiornare il DNS in modo che punti al servizio Web.

> [!IMPORTANT]
> Se si esegue la distribuzione in Azure Kubernetes Service (AKS), è possibile acquistare un certificato personalizzato o usare un certificato fornito da Microsoft. Se si usa un certificato di Microsoft, non è necessario ottenere un nome di dominio o un certificato TLS/SSL. Per altre informazioni, vedere la sezione [abilitare TLS e distribuire](#enable) in questo articolo.

Le differenze tra le [destinazioni di distribuzione](how-to-deploy-and-where.md)sono minime.

## <a name="get-a-domain-name"></a>Immettere un nome di dominio

Se non si dispone già di un nome di dominio, acquistarne uno da un *registrar*. Il processo e il prezzo variano tra i registrar. Il registrar fornisce gli strumenti per gestire il nome di dominio. Questi strumenti vengono usati per eseguire il mapping di un nome di dominio completo (FQDN) (ad esempio www \. contoso.com) all'indirizzo IP che ospita il servizio Web.

## <a name="get-a-tlsssl-certificate"></a>Ottenere un certificato TLS/SSL

Esistono diversi modi per ottenere un certificato TLS/SSL (certificato digitale). Il più comune consiste nell'acquistarne uno da un' *autorità di certificazione* (CA). Indipendentemente dalla posizione in cui si ottiene il certificato, sono necessari i file seguenti:

* Un **certificato**. Il certificato deve contenere la catena di certificati completa e deve essere "con codifica PEM".
* Una **chiave**. La chiave deve anche essere codificata con PEM.

Quando si richiede un certificato, è necessario fornire il nome di dominio completo dell'indirizzo che si prevede di utilizzare per il servizio Web (ad esempio, www \. contoso.com). Per verificare l'identità del servizio Web, l'indirizzo indicato nel certificato e l'indirizzo utilizzato dai client vengono confrontati. Se gli indirizzi non corrispondono, il client riceve un messaggio di errore.

> [!TIP]
> Se l'autorità di certificazione non può fornire il certificato e la chiave come file con codifica PEM, è possibile usare un'utilità come [openssl](https://www.openssl.org/) per modificare il formato.

> [!WARNING]
> Usare i certificati *autofirmati* solo per lo sviluppo. Non usarli negli ambienti di produzione. I certificati autofirmati possono causare problemi nelle applicazioni client. Per ulteriori informazioni, vedere la documentazione relativa alle librerie di rete utilizzate dall'applicazione client.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> Abilitare TLS e distribuire

**Per la distribuzione di AKS**, è possibile abilitare la terminazione TLS quando si [Crea o si connette un cluster AKS](how-to-create-attach-kubernetes.md) nell'area di lavoro AML. Al momento della distribuzione del modello AKS, è possibile disabilitare la terminazione TLS con l'oggetto di configurazione della distribuzione. in caso contrario, per impostazione predefinita, per tutte le distribuzioni del modello AKS sarà abilitata la terminazione TLS in fase di creazione o associazione

Per la distribuzione di ACI, è possibile abilitare la terminazione TLS in fase di distribuzione del modello con l'oggetto di configurazione della distribuzione.


### <a name="deploy-on-azure-kubernetes-service"></a>Eseguire la distribuzione nel servizio Azure Kubernetes

  > [!NOTE]
  > Le informazioni contenute in questa sezione si applicano anche quando si distribuisce un servizio Web sicuro per la finestra di progettazione. Se non si ha familiarità con l'uso di Python SDK, vedere [che cos'è l'SDK di Azure Machine Learning per Python?](/python/api/overview/azure/ml/intro).

Quando si [Crea o si associa un cluster AKS](how-to-create-attach-kubernetes.md) nell'area di lavoro AML, è possibile abilitare la terminazione TLS con gli oggetti di configurazione **[AksCompute.provisioning_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** e **[AksCompute.attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Entrambi i metodi restituiscono un oggetto di configurazione con un metodo **enable_ssl** ed è possibile usare **enable_ssl** metodo per abilitare TLS.

È possibile abilitare TLS con il certificato Microsoft o con un certificato personalizzato acquistato dalla CA. 

* **Quando si usa un certificato di Microsoft**, è necessario usare il parametro *leaf_domain_label* . Questo parametro genera il nome DNS per il servizio. Ad esempio, il valore "contoso" crea un nome di dominio "Contoso \<six-random-characters> . \<azureregion> .. cloudapp.azure.com ", dove \<azureregion> è l'area che contiene il servizio. Facoltativamente, è possibile usare il parametro *overwrite_existing_domain* per sovrascrivere la *leaf_domain_label* esistente. Nell'esempio seguente viene illustrato come creare una configurazione che Abilita un TLS con il certificato Microsoft:

    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```
    > [!IMPORTANT]
    > Quando si usa un certificato di Microsoft, non è necessario acquistare un certificato o un nome di dominio personalizzato.

    > [!WARNING]
    > Se il cluster AKS è configurato con un servizio di bilanciamento del carico interno, l'uso di un certificato fornito da Microsoft __non è supportato__ ed è necessario usare un certificato personalizzato per abilitare TLS.

* **Quando si usa un certificato personalizzato acquistato**, usare i parametri *ssl_cert_pem_file*, *ssl_key_pem_file* e *ssl_cname* . Nell'esempio seguente viene illustrato come usare i file con estensione PEM per creare una configurazione che usa un certificato TLS/SSL acquistato:
 
    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")

    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Per ulteriori informazioni su *enable_ssl*, vedere [AksProvisioningConfiguration.enable_ssl ()](/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) e [AksAttachConfiguration.enable_ssl ()](/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Distribuire in Istanze di Azure Container

Quando si esegue la distribuzione in istanze di contenitore di Azure, si forniscono valori per i parametri correlati a TLS, come illustrato nel frammento di codice seguente:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Per ulteriori informazioni, vedere [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Aggiorna DNS

Per una distribuzione di AKS con certificato personalizzato o una distribuzione di ACI, è necessario aggiornare il record DNS in modo che punti all'indirizzo IP dell'endpoint di assegnazione dei punteggi.

  > [!IMPORTANT]
  > Quando si usa un certificato di Microsoft per la distribuzione di AKS, non è necessario aggiornare manualmente il valore DNS per il cluster. Il valore deve essere impostato automaticamente.

È possibile seguire la procedura seguente per aggiornare il record DNS per il nome di dominio personalizzato:
* Ottenere l'indirizzo IP dell'endpoint di assegnazione dei punteggi dall'URI dell'endpoint di assegnazione dei punteggi, che è in genere nel formato *http://104.214.29.152:80/api/v1/service/<service-name>/score* . 
* Usare gli strumenti del registrar per aggiornare il record DNS per il nome di dominio. Il record deve puntare all'indirizzo IP dell'endpoint di assegnazione dei punteggi.
* Dopo l'aggiornamento del record DNS, è possibile convalidare la risoluzione DNS usando il comando *nslookup Custom-Domain-Name* . Se il record DNS viene aggiornato correttamente, il nome di dominio personalizzato punterà all'indirizzo IP dell'endpoint di assegnazione dei punteggi.
* È possibile che si verifichi un ritardo di minuti o ore prima che i client possano risolvere il nome di dominio, a seconda del registrar e della "durata (TTL) configurata per il nome di dominio.


## <a name="update-the-tlsssl-certificate"></a>Aggiornare il certificato TLS/SSL

I certificati TLS/SSL scadono e devono essere rinnovati. Questa situazione si verifica in genere ogni anno. Usare le informazioni nelle sezioni riportate di seguito per aggiornare e rinnovare il certificato per i modelli distribuiti nel servizio Azure Kubernetes:

### <a name="update-a-microsoft-generated-certificate"></a>Aggiornare un certificato generato da Microsoft

Se il certificato è stato originariamente generato da Microsoft (quando si usa il *leaf_domain_label* per creare il servizio), **verrà rinnovato automaticamente** quando necessario. Se si vuole rinnovarlo manualmente, usare uno degli esempi seguenti per aggiornare il certificato:

> [!IMPORTANT]
> * Se il certificato esistente è ancora valido, usare `renew=True` (SDK) o `--ssl-renew` (CLI) per forzare la configurazione a rinnovarla. Ad esempio, se il certificato esistente è ancora valido per 10 giorni e non si usa `renew=True` , il certificato potrebbe non essere rinnovato.
> * Quando il servizio è stato distribuito in origine, `leaf_domain_label` viene usato per creare un nome DNS usando il modello `<leaf-domain-label>######.<azure-region>.cloudapp.azure.com` . Per mantenere il nome esistente (incluse le 6 cifre generate originariamente), usare il `leaf_domain_label` valore originale. Non includere le 6 cifre generate.

**Usare l'SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Usare l'interfaccia della riga di comando**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

Per ulteriori informazioni, vedere la documentazione di riferimento seguente:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration)

### <a name="update-custom-certificate"></a>Aggiorna certificato personalizzato

Se il certificato è stato originariamente generato da un'autorità di certificazione, attenersi alla procedura seguente:

1. Utilizzare la documentazione fornita dall'autorità di certificazione per rinnovare il certificato. Questo processo crea nuovi file di certificato.

1. Usare l'SDK o l'interfaccia della riga di comando per aggiornare il servizio con il nuovo certificato:

    **Usare l'SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Usare l'interfaccia della riga di comando**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Per ulteriori informazioni, vedere la documentazione di riferimento seguente:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration)

## <a name="disable-tls"></a>Disabilitare TLS

Per disabilitare TLS per un modello distribuito nel servizio Azure Kubernetes, creare un `SslConfiguration` con `status="Disabled"` , quindi eseguire un aggiornamento:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Passaggi successivi
Viene illustrato come:
+ [Utilizzare un modello di apprendimento automatico distribuito come servizio Web](how-to-consume-web-service.md)
+ [Panoramica dell'isolamento e della privacy della rete virtuale](how-to-network-security-overview.md)