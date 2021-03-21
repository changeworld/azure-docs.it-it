---
title: 'Guida introduttiva: creare e configurare il server di route usando il portale di Azure'
description: In questa Guida introduttiva si apprenderà come creare e configurare un server di route usando il portale di Azure.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: f76c48af4f5ebc8013daad457f9973cf7792c7c6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547994"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>Guida introduttiva: creare e configurare il server di route usando il portale di Azure

Questo articolo illustra come configurare il server di route di Azure per il peering con un'appliance virtuale di rete nella rete virtuale usando il portale di Azure. Il server di route di Azure apprenderà le route dall'appliance virtuale di rete e le programmerà nelle macchine virtuali nella rete virtuale. Il server di route di Azure annuncia anche le route di rete virtuale per l'appliance virtuale di rete. Per altre informazioni, vedere [server di route di Azure](overview.md).

> [!IMPORTANT]
> Il server di route di Azure (anteprima) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Esaminare i [limiti del servizio per il server di route di Azure](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Creare un server di route

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Accedere al proprio account Azure e selezionare la sottoscrizione.

In un browser passare al [portale di Azure](https://portal.azure.com) e accedere con l'account Azure.

### <a name="create-a-route-server"></a>Creare un server di route

1. Passare a https://aka.ms/routeserver.

1. Selezionare **+ Crea nuovo server di route**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Screenshot della pagina di destinazione del server di route."::: 

1. Nella pagina **Crea un server di route** immettere o selezionare le informazioni necessarie.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="Screenshot della pagina create route server.":::     

    | Impostazioni | valore |
    |----------|-------|
    | Subscription | Selezionare la sottoscrizione di Azure che si vuole usare per distribuire il server di route. |
    | Resource group | Selezionare un gruppo di risorse in cui creare il server di route. Se non esiste già un gruppo di risorse, è possibile crearne uno nuovo. |
    | Nome | Immettere un nome per il server di route. |
    | Region | Selezionare l'area in cui verrà creato il server di route. Selezionare la stessa area della rete virtuale creata in precedenza per visualizzare la rete virtuale nell'elenco a discesa. |
    | Rete virtuale | Selezionare la rete virtuale in cui verrà creato il server di route. È possibile creare una nuova rete virtuale o usare una rete virtuale esistente. Se si usa una rete virtuale esistente, assicurarsi che la rete virtuale esistente disponga di spazio sufficiente per almeno una subnet/27 per soddisfare il requisito della subnet del server di route. Se la rete virtuale non è visualizzata nell'elenco a discesa, assicurarsi di aver selezionato il gruppo di risorse o l'area corretta. |
    | Subnet | Dopo aver creato o selezionato una rete virtuale, verrà visualizzato il campo subnet. Questa subnet è dedicata solo al server di routing. Selezionare **Gestisci configurazione subnet** e creare la subnet del server di route di Azure. Selezionare **+ subnet** e creare una subnet usando le linee guida seguenti:</br><br>-La subnet deve essere denominata *RouteServerSubnet*.</br><br>-La subnet deve essere almeno/27 o superiore.</br> |

1. Selezionare **Verifica + crea**, esaminare il riepilogo e quindi selezionare **Crea**. 

    > [!NOTE]
    > La distribuzione del server di route importerà circa 20 minuti.

## <a name="set-up-peering-with-nva"></a>Configurare il peering con appliance virtuale di sistema

La sezione consente di configurare il peering BGP con l'appliance virtuale di sistema.

1. Passare a [Route server](https://aka.ms/routeserver) nel portale di Azure e selezionare il server di route che si desidera configurare.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="Screenshot dell'elenco di server di route."::: 

1. Selezionare **peer** in *Impostazioni* nel pannello di navigazione sinistro. Quindi selezionare **+ Aggiungi** per aggiungere un nuovo peer.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="Screenshot della pagina di destinazione dei peer."::: 

1. Immettere le informazioni seguenti sul peer di appliance virtuale di sistema.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="Screenshot della pagina Aggiungi peer.":::

    | Impostazioni | valore |
    |----------|-------|
    | Nome | Assegnare un nome al peering tra il server di route e l'appliance virtuale di sistema. |
    | ASN |  Immettere il numero di sistema autonomo (ASN) dell'appliance virtuale di dispositivo. |
    | Indirizzo IPv4 | Immettere l'indirizzo IP dell'appliance virtuale di cui il server di route comunicherà per stabilire BGP. |

1. Selezionare **Aggiungi** per aggiungere questo peer.

## <a name="complete-the-configuration-on-the-nva"></a>Completare la configurazione nell'appliance virtuale di dispositivo

Per creare una sessione BGP, saranno necessari gli indirizzi IP peer del server di route di Azure e l'ASN per completare la configurazione nell'appliance virtuale di Azure. È possibile ottenere queste informazioni dalla pagina Panoramica del server di route.

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="Screenshot della pagina Panoramica del server di route.":::

## <a name="configure-route-exchange"></a>Configurare lo scambio delle route

Se si dispone di un gateway ExpressRoute e/o di un gateway VPN e si vuole scambiare le route con il server di route, è possibile abilitare lo scambio delle route.

1. Passare a [Route server](https://aka.ms/routeserver) nel portale di Azure e selezionare il server di route che si desidera configurare.

1. Selezionare **configurazione** in *Impostazioni* nel pannello di navigazione sinistro.

1. Selezionare **Abilita** per l'impostazione **ramo a ramo** e quindi selezionare **Salva**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="Screenshot su come abilitare lo scambio di route.":::

## <a name="clean-up-resources"></a>Pulire le risorse

Se il server di route di Azure non è più necessario, selezionare **Elimina** nella pagina Panoramica per effettuare il deprovisioning del server di route.

:::image type="content" source="./media/quickstart-configure-route-server-portal/delete-route-server.png" alt-text="Screenshot dell'eliminazione del server di route.":::

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato il server di route di Azure, continuare a conoscere il modo in cui il server di routing di Azure interagisce con i gateway ExpressRoute e VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute e supporto VPN di Azure](expressroute-vpn-support.md)
