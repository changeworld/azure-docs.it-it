---
title: Creare ambienti di Integration Services (ISEs) con l'API REST di app per la logica
description: Creare un ambiente del servizio di integrazione con l'API REST di app per la logica in modo che sia possibile accedere alle reti virtuali di Azure (reti virtuali) da app per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 12/30/2020
ms.openlocfilehash: ee6c116d02a7be1682d9e8379037ef1b8c92bce8
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967039"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Creare un ambiente del servizio di integrazione (ISE) usando l'API REST App per la logica

Per gli scenari in cui le app per la logica e gli account di integrazione devono accedere a una [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md), è possibile creare un [ *ambiente del servizio di integrazione* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) usando l'API REST di app per la logica. Per altre informazioni sugli ISE, vedere [Accedere alle risorse della rete virtuale di Azure da app per la logica di Azure](connect-virtual-network-vnet-isolated-environment-overview.md).

Questo articolo illustra come creare un ISE usando l'API REST di app per la logica in generale. Facoltativamente, è anche possibile abilitare un' [identità gestita assegnata dal sistema o assegnata dall'utente](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) in ISE, ma in questo momento solo usando l'API REST di app per la logica. Questa identità consente all'ISE di autenticare l'accesso alle risorse protette, ad esempio le macchine virtuali e altri sistemi o servizi, che si trovano in o si connettono a una rete virtuale di Azure. In questo modo, non è necessario accedere con le proprie credenziali.

Per altre informazioni su altri modi per creare un ISE, vedere questi articoli:

* [Creare un ISE usando il portale di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* [Creare un ISE usando il modello di avvio rapido di esempio Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment)
* [Creare un ISE che supporti l'uso di chiavi gestite dal cliente per la crittografia dei dati inattivi](customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Prerequisiti

* Gli stessi [prerequisiti](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) e requisiti di [accesso](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) di quando si crea un ISE nel portale di Azure

* Eventuali risorse aggiuntive che si desidera utilizzare con ISE per poter includere le informazioni nella definizione ISE, ad esempio: 

  * Per abilitare il supporto dei certificati autofirmati, è necessario includere le informazioni su tale certificato nella definizione di ISE.

  * Per abilitare l'identità gestita assegnata dall'utente, è necessario creare in anticipo tale identità e includere le `objectId` `principalId` proprietà, e `clientId` e i relativi valori nella definizione di ISE. Per ulteriori informazioni, vedere [creare un'identità gestita assegnata dall'utente nel portale di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

* Uno strumento che è possibile usare per creare ISE chiamando l'API REST di app per la logica con una richiesta PUT HTTPS. Ad esempio, è possibile usare il [post](https://www.getpostman.com/downloads/), oppure è possibile compilare un'app per la logica che esegue questa attività.

## <a name="create-the-ise"></a>Creare ISE

Per creare ISE chiamando l'API REST di app per la logica, effettuare questa richiesta HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Per la versione dell'API REST di app per la logica 2019-05-01 è necessario effettuare una richiesta HTTP PUT per i connettori ISE.

Il completamento della distribuzione richiede in genere entro due ore. In alcuni casi, la distribuzione potrebbe richiedere fino a quattro ore. Per controllare lo stato della distribuzione, nella [portale di Azure](https://portal.azure.com)della barra degli strumenti di Azure selezionare l'icona notifiche, che consente di aprire il riquadro notifiche.

> [!NOTE]
> Se si verifica un errore di distribuzione o si elimina l'ISE, Azure potrebbe richiedere fino a un'ora prima di rilasciare le subnet. Questo ritardo indica che potrebbe essere necessario attendere prima di riusare tali subnet in un altro ISE.
>
> Se si elimina la rete virtuale, Azure impiega in genere fino a due ore per rilasciare le subnet, ma questa operazione potrebbe richiedere più tempo. 
> Quando si eliminano le reti virtuali, assicurarsi che non ci siano risorse ancora connesse. 
> Vedere [Eliminare la rete virtuale](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Intestazione della richiesta

Nell'intestazione della richiesta includere le proprietà seguenti:

* `Content-type`: Impostare il valore della proprietà su `application/json` .

* `Authorization`: Impostare questo valore della proprietà sul bearer token per il cliente che ha accesso alla sottoscrizione o al gruppo di risorse di Azure che si vuole usare.

<a name="request-body"></a>

## <a name="request-body"></a>Testo della richiesta

Nel corpo della richiesta specificare la definizione di risorsa da usare per la creazione di ISE, incluse le informazioni per le funzionalità aggiuntive che si vuole abilitare in ISE, ad esempio:

* Per creare un ISE che consenta l'uso di un certificato autofirmato e di un certificato emesso da un'autorità di certificazione dell'organizzazione che viene installata nel `TrustedRoot` percorso, includere l' `certificates` oggetto nella sezione della definizione ISE `properties` , come descritto in seguito in questo articolo.

* Per creare un ISE che usa un'identità gestita assegnata dal sistema o assegnata dall'utente, includere l' `identity` oggetto con il tipo di identità gestito e altre informazioni necessarie nella definizione ISE, come descritto in questo articolo in un secondo momento.

* Per creare un ISE che usa chiavi gestite dal cliente e Azure Key Vault per crittografare i dati inattivi, includere le [informazioni che consentono il supporto della chiave gestita dal cliente](customer-managed-keys-integration-service-environment.md). È possibile configurare chiavi gestite *dal cliente solo in fase di creazione* e non in seguito.

### <a name="request-body-syntax"></a>Sintassi del corpo della richiesta

Ecco la sintassi del corpo della richiesta, che descrive le proprietà da usare quando si crea ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   // Include the `identity` object to enable the system-assigned identity or user-assigned identity
   "identity": {
      "type": <"SystemAssigned" | "UserAssigned">,
      // When type is "UserAssigned", include the following "userAssignedIdentities" object:
      "userAssignedIdentities": {
         "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{user-assigned-managed-identity-object-ID}": {
            "principalId": "{principal-ID}",
            "clientId": "{client-ID}"
         }
      }
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      // Include `certificates` object to enable self-signed certificate and the certificate issued by Enterprise Certificate Authority
      "certificates": {
         "testCertificate": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

### <a name="request-body-example"></a>Esempio di corpo della richiesta

Questo corpo della richiesta di esempio mostra i valori di esempio:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
         "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/*********************************": {
            "principalId": "*********************************",
            "clientId": "*********************************"
         }
      }
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "certificates": {
         "testCertificate": {
            "publicCertificate": "LS0tLS1CRUdJTiBDRV...",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="add-custom-root-certificates"></a>Aggiungere certificati radice personalizzati

Spesso si usa ISE per connettersi ai servizi personalizzati nella rete virtuale o in locale. Questi servizi personalizzati sono spesso protetti da un certificato emesso da un'autorità di certificazione radice personalizzata, ad esempio un'autorità di certificazione aziendale o un certificato autofirmato. Per ulteriori informazioni sull'utilizzo di certificati autofirmati, vedere [accesso protetto e accesso ai dati per le chiamate in uscita ad altri servizi e sistemi](../logic-apps/logic-apps-securing-a-logic-app.md#secure-outbound-requests). Per la corretta connessione di ISE a questi servizi tramite Transport Layer Security (TLS), ISE deve accedere a questi certificati radice. Per aggiornare ISE con un certificato radice trusted personalizzato, effettuare questa richiesta HTTPS `PATCH` :

`PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

Prima di eseguire questa operazione, esaminare le considerazioni seguenti:

* Assicurarsi di caricare il certificato radice *e* tutti i certificati intermedi. Il numero massimo di certificati è 20.

* Il caricamento dei certificati radice è un'operazione di sostituzione in cui il caricamento più recente sovrascrive i caricamenti precedenti. Se ad esempio si invia una richiesta di caricamento di un certificato e quindi si invia un'altra richiesta di caricamento di un altro certificato, ISE utilizzerà solo il secondo certificato. Se è necessario usare entrambi i certificati, aggiungerli insieme nella stessa richiesta.  

* Il caricamento dei certificati radice è un'operazione asincrona che potrebbe richiedere del tempo. Per controllare lo stato o il risultato, è possibile inviare una `GET` richiesta utilizzando lo stesso URI. Il messaggio di risposta ha un `provisioningState` campo che restituisce il `InProgress` valore quando l'operazione di caricamento è ancora in funzione. Quando `provisioningState` value è `Succeeded` , l'operazione di caricamento è stata completata.

#### <a name="request-body-syntax-for-adding-custom-root-certificates"></a>Sintassi del corpo della richiesta per l'aggiunta di certificati radice personalizzati

Ecco la sintassi del corpo della richiesta, che descrive le proprietà da usare quando si aggiungono i certificati radice:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "properties": {
      "certificates": {
         "testCertificate1": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         },
         "testCertificate2": {
            "publicCertificate": "{base64-encoded-certificate}",
            "kind": "TrustedRoot"
         }
      }
   }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Aggiungere risorse agli ambienti del servizio di integrazione](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gestire gli ambienti del servizio di integrazione](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
