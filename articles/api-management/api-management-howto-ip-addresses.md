---
title: Indirizzi IP del servizio API Management Azure | Microsoft Docs
description: Informazioni su come recuperare gli indirizzi IP di un servizio azure API Management e quando cambiano.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 04/13/2021
ms.author: apimpm
ms.openlocfilehash: 5939292b6e810634723fada17521bb227764b989
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534039"
---
# <a name="ip-addresses-of-azure-api-management"></a>Indirizzi IP di Azure API Management

Questo articolo descrive come recuperare gli indirizzi IP del servizio API Management Azure. Gli indirizzi IP possono essere pubblici o privati se il servizio si trova in una rete virtuale.

È possibile usare gli indirizzi IP per creare regole del firewall, filtrare il traffico in ingresso verso i servizi back-end o limitare il traffico in uscita.

## <a name="ip-addresses-of-api-management-service"></a>Indirizzi IP del API Management servizio

Ogni API Management di servizio nel livello Developer, Basic, Standard o Premium ha indirizzi IP pubblici, che sono esclusivi solo per tale istanza del servizio (non sono condivisi con altre risorse). 

È possibile recuperare gli indirizzi IP dal dashboard panoramica della risorsa nel portale di Azure.

![API Management'indirizzo IP](media/api-management-howto-ip-addresses/public-ip.png)

È anche possibile recuperarli a livello di codice con la chiamata API seguente:

```
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>
```

Gli indirizzi IP pubblici fanno parte della risposta:

```json
{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.77.143.53"
    ],
    ...
  }
  ...
}
```

Nelle [distribuzioni multi-regione](api-management-howto-deploy-multi-region.md)ogni distribuzione a livello di regione ha un indirizzo IP pubblico.

## <a name="ip-addresses-of-api-management-service-in-vnet"></a>Indirizzi IP del API Management nella rete virtuale

Se il API Management si trova all'interno di una rete virtuale, avrà due tipi di indirizzi IP: pubblico e privato.

Gli indirizzi IP pubblici vengono usati per la comunicazione interna sulla porta, per `3443` la gestione della configurazione, ad esempio tramite Azure Resource Manager. Nella configurazione della rete virtuale esterna vengono usati anche per il traffico api di runtime. Quando una richiesta viene inviata da API Management a un back-end pubblico (con connessione Internet), un indirizzo IP pubblico sarà visibile come origine della richiesta.

Gli indirizzi IP virtuali privati (VIP), disponibili solo **nella** modalità rete virtuale [interna,](api-management-using-with-internal-vnet.md)vengono usati per connettersi dall'interno della rete agli endpoint API Management: gateway, portale per sviluppatori e piano di gestione per l'accesso diretto alle API. È possibile usarli per configurare i record DNS all'interno della rete.

Gli indirizzi di entrambi i tipi verranno visualizzati nella portale di Azure e nella risposta della chiamata API:

![API Management nell'indirizzo IP della rete virtuale](media/api-management-howto-ip-addresses/vnet-ip.png)


```json
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<service-name>?api-version=<api-version>

{
  ...
  "properties": {
    ...
    "publicIPAddresses": [
      "13.85.20.170"
    ],
    "privateIPAddresses": [
      "192.168.1.5"
    ],
    ...
  },
  ...
}
```

API Management usa un indirizzo IP pubblico per le connessioni esterne alla rete virtuale e un indirizzo IP privato per le connessioni all'interno della rete virtuale.

## <a name="ip-addresses-of-consumption-tier-api-management-service"></a>Indirizzi IP del servizio di API Management consumo

Se il API Management è un servizio a consumo, non ha un indirizzo IP dedicato. Il servizio del livello a consumo viene eseguito in un'infrastruttura condivisa e senza un indirizzo IP deterministico. 

Ai fini della restrizione del traffico, è possibile usare l'intervallo di indirizzi IP dei data center di Azure. Fare riferimento [all'articolo Funzioni di Azure documentazione per](../azure-functions/ip-addresses.md#data-center-outbound-ip-addresses) i passaggi precisi.

## <a name="changes-to-the-ip-addresses"></a>Modifiche agli indirizzi IP

Nei livelli Developer, Basic, Standard e Premium di API Management gli indirizzi IP pubblici (VIP) sono statici per la durata di un servizio, con le eccezioni seguenti:

* Il servizio viene eliminato e quindi ricreato.
* La sottoscrizione al servizio viene [sospesa](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) o [avvisata](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states), ad esempio per mancato pagamento, e quindi ripristinata.
* La rete virtuale di Azure viene aggiunta o rimossa dal servizio.
* API Management servizio viene alternato tra la modalità di distribuzione della rete virtuale esterna e la modalità di distribuzione della rete virtuale interna.

Nelle [distribuzioni multiarea,](api-management-howto-deploy-multi-region.md)l'indirizzo IP dell'area cambia se un'area viene liberata e quindi ripristinata. L'indirizzo IP a livello di regione cambia anche quando si abilita, aggiunge o rimuove zone [di disponibilità.](zone-redundancy.md)
