---
title: Preferenza di routing non a consumo in Azure
description: Informazioni su come configurare la preferenza di routing per le risorse in uscita da i dati nel provider della rete CDN.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about enabling routing preference for my CDN origin resources.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 1fcc918e5b4b54a415fed0f38d210aeeaa62c008
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679791"
---
# <a name="what-is-routing-preference-unmetered"></a>Informazioni sulle preferenze di routing non a consumo

Per i provider della rete per la distribuzione di contenuti (CDN) con clienti che ospitano il contenuto di origine in Azure sono disponibili le preferenze di routing non a consumo. Il servizio consente ai provider della rete CDN di stabilire una connessione diretta al peering con i router perimetrali della rete globale Microsoft in varie posizioni.

![Preferenza di routing non a consumo](media/routing-preference-unmetered/unmetered.png)

Il traffico di rete in uscita da dall'origine in Azure destinato al provider della rete CDN trae vantaggio dalla connettività diretta.
* La fattura per il trasferimento dei dati per il traffico in uscita da dalle risorse di Azure instradate attraverso questi collegamenti diretti è gratuita.
* La connessione diretta tra il provider e l'origine della rete CDN in Azure offre prestazioni ottimali in quanto non sono presenti hop tra. Questo vantaggio è il carico di lavoro della rete CDN che recupera spesso i dati dall'origine.

## <a name="configuring-routing-preference-unmetered"></a>Configurazione delle preferenze di routing non a consumo

Per sfruttare i vantaggi della preferenza di routing, è necessario che i provider della rete CDN facciano parte di questo programma. Se il provider della rete CDN non fa parte del programma, contattare il provider della rete CDN.

Configurare quindi la preferenza di routing per le risorse e impostare il tipo di preferenza di routing su **Internet**. È possibile configurare la preferenza di routing durante la creazione di un indirizzo IP pubblico e quindi associare l'indirizzo IP pubblico a risorse quali macchine virtuali, bilanciamenti del carico con connessione Internet e altro ancora. [Informazioni su come configurare le preferenze di routing per un indirizzo IP pubblico usando il portale di Azure](routing-preference-portal.md)

È anche possibile abilitare la preferenza di routing per l'account di archiviazione e pubblicare un secondo endpoint, che deve essere usato dal provider della rete CDN per recuperare i dati dall'origine di archiviazione. Ad esempio, la pubblicazione di un endpoint specifico della Route Internet per l'account di archiviazione *StorageAccountA* pubblicherà il secondo endpoint per i servizi di archiviazione, come mostrato di seguito:

![Preferenza di routing per gli account di archiviazione](media/routing-preference-unmetered/storage-endpoints.png)


## <a name="next-steps"></a>Passaggi successivi

* [Configurare la preferenza di routing per una macchina virtuale con Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Configurare la preferenza di routing per una macchina virtuale con l'interfaccia della riga di comando di Azure](configure-routing-preference-virtual-machine-cli.md)
* [Configurare la preferenza di routing per l'account di archiviazione](/azure/storage/common/network-routing-preference)