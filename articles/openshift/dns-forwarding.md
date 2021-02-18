---
title: Configurare l'invio DNS per Azure Red Hat OpenShift 4
description: Configurare l'invio DNS per Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 68cc5dbee5b96472d11bdfb3a8bf35d118638dcd
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633870"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Configurare l'invio DNS in un cluster Azure Red Hat OpenShift 4

Per configurare l'invio DNS in un cluster Azure Red Hat OpenShift, è necessario modificare l'operatore DNS. Questa modifica consentirà ai pod dell'applicazione in esecuzione all'interno del cluster di risolvere i nomi ospitati in un server DNS privato all'esterno del cluster. Questi passaggi sono documentati per OpenShift 4,6 [qui](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).

Se, ad esempio, si desidera che tutte le richieste DNS per *. example.com vengano risolte da un server DNS 192.168.100.10, è possibile modificare la configurazione dell'operatore eseguendo:
 
```bash
oc edit dns.operator/default
```
 
Verrà avviato un editor ed è possibile sostituire `spec: {}` con:
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Salvare il file e chiudere l'editor.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'invio DNS per OpenShift 4,6, vedere [qui](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html).