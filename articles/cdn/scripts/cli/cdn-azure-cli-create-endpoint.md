---
title: Creare un profilo e un endpoint della rete per la distribuzione di contenuti di Azure tramite l'interfaccia della riga di comando di Azure
description: Script dell'interfaccia della riga di comando di Azure per creare un profilo, un endpoint, un gruppo di origine, un'origine e un dominio personalizzato della rete CDN
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce01c1f851a5dbaedc85d848b12bf0b0831a16ef
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726870"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Creare un profilo e un endpoint della rete CDN di Azure usando l'interfaccia della riga di comando

In alternativa alla portale di Azure, è possibile usare questi script dell'interfaccia della riga di comando di Azure di esempio per gestire le operazioni della rete CDN seguenti:

- Creare un profilo di rete CDN.
- Creare un endpoint della rete CDN.
- Creare un gruppo di origine della rete CDN e impostarlo come gruppo predefinito.
- Creare un'origine della rete CDN.
- Creare un dominio personalizzato e abilitare HTTPS.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>Script di esempio

Se non si ha già un gruppo di risorse per il profilo della rete CDN, crearlo con il comando `az group create` :

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

Il seguente script dell'interfaccia della riga di comando di Azure crea un profilo e un endpoint della rete CDN:

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

Il seguente script dell'interfaccia della riga di comando di Azure crea un gruppo di origine della rete CDN, imposta il gruppo di origine predefinito per un endpoint e crea una nuova origine:

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

Il seguente script dell'interfaccia della riga di comando di Azure crea un dominio personalizzato della rete CDN e Abilita HTTPS. Prima di poter associare un dominio personalizzato a un endpoint della rete CDN di Azure, è necessario creare prima un record di nome canonico (CNAME) con il servizio DNS di Azure o il provider DNS per puntare all'endpoint della rete CDN. Per altre informazioni, vedere [Creare un record DNS CNAME](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record).

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esecuzione degli script di esempio, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>Comandi dell'interfaccia della riga di comando Azure usati in questo articolo

- [AZ CDN endpoint create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [AZ CDN endpoint Update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [AZ CDN Origin create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [AZ CDN Origin-Group create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [AZ CDN profile create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [AZ CDN Custom-Domain create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [AZ CDN Custom-Domain Enable-HTTPS](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
