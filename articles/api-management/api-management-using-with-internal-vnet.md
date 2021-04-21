---
title: Usare Azure API Management con reti virtuali interne
titleSuffix: Azure API Management
description: Informazioni su come installare e configurare Gestione API di Azure in una rete virtuale interna.
services: api-management
documentationcenter: ''
author: vladvino
editor: ''
ms.service: api-management
ms.topic: how-to
ms.date: 04/12/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 35d813b6dfedbd7f76a88713757ce83c2644ff95
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813147"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Uso del servizio Gestione API di Azure con una rete virtuale interna
Grazie alle reti virtuali di Azure, Gestione API è in grado di gestire API non accessibili su Internet. Sono disponibili varie tecnologie VPN per stabilire la connessione. È possibile distribuire Gestione API in due modalità principali all'interno di una rete virtuale:
* Esterno
* Interno

Quando API Management distribuisce in modalità rete virtuale interna, tutti gli endpoint di servizio (gateway proxy, portale per sviluppatori, gestione diretta e Git) sono visibili solo all'interno di una rete virtuale a cui si controlla l'accesso. Nessuno degli endpoint di servizio è registrato nel server DNS pubblico.

> [!NOTE]
> Poiché non sono presenti voci DNS per gli endpoint di servizio, questi endpoint non saranno accessibili fino a quando [non viene](#apim-dns-configuration) configurato DNS per la rete virtuale.

Usando Gestione API in modalità interna è possibile implementare gli scenari seguenti:

* Consentire un accesso esterno sicuro da parte di terzi alle API ospitate in un data center privato, tramite connessioni VPN da sito a sito o Azure ExpressRoute.
* Abilitare scenari cloud ibridi esponendo le API basate su cloud e locali tramite un gateway comune.
* Gestire le API ospitate in più aree geografiche usando un singolo endpoint del gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, è necessario disporre di:

+ **Sottoscrizione di Azure attiva.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Un'istanza di Gestione API**. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

Quando un API Management viene distribuito in una rete [](./api-management-using-with-vnet.md#required-ports) virtuale, viene usato un elenco di porte che devono essere aperte. 

## <a name="creating-an-api-management-in-an-internal-virtual-network"></a><a name="enable-vpn"> </a>Creazione di un servizio Gestione API in una rete virtuale interna
Il API Management in una rete virtuale interna è ospitato dietro uno SKU Basic del servizio di bilanciamento del carico interno se il servizio viene creato con l'API client versione 2020-12-01. Per il servizio creato con client con API versione 2021-01-01-preview e con un indirizzo IP pubblico dalla sottoscrizione del cliente, è ospitato dietro uno SKU Standard del servizio di bilanciamento del carico interno. Per altre informazioni, vedere Azure Load Balancer [SKU](../load-balancer/skus.md).

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Abilitare la connessione a una rete virtuale usando il portale di Azure

1. Selezionare l'istanza di Gestione API di Azure nel [portale di Azure](https://portal.azure.com/).
1. Selezionare **Rete virtuale**.
1. Configurare il **tipo di** accesso Interno. Per la procedura dettagliata, vedere [Abilitare la connettività della rete virtuale usando portale di Azure](api-management-using-with-vnet.md#enable-vnet-connectivity-using-the-azure-portal).

    ![Menu per la configurazione di Gestione API di Azure in una rete virtuale interna][api-management-using-internal-vnet-menu]

4. Selezionare **Salva**.

Al termine della distribuzione,  nel pannello panoramica  dovrebbero essere visualizzati l'indirizzo IP virtuale privato e l'indirizzo IP virtuale pubblico API Management servizio. **L'indirizzo** IP virtuale privato è un indirizzo IP con carico bilanciato dall'interno della subnet delegata API Management cui è possibile accedere agli endpoint , e `gateway` `portal` `management` `scm` . **L'indirizzo** IP virtuale  pubblico viene usato solo per il traffico del piano di controllo verso l'endpoint sulla porta 3443 e può essere bloccato nel tag del servizio `management` [ApiManagement.][ServiceTags]

![Dashboard di Gestione API con una rete virtuale interna configurata][api-management-internal-vnet-dashboard]

> [!NOTE]
> La console di test disponibile nel portale di Azure non funzionerà per il pacchetto distribuito VNET **interno**, poiché l'URL del gateway non è registrato nel DNS pubblico. In questo caso, è necessario usare la console di test disponibile nel **portale per sviluppatori**.

### <a name="deploy-api-management-into-virtual-network"></a><a name="deploy-apim-internal-vnet"> </a>Distribuire API Management nella rete virtuale

È anche possibile abilitare la connettività di rete virtuale usando i metodi seguenti.


### <a name="api-version-2020-12-01"></a>API versione 2020-12-01

* Azure Resource Manager [modello](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-internal-vnet)

     [![Distribuisci in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-internal-vnet%2Fazuredeploy.json)

* Azure PowerShell cmdlet - [Creare o](/powershell/module/az.apimanagement/new-azapimanagement) aggiornare [un'istanza](/powershell/module/az.apimanagement/update-azapimanagementregion) API Management in una rete virtuale

## <a name="dns-configuration"></a><a name="apim-dns-configuration"></a>Configurazione DNS
Quando si usa Gestione API in modalità di rete virtuale esterna, il servizio DNS è gestito da Azure. Per la modalità di rete virtuale interna, è necessario gestire un proprio DNS. È consigliabile DNS di Azure una zona privata e collegarla alla rete API Management virtuale in cui viene distribuito il servizio. Informazioni su come [configurare una zona privata in DNS di Azure](../dns/private-dns-getstarted-portal.md).

> [!NOTE]
> Il servizio Gestione API non ascolta le richieste in arrivo dagli indirizzi IP. Risponde solo alle richieste per il nome host configurato negli endpoint di servizio. Questi endpoint includono gateway, portale di Azure, portale per sviluppatori, endpoint di gestione diretta e GIT.

### <a name="access-on-default-host-names"></a>Accesso con i nomi host predefiniti
Quando si crea un servizio API Management, denominato "contosointernalvnet", ad esempio, gli endpoint di servizio seguenti sono configurati per impostazione predefinita:

   * Gateway o proxy: contosointernalvnet.azure-api.net

   * Portale per sviluppatori: contosointernalvnet.portal.azure-api.net

   * Il nuovo portale per sviluppatori: contosointernalvnet.developer.azure-api.net

   * Endpoint di gestione diretta: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Per accedere a questi endpoint del servizio Gestione API è possibile creare una macchina virtuale in una subnet connessa alla rete virtuale in cui viene distribuito Gestione API. Supponendo che l'indirizzo IP virtuale interno per il servizio sia 10.1.0.5, è possibile eseguire il mapping del file hosts, %SystemDrive%\drivers\etc\hosts, come indicato di seguito:

   * 10.1.0.5 contosointernalvnet.azure-api.net

   * 10.1.0.5 contosointernalvnet.portal.azure-api.net

   * 10.1.0.5 contosointernalvnet.developer.azure-api.net

   * 10.1.0.5 contosointernalvnet.management.azure-api.net

   * 10.1.0.5 contosointernalvnet.scm.azure-api.net

È quindi possibile accedere a tutti gli endpoint di servizio dalla macchina virtuale creata.
Se si usa un server DNS personalizzato in una rete virtuale, è anche possibile creare record DNS A e accedere a questi endpoint da qualsiasi punto nella rete virtuale.

### <a name="access-on-custom-domain-names"></a>Accesso con i nomi di dominio personalizzati

1. Se non si vuole accedere al servizio Gestione API con i nomi host predefiniti, è possibile configurare nomi di dominio personalizzati per tutti gli endpoint di servizio come mostrato nella figura seguente:

   ![Configurazione di un dominio personalizzato per Gestione API][api-management-custom-domain-name]

2. È quindi possibile creare record nel server DNS per accedere agli endpoint accessibili solo dall'interno della rete virtuale.

## <a name="routing"></a><a name="routing"> </a> Routing

* Un indirizzo IP virtuale *privato con* carico bilanciato dall'intervallo di subnet verrà riservato e usato per accedere API Management endpoint di servizio dall'interno della rete virtuale. Questo *indirizzo* IP privato è disponibile nel pannello Panoramica del servizio nella portale di Azure. Questo indirizzo deve essere registrato con i server DNS usati dalla rete virtuale.
* Un indirizzo *IP* pubblico (VIP) con carico bilanciato verrà riservato anche per fornire l'accesso all'endpoint del servizio di gestione sulla porta 3443. Questo *indirizzo* IP pubblico è disponibile nel pannello Panoramica per il servizio nel portale di Azure. *L'indirizzo* IP pubblico viene usato solo per il traffico del piano di controllo verso l'endpoint sulla porta 3443 e può essere bloccato nel tag del servizio `management` [ApiManagement.][ServiceTags]
* Gli indirizzi IP dell'intervallo IP della subnet verranno assegnati a ogni macchina virtuale nel servizio e verranno usati per accedere alle risorse all'interno della rete virtuale. Verrà usato un indirizzo IP pubblico (VIP) per accedere alle risorse esterne alla rete virtuale. Se gli elenchi di restrizioni IP vengono usati per proteggere le risorse all'interno della rete virtuale, è necessario specificare l'intero intervallo per la subnet in cui viene distribuito il servizio API Management per concedere o limitare l'accesso dal servizio.
* Gli indirizzi IP pubblici e privati con bilanciamento del carico sono disponibili nel pannello Panoramica della portale di Azure.
* Gli indirizzi IP assegnati per l'accesso pubblico e privato possono cambiare se il servizio viene rimosso da e quindi aggiunto nuovamente alla rete virtuale. In questo caso, potrebbe essere necessario aggiornare le registrazioni DNS, le regole di routing e gli elenchi di restrizioni IP all'interno della rete virtuale.

## <a name="related-content"></a><a name="related-content"> </a>Contenuto correlato
Per altre informazioni, vedere gli articoli seguenti:
* [Problemi comuni di configurazione di rete durante la configurazione di Gestione API di Azure in una rete virtuale][Common network configuration problems]
* [Domande frequenti sulla rete virtuale](../virtual-network/virtual-networks-faq.md)
* [Creazione di un record in DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
