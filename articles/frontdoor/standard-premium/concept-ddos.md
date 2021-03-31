---
title: 'Sportello anteriore di Azure: protezione DDoS'
description: Questa pagina fornisce informazioni sul modo in cui Azure front door standard/Premium contribuisce alla protezione da attacchi DDoS
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100150"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>Protezione DDoS in Azure front door standard/Premium (anteprima)

> [!Note]
> Questa documentazione è destinata a Azure front door standard/Premium (Preview). Stai cercando informazioni su Azure front door? Visualizza [qui](../front-door-overview.md).

Il front-end di Azure presenta diverse caratteristiche e caratteristiche che consentono di evitare attacchi di tipo Denial of Service (DDoS) distribuiti. Queste funzionalità possono impedire agli utenti malintenzionati di raggiungere l'applicazione e influire sulla disponibilità e sulle prestazioni dell'applicazione.

> [!IMPORTANT]
> Azure front door standard/Premium (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="integration-with-azure-ddos-protection-basic"></a>Integrazione con protezione DDoS di Azure Basic

La porta anteriore è protetta da protezione DDoS di Azure Basic. Per impostazione predefinita, la funzionalità è integrata nella piattaforma front door e senza costi aggiuntivi. La scalabilità e la capacità complete della rete distribuita a livello globale di front-end offrono una difesa contro gli attacchi comuni a livello di rete tramite il monitoraggio del traffico always on e la mitigazione in tempo reale. La protezione DDoS di base difende inoltre i flussi di query DNS di livello 7 più comuni e di livello 3 e 4 che si rivolgono agli endpoint pubblici. Questo servizio ha anche un tracking collaudato nella protezione dei servizi aziendali e consumer di Microsoft da attacchi su larga scala. Per altre informazioni, vedere [protezione DDoS di Azure](../../security/fundamentals/ddos-best-practices.md).

## <a name="protocol-blocking"></a>Blocco del protocollo

Lo sportello anteriore accetta solo il traffico sui protocolli HTTP e HTTPS ed elabora le richieste valide solo con un' `Host` intestazione nota. Questo comportamento contribuisce a mitigare alcuni tipi di attacco DDoS comuni, inclusi gli attacchi volumetrici che vengono distribuiti in una gamma di protocolli e porte, attacchi di amplificazione DNS e attacchi di avvelenamento TCP.

## <a name="capacity-absorption"></a>Assorbimento della capacità

La porta anteriore è un servizio ampiamente distribuito a livello globale. Ci sono molti clienti, inclusi i prodotti cloud su larga scala Microsoft, che ricevono centinaia di migliaia di richieste al secondo. La porta anteriore si trova alla periferia della rete di Azure, assorbendo e isolando geograficamente gli attacchi di grandi volumi. Ciò può impedire che il traffico dannoso continui a essere superiore rispetto al perimetro della rete di Azure.

## <a name="caching"></a>Memorizzazione nella cache

Le [funzionalità di memorizzazione nella cache della porta anteriore](concept-caching.md) possono essere usate per proteggere i backend da volumi di traffico elevati generati da un attacco. Le risorse memorizzate nella cache verranno restituite dai nodi perimetrali front-end in modo che non vengano trasmesse al back-end. Anche i tempi di scadenza della cache brevi (secondi o minuti) sulle risposte dinamiche possono ridurre notevolmente il carico dei servizi back-end. Per ulteriori informazioni sulla memorizzazione nella cache di concetti e modelli, vedere [considerazioni sulla memorizzazione nella](/azure/architecture/best-practices/caching) cache e [modello cache-aside](/azure/architecture/patterns/cache-aside).

## <a name="web-application-firewall-waf"></a>WAF (Web application firewall)

[Il Web Application Firewall (WAF) dello sportello anteriore](../../web-application-firewall/afds/afds-overview.md) può essere usato per attenuare molti tipi diversi di attacchi:

* L'utilizzo del set di regole gestite offre protezione contro molti attacchi comuni.
* Il traffico esterno a una determinata area geografica o all'interno di un'area definita può essere bloccato o reindirizzato a una pagina Web statica. Per altre informazioni, vedere [filtro geografico](../../web-application-firewall/afds/waf-front-door-geo-filtering.md).
* Gli indirizzi IP e gli intervalli identificati come dannosi possono essere bloccati.
* È possibile applicare la limitazione della frequenza per impedire che gli indirizzi IP chiamino il servizio troppo spesso.
* È possibile creare [regole WAF personalizzate](../../web-application-firewall/afds/waf-front-door-custom-rules.md) per bloccare e classificare automaticamente gli attacchi http o HTTPS con firme note.

## <a name="for-further-protection"></a>Per una maggiore protezione

Se è necessaria una protezione aggiuntiva, è possibile abilitare la [protezione DDoS di Azure standard](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) nel VNet in cui vengono distribuiti i back-end. I clienti di protezione DDoS standard ricevono maggiori vantaggi, tra cui:

* Protezione dei costi
* Garanzia SLA
* Accesso agli esperti del team di risposta rapida DDoS per assistenza immediata durante un attacco.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare uno standard/Premium per la porta anteriore](create-front-door-portal.md).
