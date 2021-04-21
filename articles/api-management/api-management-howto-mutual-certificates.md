---
title: Proteggere API Management back-end usando l'autenticazione del certificato client
titleSuffix: Azure API Management
description: Informazioni su come gestire i certificati client e proteggere i servizi back-end usando l'autenticazione del certificato client in Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.openlocfilehash: d5d261368260a1c9658ae0bef8bdf63a7ca6bafe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750627"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Proteggere i servizi back-end usando l'autenticazione del certificato client in Azure API Management

API Management consente di proteggere l'accesso al servizio back-end di un'API usando i certificati client. Questa guida illustra come gestire i certificati in un'istanza del servizio azure API Management usando il portale di Azure. Viene inoltre illustrato come configurare un'API per l'uso di un certificato per accedere a un servizio back-end.

È anche possibile gestire i API Management usando [l'API REST API Management.](/rest/api/apimanagement/2020-06-01-preview/certificate)

## <a name="certificate-options"></a>Opzioni del certificato

API Management offre due opzioni per gestire i certificati usati per proteggere l'accesso ai servizi back-end:

* Fare riferimento a un certificato gestito in [Azure Key Vault](../key-vault/general/overview.md) 
* Aggiungere un file di certificato direttamente in API Management

È consigliabile usare i certificati dell'insieme di credenziali delle chiavi perché consente di migliorare API Management sicurezza:

* I certificati archiviati negli insiemi di credenziali delle chiavi possono essere riutilizzati tra i servizi
* È [possibile applicare criteri di](../key-vault/general/security-overview.md#privileged-access) accesso granulari ai certificati archiviati negli insiemi di credenziali delle chiavi
* I certificati aggiornati nell'insieme di credenziali delle chiavi vengono ruotati automaticamente in API Management. Dopo l'aggiornamento nell'insieme di credenziali delle chiavi, un certificato API Management viene aggiornato entro 4 ore. È anche possibile aggiornare manualmente il certificato usando il portale di Azure o tramite l'API REST di gestione.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Se non è ancora stata creata un'istanza API Management servizio, vedere [Creare un'istanza API Management servizio .][Create an API Management service instance]
* Il servizio back-end deve essere configurato per l'autenticazione del certificato client. Per configurare l'autenticazione del certificato Servizio app di Azure, vedere [questo articolo.][to configure certificate authentication in Azure WebSites refer to this article] 
* È necessario accedere al certificato e alla password per la gestione in un insieme di credenziali delle chiavi di Azure o caricarlo nel API Management servizio. Il certificato deve essere in **formato PFX.** Sono consentiti i certificati autofirmati.

### <a name="prerequisites-for-key-vault-integration"></a>Prerequisiti per l'integrazione dell'insieme di credenziali delle chiavi

1. Per la procedura di creazione di un insieme di credenziali delle chiavi, vedere [Avvio rapido: Creare un insieme](../key-vault/general/quick-create-portal.md)di credenziali delle chiavi usando l'portale di Azure .
1. Abilitare un'identità gestita assegnata dal sistema o [assegnata](api-management-howto-use-managed-service-identity.md) dall'utente nell'API Management predefinita.
1. Assegnare un [criterio di accesso dell'insieme di](../key-vault/general/assign-access-policy-portal.md) credenziali delle chiavi all'identità gestita con le autorizzazioni per ottenere ed elencare i certificati dall'insieme di credenziali. Per aggiungere i criteri:
    1. Nel portale passare all'insieme di credenziali delle chiavi.
    1. Selezionare **Impostazioni > criteri di accesso > + Aggiungi criteri di accesso**.
    1. Selezionare **Autorizzazioni certificato,** quindi Selezionare **Ottieni** ed **Elenca**.
    1. In **Seleziona entità** selezionare il nome della risorsa dell'identità gestita. Se si usa un'identità assegnata dal sistema, l'entità è il nome dell'API Management istanza.
1. Creare o importare un certificato nell'insieme di credenziali delle chiavi. Vedere [Avvio rapido: Impostare e recuperare un certificato da Azure Key Vault usando portale di Azure](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Aggiungere un certificato dell'insieme di credenziali delle chiavi

Vedere [Prerequisiti per l'integrazione dell'insieme di credenziali delle chiavi.](#prerequisites-for-key-vault-integration)

> [!CAUTION]
> Quando si usa un certificato dell'insieme di credenziali delle chiavi API Management, prestare attenzione a non eliminare il certificato, l'insieme di credenziali delle chiavi o l'identità gestita usata per accedere all'insieme di credenziali delle chiavi.

Per aggiungere un certificato dell'insieme di credenziali delle chiavi API Management:

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. In **Sicurezza** selezionare **Certificati**.
1. Selezionare **Certificati**  >  **+ Aggiungi**.
1. In **ID** immettere un nome a scelta.
1. In **Certificato selezionare** Insieme di credenziali delle **chiavi.**
1. Immettere l'identificatore di un certificato dell'insieme di credenziali delle chiavi oppure scegliere **Seleziona** per selezionare un certificato da un insieme di credenziali delle chiavi.
    > [!IMPORTANT]
    > Se si immette manualmente un identificatore di certificato dell'insieme di credenziali delle chiavi, assicurarsi che non abbia informazioni sulla versione. In caso contrario, il certificato non ruota automaticamente in API Management dopo un aggiornamento nell'insieme di credenziali delle chiavi.
1. In **Identità client** selezionare un'identità gestita assegnata dal sistema o un'identità gestita assegnata dall'utente esistente. Informazioni su come [aggiungere o modificare le identità gestite nel API Management servizio](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > L'identità deve avere le autorizzazioni per ottenere ed elencare il certificato dall'insieme di credenziali delle chiavi. Se non è già stato configurato l'accesso all'insieme di credenziali delle chiavi, API Management richiederà di configurare automaticamente l'identità con le autorizzazioni necessarie.
1. Selezionare **Aggiungi**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Aggiungere il certificato dell'insieme di credenziali delle chiavi":::

## <a name="upload-a-certificate"></a>Caricamento di un certificato

Per caricare un certificato client in API Management: 

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. In **Sicurezza** selezionare **Certificati.**
1. Selezionare **Certificati**  >  **+ Aggiungi.**
1. In **ID** immettere un nome a scelta.
1. In **Certificato** selezionare **Personalizzato.**
1. Sfogliare per selezionare il file con estensione pfx del certificato e immettere la relativa password.
1. Selezionare **Aggiungi**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Caricare il certificato client":::

Dopo il caricamento, il certificato viene visualizzato nella **finestra** Certificati. Se si dispone di molti certificati, prendere nota dell'identificazione personale del certificato desiderato per configurare un'API per l'uso di un certificato client per [l'autenticazione del gateway.](#configure-an-api-to-use-client-certificate-for-gateway-authentication)

> [!NOTE]
> Per disattivare la convalida della catena di certificati quando si usa, ad esempio, un certificato autofirmato, seguire la procedura descritta in [Certificati](#self-signed-certificates)autofirmati più avanti in questo articolo.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Configurare un'API per l'uso del certificato client per l'autenticazione del gateway

1. Nel [portale di Azure](https://portal.azure.com) accedere all'istanza di Gestione API.
1. In **API selezionare** **API**.
1. Selezionare un'API dall'elenco. 
2. Nella scheda **Progettazione** selezionare l'icona dell'editor nella **sezione Back-end.**
3. In **Credenziali gateway** selezionare Certificato **client** e selezionare il certificato nell'elenco a discesa.
1. Selezionare **Salva**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Usare il certificato client per l'autenticazione del gateway":::

> [!CAUTION]
> Questa modifica è effettiva immediatamente e le chiamate alle operazioni di tale API useranno il certificato per l'autenticazione nel server back-end.

> [!TIP]
> Quando viene specificato un certificato per l'autenticazione del gateway per il servizio back-end di un'API, diventa parte dei criteri per tale API e può essere visualizzato nell'editor dei criteri.

## <a name="self-signed-certificates"></a>Certificati autofirmati

Se si usano certificati autofirmati, è necessario disabilitare la convalida della catena di certificati per API Management comunicare con il sistema back-end. In caso contrario, verrà restituito un codice di Errore 500. Per configurare questa impostazione, è possibile usare i cmdlet di [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) PowerShell (per il nuovo back-end) o (per il [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) back-end esistente) e impostare il `-SkipCertificateChainValidation` parametro su `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Eliminare un certificato client

Per eliminare un certificato, selezionarlo e quindi **scegliere Elimina** dal menu di scelta rapida (**...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Eliminare un certificato":::

> [!IMPORTANT]
> Se i criteri fanno riferimento al certificato, viene visualizzata una schermata di avviso. Per eliminare il certificato, è prima necessario rimuovere il certificato da tutti i criteri configurati per usarlo.

## <a name="next-steps"></a>Passaggi successivi

* [Come proteggere le API usando l'autenticazione con certificati client in Gestione API](api-management-howto-mutual-certificates-for-clients.md)
* Informazioni sui [criteri in API Management](api-management-howto-policies.md)


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

