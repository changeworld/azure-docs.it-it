---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: ed53cb60f6fb3bca93900941a8a6a53bed99d779
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073438"
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
* Mapping dei percorsi
* Impostazioni che terminano con il suffisso _EXTENSION_VERSION

> [!NOTE]
> Per rendere queste impostazioni scambiabili, aggiungere l'impostazione dell'app `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` in ogni slot dell'app e impostarne il valore su `0` o `false` . Queste impostazioni sono tutte scambiabili o non sono affatto disponibili. Non è possibile rendere solo alcune impostazioni scambiabili e non le altre.

> Anche alcune impostazioni di app che si applicano alle impostazioni non scambiate non vengono scambiate. Ad esempio, poiché le impostazioni di diagnostica non vengono scambiate, le impostazioni dell'app correlate come `WEBSITE_HTTPLOGGING_RETENTION_DAYS` e `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` non vengono scambiate anche se non vengono visualizzate come impostazioni degli slot.
>
