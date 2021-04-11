---
title: Abilita Transport Layer Security end-to-end
titleSuffix: Azure Spring Cloud
description: Come abilitare Transport Layer Security end-to-end per un'applicazione.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/24/2021
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 647cf6f0b1af6a5858bbf1147cc03ecc4637ed25
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227807"
---
# <a name="enable-end-to-end-tls-for-an-application"></a>Abilitare TLS end-to-end per un'applicazione

Questo argomento illustra come abilitare SSL/TLS end-to-end per proteggere il traffico da un controller di ingresso alle applicazioni che supportano HTTPS. Dopo l'abilitazione di TLS end-to-end e il caricamento di un certificato da un insieme di credenziali delle credenziali, tutte le comunicazioni in Azure Spring cloud sono protette da TLS.

   ![Grafico delle comunicazioni protette da TLS.](media/enable-end-to-end-tls/secured-tls.png)

## <a name="prerequisites"></a>Prerequisiti 

- Un'istanza di Azure Spring Cloud distribuita. Per iniziare, seguire la guida di [avvio rapido sulla distribuzione tramite l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-cli).
- Se non si ha familiarità con il protocollo TLS end-to-end, vedere l' [esempio end-to-end di TLS](https://github.com/Azure-Samples/spring-boot-secure-communications-using-end-to-end-tls-ssl).
- Per caricare in modo sicuro i certificati richiesti nelle app Spring boot, è possibile usare l'utilità di [avvio Spring boot Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-keyvault-certificates).


## <a name="enable-end-to-end-tls-on-an-existing-app"></a>Abilitare TLS end-to-end in un'app esistente

Usare il comando `az spring-cloud app update --enable-end-to-end-tls` per abilitare o disabilitare TLS end-to-end per un'app.

```azurecli
az spring-cloud app update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app update --enable-end-to-end-tls false -n app_name -s service_name -g resource_group_name
```

## <a name="enable-end-to-end-tls-when-you-bind-custom-domain"></a>Abilitare TLS end-to-end quando si associa un dominio personalizzato

Usare il comando `az spring-cloud app custom-domain update --enable-end-to-end-tls` o `az spring-cloud app custom-domain bind --enable-end-to-end-tls` per abilitare o disabilitare TLS end-to-end per un'app.

```azurecli
az spring-cloud app custom-domain update --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
az spring-cloud app custom-domain bind --enable-end-to-end-tls -n app_name -s service_name -g resource_group_name
```

## <a name="verify-end-to-end-tls-status"></a>Verificare lo stato di TLS end-to-end

Usare il comando `az spring-cloud app show` per controllare il valore di `enableEndToEndTls` .
```
az spring-cloud app show -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Passaggi successivi
* [Accesso al server di configurazione e al registro del servizio](how-to-access-data-plane-azure-ad-rbac.md)
