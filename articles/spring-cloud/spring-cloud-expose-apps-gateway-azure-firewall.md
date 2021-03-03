---
title: Esporre le applicazioni a Internet usando il gateway applicazione e il firewall di Azure
description: Come esporre le applicazioni a Internet usando il gateway applicazione e il firewall di Azure
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6c22d1bae4f1d116aa52846880498c7c2a425174
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738719"
---
# <a name="expose-applications-to-the-internet-using-application-gateway-and-azure-firewall"></a>Esporre le applicazioni a Internet usando il gateway applicazione e il firewall di Azure

Questo documento illustra come esporre le applicazioni a Internet usando il gateway applicazione e il firewall di Azure. Quando viene distribuita un'istanza del servizio cloud Spring di Azure nella rete virtuale, le applicazioni nell'istanza del servizio sono accessibili solo nella rete privata. Per rendere accessibili le applicazioni su Internet, è necessario eseguire l'integrazione con **applicazione Azure gateway** e, facoltativamente, con il **firewall di Azure**.

## <a name="prerequisites"></a>Prerequisiti

- INTERFACCIA della riga di comando di [Azure versione 2.0.4 o successiva](/cli/azure/install-azure-cli).

## <a name="define-variables"></a>Definire le variabili

Definire le variabili per il gruppo di risorse e la rete virtuale creati come indicato nella pagina relativa alla [distribuzione del cloud Spring di Azure in rete virtuale di Azure (VNet Injection)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md). Personalizzare i valori in base all'ambiente reale.

```
SUBSCRIPTION='subscription-id'
RESOURCE_GROUP='my-resource-group'
LOCATION='eastus'
SPRING_APP_PRIVATE_FQDN='my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io'
VIRTUAL_NETWORK_NAME='azure-spring-cloud-vnet'
APPLICATION_GATEWAY_SUBNET_NAME='app-gw-subnet'
APPLICATION_GATEWAY_SUBNET_CIDR='10.1.2.0/24'
```

## <a name="login-to-azure"></a>Accedere ad Azure

Accedere all'interfaccia della riga di comando di Azure e scegliere una sottoscrizione attiva.

```
az login
az account set --subscription ${SUBSCRIPTION}
```

## <a name="create-network-resources"></a>Creare risorse di rete

Il **Gateway applicazione Azure** da creare verrà aggiunto alla stessa rete virtuale o alla rete virtuale con peering per l'istanza del servizio cloud Spring di Azure. Creare prima di tutto una nuova subnet per il gateway applicazione nella rete virtuale usando `az network vnet subnet create` e creare anche un indirizzo IP pubblico come front-end del gateway applicazione usando `az network public-ip create` .

```
APPLICATION_GATEWAY_PUBLIC_IP_NAME='app-gw-public-ip'
az network vnet subnet create \
    --name ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --address-prefix ${APPLICATION_GATEWAY_SUBNET_CIDR}
az network public-ip create \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --allocation-method Static \
    --sku Standard
```

## <a name="create-application-gateway"></a>Creare il gateway applicazione

Creare un gateway applicazione usando `az network application-gateway create` e specificare il nome di dominio completo (FQDN) privato dell'applicazione come server nel pool back-end. Aggiornare quindi l'impostazione HTTP usando `az network application-gateway http-settings update` per usare il nome host dal pool back-end.

```
APPLICATION_GATEWAY_NAME='my-app-gw'
az network application-gateway create \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --location ${LOCATION} \
    --capacity 2 \
    --sku Standard_v2 \
    --http-settings-cookie-based-affinity Enabled \
    --http-settings-port 443 \
    --http-settings-protocol Https \
    --public-ip-address ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --vnet-name ${VIRTUAL_NETWORK_NAME} \
    --subnet ${APPLICATION_GATEWAY_SUBNET_NAME} \
    --servers ${SPRING_APP_PRIVATE_FQDN}
az network application-gateway http-settings update \
    --gateway-name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP} \
    --name appGatewayBackendHttpSettings \
    --host-name-from-backend-pool true
```

Il processo di creazione del gateway applicazione in Azure può richiedere fino a 30 minuti. Dopo averla creata, controllare l'integrità back-end usando `az network application-gateway show-backend-health` .  Viene esaminato se il gateway applicazione raggiunge l'applicazione tramite il nome di dominio completo privato.

```
az network application-gateway show-backend-health \
    --name ${APPLICATION_GATEWAY_NAME} \
    --resource-group ${RESOURCE_GROUP}
```

L'output indica lo stato integro del pool back-end.

```
{
  "backendAddressPools": [
    {
      "backendHttpSettingsCollection": [
        {
          "servers": [
            {
              "address": "my-azure-spring-cloud-hello-vnet.private.azuremicroservices.io",
              "health": "Healthy",
              "healthProbeLog": "Success. Received 200 status code",
              "ipConfiguration": null
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="access-your-application-using-the-frontend-public-ip-of-the-application-gateway"></a>Accedere all'applicazione usando l'IP pubblico front-end del gateway applicazione

Ottenere l'indirizzo IP pubblico del gateway applicazione usando `az network public-ip show` .

```
az network public-ip show \
    --resource-group ${RESOURCE_GROUP} \
    --name ${APPLICATION_GATEWAY_PUBLIC_IP_NAME} \
    --query [ipAddress] \
    --output tsv
```

Copiare e incollare l'indirizzo IP pubblico nella barra degli indirizzi del browser.

  ![App in IP pubblico](media/spring-cloud-expose-apps-gateway-az-firewall/app-gateway-public-ip.png)

## <a name="see-also"></a>Vedere anche

- [Risoluzione dei problemi di Azure Spring Cloud nella rete virtuale](spring-cloud-troubleshooting-vnet.md)
- [Responsabilità del cliente per l'esecuzione di Azure Spring Cloud nella rete virtuale](spring-cloud-vnet-customer-responsibilities.md)