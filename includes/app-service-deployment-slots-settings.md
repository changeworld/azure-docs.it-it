---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: 70ab0b5c70e94c4784a7ab260b3304107bcb1175
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096344"
---
Quando si clona la configurazione da un altro slot di distribuzione, la configurazione clonata è modificabile. Alcuni elementi di configurazione seguono il contenuto in uno scambio (non specifico dello slot), mentre altri elementi di configurazione si mantengono nello stesso slot dopo uno scambio (specifico dello slot). Negli elenchi seguenti sono riportate le impostazioni che vengono modificate durante lo scambio degli slot.

**Impostazioni che vengono scambiate**:

* Impostazioni generali, ad esempio versione del Framework, 32/64 bit, Web socket
* Impostazioni app (possono essere configurate per adattarsi a uno slot)
* Stringhe di connessione (possono essere configurate per adattarsi a uno slot)
* Mapping dei gestori
* Certificati pubblici
* Contenuto WebJobs
* Connessioni ibride *
* Endpoint di servizio *
* Rete per la distribuzione di contenuti di Azure *

Le funzionalità contrassegnate con un asterisco (*) sono pianificate per lo scambio. 

**Impostazioni che non vengono scambiate**:

* Endpoint di pubblicazione
* Nomi di dominio personalizzati
* Certificati non pubblici e impostazioni TLS/SSL
* Impostazioni di scalabilità
* Utilità di pianificazione WebJobs
* Restrizioni IP
* Always On
* Impostazioni di diagnostica
* Condivisione di risorse tra le origini (CORS)
* Integrazione della rete virtuale

> [!NOTE]
> Anche alcune impostazioni di app che si applicano alle impostazioni non scambiate non vengono scambiate. Ad esempio, poiché le impostazioni di diagnostica non vengono scambiate, le impostazioni dell'app correlate come `WEBSITE_HTTPLOGGING_RETENTION_DAYS` e `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` non vengono scambiate anche se non vengono visualizzate come impostazioni degli slot.
>
