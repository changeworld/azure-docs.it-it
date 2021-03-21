---
title: 'Guida introduttiva: indirizzare i servizi condivisi usando un modello ARM'
titleSuffix: Azure Virtual WAN
description: Questa Guida introduttiva illustra come configurare le route per accedere a un VNet del servizio condiviso con un carico di lavoro a cui si vuole che ogni VNet e ramo acceda usando un modello di Azure Resource Manager (modello ARM).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 03/05/2021
ms.author: cherylmc
ms.custom: subject-armqs
ms.openlocfilehash: f325dd445e778bf03049d2c9e2e00fed7a427ccf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443955"
---
# <a name="quickstart-route-to-shared-services-vnets-using-an-arm-template"></a>Guida introduttiva: indirizzare i servizi condivisi reti virtuali usando un modello ARM

Questa Guida introduttiva descrive come usare un modello di Azure Resource Manager (modello ARM) per configurare le route per accedere a un servizio condiviso VNet con i carichi di lavoro a cui si vuole accedere ogni VNet e ramo (VPN/ER/P2S). Esempi di questi carichi di lavoro condivisi possono includere macchine virtuali con servizi come controller di dominio o condivisioni file o servizi di Azure esposti internamente tramite [endpoint privato di Azure](../private-link/private-endpoint-overview.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f301-virtual-wan-with-route-tables%2fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Per questa configurazione sono necessari i dati del certificato di chiave pubblica. I dati di esempio sono disponibili nell'articolo. Tuttavia, i dati di esempio vengono forniti solo per soddisfare i requisiti del modello per creare un gateway P2S. Dopo il completamento del modello e la distribuzione delle risorse, è necessario aggiornare questo campo con i dati del certificato in modo che la configurazione funzioni. Vedere [certificati VPN utente](certificates-point-to-site.md#cer).

## <a name="review-the-template"></a><a name="review"></a>Rivedere il modello

Il modello usato in questo avvio rapido proviene dai [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/301-virtual-wan-with-route-tables). Il modello per questo articolo è troppo lungo per essere visualizzato qui. Per visualizzare il modello, vedere [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/301-virtual-wan-with-route-tables/azuredeploy.json).

Questa Guida introduttiva illustra come creare una distribuzione multihub WAN virtuale di Azure, inclusi tutti i gateway e le connessioni VNet. L'elenco dei parametri di input è stato mantenuto come minimo. Lo schema di indirizzamento IP può essere modificato modificando le variabili all'interno del modello. Lo scenario è illustrato più avanti in questo articolo [: VNet di servizi condivisi](scenario-shared-services-vnet.md) .

:::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Architettura di distribuzione" lightbox="./media/routing-scenarios/shared-service-vnet/shared-services.png":::

Questo modello consente di creare un ambiente WAN virtuale di Azure completamente funzionante con le risorse seguenti:

* 2 hub distinti in aree diverse.
* 4 reti virtuali di Azure (VNet).
* 2 connessioni VNet per ogni hub VWan.
* 1 gateway VPN da punto a sito (P2S) in ogni hub.
* 1 gateway VPN da sito a sito (S2S) in ogni hub.
* 1 gateway ExpressRoute in ogni hub.
* Le tabelle di route personalizzate RT_SHARED in ogni hub.
* Etichetta LBL_RT_SHARED per raggruppare RT_SHARED tabelle di route.

Nel modello sono definite più risorse di Azure:

* [**Microsoft. Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft. Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft. Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft. Network/hubroutetables**](/azure/templates/microsoft.network/virtualhubs/hubRouteTables)
* [**Microsoft. Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft. Network/vpngateways**](/azure/templates/microsoft.network/vpngateways)
* [**Microsoft. Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft. Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnServerConfigurations)

>[!NOTE]
> Questo modello ARM non crea le risorse sul lato cliente necessarie per la connettività ibrida. Dopo aver distribuito il modello, è comunque necessario creare e configurare i client VPN P2S, i rami VPN (siti locali) e connettere i circuiti ExpressRoute.
>

Per trovare altri modelli, vedere [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a><a name="deploy"></a>Distribuire il modello

Per distribuire il modello in modo corretto, è necessario usare il pulsante per distribuire in Azure e il portale di Azure, anziché altri metodi, per i motivi seguenti:

* Per creare la configurazione P2S, è necessario caricare i dati del certificato radice. Il campo dati non accetta i dati del certificato quando si usa PowerShell o l'interfaccia della riga di comando.
* Questo modello non funziona correttamente con Cloud Shell a causa del caricamento dei dati del certificato.
* Inoltre, è possibile modificare facilmente il modello e i parametri nel portale per supportare gli intervalli di indirizzi IP e altri valori.

1. Fare clic su **Distribuzione in Azure**.

   [![Distribuzione in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f301-virtual-wan-with-route-tables%2fazuredeploy.json)
1. Per visualizzare il modello, fare clic su **modifica modello**. In questa pagina è possibile modificare alcuni valori, ad esempio lo spazio degli indirizzi o il nome di determinate risorse. **Salva** per salvare le modifiche o **Elimina**.
1. Nella pagina modello immettere i valori. Per questo modello, i dati del certificato pubblico P2S sono obbligatori. Se si usa questo articolo come esercizio, è possibile usare i dati seguenti del file con estensione cer come dati di esempio per entrambi gli hub. Una volta che il modello è stato eseguito e la distribuzione è stata completata, per poter usare la configurazione P2S, è necessario sostituire queste informazioni con i [dati del certificato](certificates-point-to-site.md#cer) chiave pubblica per la propria distribuzione.

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. Al termine dell'immissione dei valori, selezionare **Verifica + crea**.
1. Nella pagina **Verifica e crea** , dopo i passaggi di convalida, selezionare **Crea**.
1. Per il completamento della distribuzione sono necessari circa 75 minuti. È possibile visualizzare lo stato di avanzamento nella pagina **Panoramica** modello. Se si chiude il portale, la distribuzione continuerà.

   :::image type="content" source="./media/quickstart-route-shared-services-template/template.png" alt-text="Esempio di distribuzione completata":::

## <a name="validate-the-deployment"></a><a name="validate"></a>Convalidare la distribuzione

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare **Gruppi di risorse** nel riquadro sinistro.
1. Selezionare il gruppo di risorse creato nella sezione precedente. Nella pagina **Overview (panoramica** ) verrà visualizzato qualcosa di simile a questo esempio: :::image type="content" source="./media/quickstart-route-shared-services-template/resources.png" alt-text="esempio di risorse" lightbox="./media/quickstart-route-shared-services-template/resources.png":::

1. Fare clic sulla rete WAN virtuale per visualizzare gli hub. Nella pagina WAN virtuale fare clic su ogni hub per visualizzare le connessioni e altre informazioni sull'hub.
   :::image type="content" source="./media/quickstart-route-shared-services-template/hub.png" alt-text="Esempio di hub" lightbox="./media/quickstart-route-shared-services-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>Completa la configurazione ibrida

Il modello non configura tutte le impostazioni necessarie per una rete ibrida. È necessario completare le configurazioni e le impostazioni seguenti, a seconda dei requisiti.

* [Configurare i rami VPN-siti locali](virtual-wan-site-to-site-portal.md#site)
* [Completare la configurazione VPN P2S](virtual-wan-point-to-site-portal.md)
* [Connettere i circuiti ExpressRoute](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando le risorse create non sono più necessarie, eliminarle. Alcune delle risorse WAN virtuali devono essere eliminate in un determinato ordine a causa delle dipendenze. Il completamento dell'eliminazione può richiedere circa 30 minuti.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Completare la configurazione VPN P2S](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [Configurare i rami VPN-siti locali](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [Connettere i circuiti ExpressRoute](virtual-wan-expressroute-portal.md)
