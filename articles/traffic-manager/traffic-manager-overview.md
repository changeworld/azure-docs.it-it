---
title: Gestione traffico di Azure | Microsoft Docs
description: Questo articolo offre una panoramica di Gestione traffico di Azure. Scoprire se è la scelta migliore per il bilanciamento del carico del traffico utente per l'applicazione.
services: traffic-manager
author: duongau
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2021
ms.author: duau
ms.openlocfilehash: 09b82eed5ad6a9ad121ca56d197eb9c003d027f5
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624910"
---
# <a name="what-is-traffic-manager"></a>Gestione traffico di Azure
Gestione traffico di Azure è un servizio di bilanciamento del carico del traffico basato su DNS. Questo servizio ti permette di distribuire il traffico alle tue applicazioni pubbliche nelle aree di Azure globali. Gestione traffico fornisce anche gli endpoint pubblici con disponibilità elevata e velocità di risposta rapida.

Gestione traffico USA DNS per indirizzare le richieste client all'endpoint di servizio appropriato in base a un metodo di routing del traffico. Gestione traffico fornisce anche il monitoraggio dello stato per ogni endpoint. L'endpoint può essere qualsiasi servizio con connessione Internet ospitato all'interno o all'esterno di Azure. Gestione traffico offre diversi [metodi di routing del traffico](traffic-manager-routing-methods.md) e [opzioni di monitoraggio degli endpoint](traffic-manager-monitoring.md) per soddisfare le diverse esigenze delle applicazioni e i modelli di failover automatico. Gestione traffico è resiliente agli errori, incluso l'errore di un'intera area di Azure.

>[!NOTE]
> Azure offre una suite di soluzioni di bilanciamento del carico completamente gestite per tutti gli scenari. Per la terminazione del protocollo Transport Layer Security (TLS) ("offload SSL"), la richiesta per HTTP/HTTPS o l'elaborazione a livello di applicazione, vedere [Gateway di applicazione](../application-gateway/overview.md). Se si vuole ottenere il bilanciamento a livello di area, vedere [Load Balancer](../load-balancer/load-balancer-overview.md). Gli scenari end-to-end potrebbero trarre vantaggio dalla combinazione di queste soluzioni in base alle esigenze.
>
> Per un confronto tra le opzioni di bilanciamento del carico di Azure, vedere [Panoramica delle opzioni di bilanciamento del carico in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

Gestione traffico offre le funzionalità seguenti:

## <a name="increase-application-availability"></a>Migliorare la disponibilità dell'applicazione

Gestione traffico assicura la disponibilità elevata delle applicazioni essenziali attraverso il monitoraggio degli endpoint e il failover automatico in caso di inattività di un endpoint.
    
## <a name="improve-application-performance"></a>Migliorare le prestazioni dell'applicazione

Azure consente di eseguire servizi cloud e siti Web in data center dislocati in tutto il mondo. Gestione traffico può migliorare la velocità di risposta del sito Web indirizzando il traffico all'endpoint con la latenza più bassa.

## <a name="service-maintenance-without-downtime"></a>Manutenzione del servizio senza tempi di inattività

È possibile eseguire una manutenzione pianificata sulle applicazioni senza tempi di inattività. Durante l'esecuzione delle operazioni di manutenzione, Gestione traffico può indirizzare il traffico verso endpoint alternativi.

## <a name="combine-hybrid-applications"></a>Combinare applicazioni ibride

Gestione traffico supporta endpoint esterni, non di Azure, che possono essere usati con distribuzioni cloud ibride e locali, inclusi gli scenari di "[espansione nel cloud", "](https://azure.microsoft.com/overview/what-is-cloud-bursting/)migrazione al cloud" e "failover nel cloud".

## <a name="distribute-traffic-for-complex-deployments"></a>Distribuire il traffico per distribuzioni complesse

Usando i [profili di Gestione traffico annidati](traffic-manager-nested-profiles.md) è possibile combinare più metodi di routing del traffico per creare regole sofisticare e flessibili in grado di adattarsi alle esigenze di distribuzioni più complesse e di maggiori dimensioni.

## <a name="pricing"></a>Prezzi

Per informazioni sui prezzi, vedere [Prezzi per Gestione traffico](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare un profilo di gestione traffico](./quickstart-create-traffic-manager-profile.md).
- Informazioni [sul funzionamento di gestione traffico](traffic-manager-how-it-works.md).
- Visualizzare le [domande frequenti](traffic-manager-FAQs.md) su Gestione traffico.