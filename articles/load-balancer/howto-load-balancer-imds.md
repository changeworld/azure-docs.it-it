---
title: Recuperare i metadati del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure (IMDS)
titleSuffix: Azure Load Balancer
description: Introduzione all'apprendimento come recuperare i metadati del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: 35e32627eaf26eb6f73ca382f119eab61ebd8711
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417830"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Recuperare i metadati del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure (IMDS)

## <a name="prerequisites"></a>Prerequisiti

* Usare la [versione più recente dell'API](/virtual-machines/windows/instance-metadata-service?tabs=windows#supported-api-versions) per la richiesta.

## <a name="sample-request-and-response"></a>Richiesta e risposta di esempio
> [!IMPORTANT]
> Questo esempio Ignora i proxy. È **necessario** ignorare i proxy durante l'esecuzione di query su IMDS. Per altre informazioni, vedere [proxy](/virtual-machines/windows/instance-metadata-service?tabs=windows#proxies).
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```

### <a name="linux"></a>[Linux](#tab/linux/)

```bash
curl -H "Metadata:true" --noproxy "*" "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01"
```

---
### <a name="sample-response"></a>Risposta di esempio

```json
{
   "loadbalancer": {
    "publicIpAddresses":[
      {
         "frontendIpAddress":"51.0.0.1",
         "privateIpAddress":"10.1.0.4"
      }
   ],
   "inboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frontendIpAddress":"2603:10e1:100:2::1:1",
         "protocol":"tcp",
         "frontendPort":80,
         "backendPort":443,
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
   ],
   "outboundRules":[
      {
         "frontendIpAddress":"50.0.0.1",
         "privateIpAddress":"10.1.0.4"
      },
      {
         "frotendIpAddress":"2603:10e1:100:2::1:1",
         "privateIpAddress":"ace:cab:deca:deed::1"
      }
    ]
   }
}

```

## <a name="next-steps"></a>Passaggi successivi
[Codici di errore comuni e passaggi per la risoluzione dei problemi](troubleshoot-load-balancer-imds.md)

Altre informazioni sul [servizio metadati dell'istanza di Azure](/virtual-machines/windows/instance-metadata-service)

[Recuperare tutti i metadati per un'istanza](/virtual-machines/windows/instance-metadata-service?tabs=windows#access-azure-instance-metadata-service)

[Distribuire un servizio di bilanciamento del carico standard](quickstart-load-balancer-standard-public-portal.md)

