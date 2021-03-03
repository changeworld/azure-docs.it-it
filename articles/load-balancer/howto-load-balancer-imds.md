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
ms.openlocfilehash: 9ec217cefb05929ed6f5c7395df5e68891e823ac
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739781"
---
# <a name="retrieve-load-balancer-metadata-using-the-azure-instance-metadata-service-imds"></a>Recuperare i metadati del servizio di bilanciamento del carico usando il servizio metadati dell'istanza di Azure (IMDS)

## <a name="prerequisites"></a>Prerequisiti

* Usare la [versione più recente dell'API](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#supported-api-versions) per la richiesta.

## <a name="sample-request-and-response"></a>Richiesta e risposta di esempio
> [!IMPORTANT]
> Questo esempio Ignora i proxy. È **necessario** ignorare i proxy durante l'esecuzione di query su IMDS. Per altre informazioni, vedere [proxy](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#proxies).
### <a name="windows"></a>[Windows](#tab/windows/)

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254:80/metadata/loadbalancer?api-version=2020-10-01" | ConvertTo-Json
```
> [!NOTE]
> Il parametro-NoProxy è stato introdotto in PowerShell 6,0. Se si usa una versione precedente di PowerShell, rimuovere-NoProxy nel corpo della richiesta e assicurarsi che non si stia usando un proxy durante il recupero delle informazioni IMDS. Fare clic [qui](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service?tabs=windows#proxies) per altre informazioni.
> 
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

Altre informazioni sul [servizio metadati dell'istanza di Azure](../virtual-machines/windows/instance-metadata-service.md)

[Recuperare tutti i metadati per un'istanza](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#access-azure-instance-metadata-service)

[Distribuire un servizio di bilanciamento del carico standard](quickstart-load-balancer-standard-public-portal.md)

