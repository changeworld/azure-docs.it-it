---
title: Proteggere il back-end di gestione API usando l'autenticazione del certificato client
titleSuffix: Azure API Management
description: Informazioni su come gestire i certificati client e proteggere i servizi back-end usando l'autenticazione con certificati client in gestione API di Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.openlocfilehash: 2e4a398ab71878134887fb8fba025cd8aa6122ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492839"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Proteggere i servizi back-end usando l'autenticazione con certificati client in gestione API di Azure

Gestione API consente di proteggere l'accesso al servizio back-end di un'API usando i certificati client. Questa guida illustra come gestire i certificati in un'istanza del servizio gestione API di Azure usando il portale di Azure. Viene inoltre illustrato come configurare un'API per l'utilizzo di un certificato per accedere a un servizio back-end.

È anche possibile gestire i certificati di gestione API usando l' [API REST di gestione API](/rest/api/apimanagement/2020-06-01-preview/certificate).

## <a name="certificate-options"></a>Opzioni certificato

Gestione API offre due opzioni per gestire i certificati usati per proteggere l'accesso ai servizi back-end:

* Fare riferimento a un certificato gestito in [Azure Key Vault](../key-vault/general/overview.md) 
* Aggiungere un file di certificato direttamente in gestione API

È consigliabile usare i certificati dell'insieme di credenziali delle chiavi perché consente di migliorare la sicurezza di gestione API:

* I certificati archiviati negli insiemi di credenziali delle chiavi possono essere riutilizzati tra i servizi
* I [criteri di accesso](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) granulari possono essere applicati ai certificati archiviati negli insiemi di credenziali delle chiavi
* I certificati aggiornati nell'insieme di credenziali delle chiavi vengono ruotati automaticamente in gestione API. Dopo l'aggiornamento nell'insieme di credenziali delle chiavi, un certificato in gestione API viene aggiornato entro 4 ore. È anche possibile aggiornare manualmente il certificato usando il portale di Azure o tramite l'API REST di gestione.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Se non è ancora stata creata un'istanza del servizio gestione API, vedere [creare un'istanza del servizio gestione API][Create an API Management service instance].
* È necessario che il servizio back-end sia configurato per l'autenticazione del certificato client. Per configurare l'autenticazione del certificato nel servizio app Azure, fare riferimento a [questo articolo][to configure certificate authentication in Azure WebSites refer to this article]. 
* È necessario accedere al certificato e alla password per la gestione in un insieme di credenziali delle chiavi di Azure o caricarli nel servizio gestione API. Il certificato deve essere in formato **PFX** . Sono consentiti i certificati autofirmati.

### <a name="prerequisites-for-key-vault-integration"></a>Prerequisiti per l'integrazione di Key Vault

1. Per la procedura di creazione di un insieme di credenziali delle chiavi, vedere [Guida introduttiva: creare un insieme di credenziali delle chiavi usando il portale di Azure](../key-vault/general/quick-create-portal.md)
1. Abilitare un' [identità gestita](api-management-howto-use-managed-service-identity.md) assegnata dal sistema o assegnata dall'utente nell'istanza di gestione API.
1. Assegnare un [criterio di accesso di Key Vault](../key-vault/general/assign-access-policy-portal.md) all'identità gestita con le autorizzazioni per ottenere ed elencare i certificati dall'insieme di credenziali. Per aggiungere i criteri:
    1. Nel portale passare all'insieme di credenziali delle chiavi.
    1. Selezionare **impostazioni > criteri di accesso > + Aggiungi criteri di accesso**.
    1. Selezionare **autorizzazioni** per i certificati, quindi selezionare **Get** ed **elenco**.
    1. In **Seleziona entità** selezionare il nome della risorsa dell'identità gestita. Se si usa un'identità assegnata dal sistema, l'entità è il nome dell'istanza di gestione API.
1. Creare o importare un certificato nell'insieme di credenziali delle chiavi. Vedere [Guida introduttiva: impostare e recuperare un certificato da Azure Key Vault usando il portale di Azure](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Aggiungere un certificato di Key Vault

Vedere [prerequisiti per l'integrazione di Key Vault](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> Quando si usa un certificato di Key Vault in gestione API, prestare attenzione a non eliminare il certificato, l'insieme di credenziali delle chiavi o l'identità gestita usata per accedere all'insieme di credenziali delle chiavi.

Per aggiungere un certificato di Key Vault a gestione API:

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. In **sicurezza** selezionare **certificati**.
1. Selezionare **certificati**  >  **+ Aggiungi**.
1. In **ID** immettere un nome a scelta.
1. In **certificato** selezionare **Key Vault**.
1. Immettere l'identificatore di un certificato di Key Vault oppure scegliere **Seleziona** per selezionare un certificato da un insieme di credenziali delle chiavi.
    > [!IMPORTANT]
    > Se si immette manualmente un identificatore del certificato di Key Vault, assicurarsi che non disponga di informazioni sulla versione. In caso contrario, il certificato non verrà ruotato automaticamente in gestione API dopo un aggiornamento nell'insieme di credenziali delle chiavi.
1. In **identità client** selezionare un'identità gestita assegnata dal sistema o esistente assegnata dall'utente. Informazioni su come [aggiungere o modificare le identità gestite nel servizio gestione API](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > L'identità necessita delle autorizzazioni per ottenere ed elencare il certificato dall'insieme di credenziali delle chiavi. Se l'accesso all'insieme di credenziali delle chiavi non è già stato configurato, gestione API richiede l'utente in modo che sia in grado di configurare automaticamente l'identità con le autorizzazioni necessarie.
1. Selezionare **Aggiungi**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Aggiungi certificato di Key Vault":::

## <a name="upload-a-certificate"></a>Caricamento di un certificato

Per caricare un certificato client in gestione API: 

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. In **sicurezza** selezionare **certificati**.
1. Selezionare **certificati**  >  **+ Aggiungi**.
1. In **ID** immettere un nome a scelta.
1. In **certificato** selezionare **personalizzato**.
1. Sfogliare per selezionare il file con estensione pfx del certificato e immettere la relativa password.
1. Selezionare **Aggiungi**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Carica certificato client":::

Una volta caricato, il certificato viene visualizzato nella finestra **certificati** . Se si dispone di molti certificati, prendere nota dell'identificazione personale del certificato desiderato per configurare un'API per l'uso di un certificato client per [l'autenticazione del gateway](#configure-an-api-to-use-client-certificate-for-gateway-authentication).

> [!NOTE]
> Per disattivare la convalida della catena di certificati quando si usa, ad esempio, un certificato autofirmato, seguire i passaggi descritti in [certificati autofirmati](#self-signed-certificates), più avanti in questo articolo.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Configurare un'API per l'uso del certificato client per l'autenticazione del gateway

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. In **API** selezionare **API**.
1. Selezionare un'API dall'elenco. 
2. Nella scheda **progettazione** selezionare l'icona dell'editor nella sezione **back-end** .
3. In **credenziali del gateway** selezionare certificato **client** e selezionare il certificato dall'elenco a discesa.
1. Selezionare **Salva**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Usa certificato client per l'autenticazione del gateway":::

> [!CAUTION]
> Questa modifica ha effetto immediato e le chiamate alle operazioni dell'API utilizzeranno il certificato per l'autenticazione nel server back-end.

> [!TIP]
> Quando un certificato viene specificato per l'autenticazione del gateway per il servizio back-end di un'API, diventa parte del criterio per tale API e può essere visualizzato nell'editor dei criteri.

## <a name="self-signed-certificates"></a>Certificati autofirmati

Se si usano certificati autofirmati, sarà necessario disabilitare la convalida della catena di certificati per gestione API per comunicare con il sistema back-end. In caso contrario, verrà restituito un codice di Errore 500. Per configurarlo, è possibile usare i [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) cmdlet di PowerShell (per il nuovo back-end) o [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (per back-end esistente) e impostare il `-SkipCertificateChainValidation` parametro su `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Eliminare un certificato client

Per eliminare un certificato, selezionarlo e quindi scegliere **Elimina** dal menu di scelta rapida (**...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Eliminare un certificato":::

> [!IMPORTANT]
> Se un criterio fa riferimento a un certificato, viene visualizzata una schermata di avviso. Per eliminare il certificato, è necessario prima rimuovere il certificato dai criteri configurati per l'utilizzo.

## <a name="next-steps"></a>Passaggi successivi

* [Come proteggere le API usando l'autenticazione con certificati client in Gestione API](api-management-howto-mutual-certificates-for-clients.md)
* Informazioni sui [criteri in gestione API](api-management-howto-policies.md)


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

