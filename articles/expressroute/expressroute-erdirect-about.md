---
title: Informazioni su Azure ExpressRoute Direct
description: Scopri le funzionalità chiave di Azure ExpressRoute Direct e le informazioni necessarie per l'onboarding in ExpressRoute Direct, come gli SKU disponibili e i requisiti tecnici.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: duau
ms.openlocfilehash: 56d6a76991c4386be45b2c18f4edb3d363e58fa5
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027143"
---
# <a name="about-expressroute-direct"></a>Informazioni su ExpressRoute Direct

ExpressRoute Direct offre la possibilità di connettersi direttamente alla rete globale di Microsoft presso le sedi di peering distribuite in modo strategico in tutto il mondo. ExpressRoute Direct offre connettività dual 100 Gbps o 10 Gbps, che supporta la connettività attiva/attiva su larga scala. È possibile usare qualsiasi provider di servizi per ER Direct.

Tra le funzionalità principali di ExpressRoute Direct sono incluse:

* Inserimento dati massiccio in servizi quali Archiviazione e Cosmos DB
* Isolamento fisico per i settori regolamentati che richiedono una connettività dedicata e isolata, ad esempio banche, enti pubblici e punti vendita al dettaglio
* Controllo granulare della distribuzione del circuito in base alla business unit

## <a name="onboard-to-expressroute-direct"></a>Onboarding in ExpressRoute Direct

Prima di usare ExpressRoute Direct, è necessario prima registrare la sottoscrizione. Per eseguire la registrazione, eseguire i comandi seguenti usando Azure PowerShell:

1.  Accedere ad Azure e selezionare la sottoscrizione che si vuole registrare.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Registrare la sottoscrizione per l'anteprima pubblica usando il comando seguente:
1. 
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Al termine della registrazione, verificare che il provider di risorse **Microsoft. Network** sia registrato nella sottoscrizione. La registrazione di un provider di risorse configura la sottoscrizione per l'utilizzo del provider di risorse.

1. Accedere alle impostazioni della sottoscrizione come descritto in [tipi e provider di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md).

1. Nella sottoscrizione, per i **provider di risorse**, verificare che il provider **Microsoft. Network** indichi uno stato **registrato** . Se il provider di risorse Microsoft. Network non è presente nell'elenco dei provider registrati, aggiungerlo.

Se si inizia a usare ExpressRoute Direct e si nota che non sono presenti porte disponibili nella località di peering scelta, inviare un messaggio di posta elettronica ExpressRouteDirect@microsoft.com per richiedere ulteriore inventario.

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute tramite un provider di servizi ed ExpressRoute Direct

| **ExpressRoute tramite un provider di servizi** | **ExpressRoute Direct** | 
| --- | --- |
| USA i provider di servizi per abilitare l'onboarding e la connettività veloci nell'infrastruttura esistente | Richiede l'infrastruttura 100 Gbps/10 Gbps e la gestione completa di tutti i livelli
| Si integra con centinaia di provider, tra cui Ethernet e MPLS | Capacità diretta/dedicata per settori regolamentati e inserimento dati massiccio |
| SKU di circuiti da 50 Mbps a 10 Gbps | Il cliente può selezionare una combinazione degli SKU di circuito seguenti su 100-Gbps ExpressRoute Direct: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> Il cliente può selezionare una combinazione degli SKU di circuito seguenti su ExpressRoute Direct da 10 Gbps:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Ottimizzato per tenant singolo | Ottimizzato per un singolo tenant con più business unit e più ambienti di lavoro

## <a name="expressroute-direct-circuits"></a>Circuiti di ExpressRoute Direct

Microsoft Azure ExpressRoute consente di estendere la rete locale nel cloud Microsoft tramite una connessione privata semplificata da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, ad esempio Microsoft Azure e Microsoft 365.

Ogni località di peering ha accesso alla rete globale di Microsoft e può accedere a qualsiasi area in una zona geopolitica per impostazione predefinita. È possibile accedere a tutte le aree globali con un circuito Premium.  

La funzionalità nella maggior parte degli scenari è equivalente ai circuiti che usano un provider di servizi ExpressRoute per operare. Per supportare un'ulteriore granularità e le nuove funzionalità offerte da ExpressRoute Direct, sono disponibili alcune funzionalità chiave nei circuiti di ExpressRoute Direct.

## <a name="circuit-skus"></a>SKU di circuiti

ExpressRoute Direct supporta scenari di inserimento dati massiccio in Archiviazione di Azure e altri servizi Big Data. I circuiti ExpressRoute su 100-Gbps ExpressRoute Direct ora supportano anche gli SKU di circuito **40 Gbps** e * * 100-Gbps. Le coppie di porte fisiche sono solo a **100 Gbps o 10 Gbps** e possono avere più circuiti virtuali. Dimensioni del circuito:

| **100-Gbps ExpressRoute Direct** | **ExpressRoute diretto da 10 Gbps** | 
| --- | --- |
| **Larghezza di banda sottoscritta**: 200 Gbps | **Larghezza di banda sottoscritta**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Requisiti tecnici

* Interfacce di Microsoft Enterprise Edge router (MSEE):
    * Doppio porte Ethernet da 10 Gigabit o 100 Gigabit solo tra coppie di router
    * Connettività Fiber LR in modalità singola
    * IPv4 e IPv6
    * IP MTU 1500 byte

* Switch/router livello 2/livello 3 connettività:
    * Deve supportare 1 tag 802.1 Q (Dot1Q) o l'incapsulamento di due tag 802.1 Q (QinQ)
    * Ethertype = 0x8100
    * È necessario aggiungere il tag di VLAN esterno (STAG) in base all'ID VLAN specificato da Microsoft, *applicabile solo su QinQ*
    * Deve supportare più sessioni BGP (VLAN) per porta e dispositivo
    * Connettività IPv4 e IPv6. *Per IPv6 non verrà creata alcuna sottointerfaccia aggiuntiva. L'indirizzo IPv6 verrà aggiunto alla sottointerfaccia esistente*. 
    * Facoltativo: supporto del [rilevamento di inoltri bidirezionale (BFD)](./expressroute-bfd.md) , configurato per impostazione predefinita in tutti i peering privati nei circuiti ExpressRoute

## <a name="vlan-tagging"></a>Codifica VLAN

ExpressRoute Direct supporta la codifica VLAN QinQ e Dot1Q.

* La **codifica VLAN QinQ** consente domini di routing isolati per circuito di ExpressRoute. Azure fornisce dinamicamente un tag S alla creazione del circuito e non può essere modificato. Ogni peering sul circuito (privato e Microsoft) utilizzerà un tag C univoco come VLAN. Non è necessario che il tag C sia univoco nei circuiti sulle porte dirette ExpressRoute.

* La **codifica VLAN Dot1Q** consente una singola VLAN con tag per coppia di porte di ExpressRoute Direct. Un C-Tag usato in un peering deve essere univoco tra i circuiti e i peering sulla coppia di porte di ExpressRoute Direct.

## <a name="workflow"></a>Flusso di lavoro

[![flusso](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Contratto di servizio

ExpressRoute Direct offre lo stesso contratto di servizio di livello enterprise con connessioni ridondanti attivo/attivo nella rete globale Microsoft. L'infrastruttura ExpressRoute è ridondante e la connettività alla rete globale Microsoft è ridondante e diversificata e viene ridimensionata correttamente con i requisiti dei clienti. 

## <a name="next-steps"></a>Passaggi successivi

[Configurare ExpressRoute Direct](expressroute-howto-erdirect.md)
