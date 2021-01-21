---
title: Creare una VPN tra Azure e AWS usando le soluzioni gestite
description: Come creare una connessione VPN tra Azure e AWS usando le soluzioni gestite, anziché le VM o i dispositivi.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: ricmmartins
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/15/2021
ms.author: ricmart
ms.openlocfilehash: 3b9e60eb037182318e9d1ef7336565908a9c8f32
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664784"
---
# <a name="create-a-vpn-connection-between-azure-and-aws-using-managed-solutions"></a>Creare una connessione VPN tra Azure e AWS usando le soluzioni gestite

È possibile stabilire una connessione tra Azure e AWS usando le soluzioni gestite. In precedenza era necessario usare un dispositivo o una macchina virtuale che funge da risponditore. A questo punto, è possibile connettere il gateway privato virtuale AWS al gateway VPN di Azure direttamente senza doversi preoccupare della gestione delle risorse IaaS, ad esempio le macchine virtuali. Questo articolo consente di creare una connessione VPN tra Azure e AWS usando solo le soluzioni gestite.

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/diagram.png" alt-text="Diagramma dell'architettura":::

## <a name="configure-azure"></a>Configura Azure

### <a name="configure-a-virtual-network"></a>Configurare una rete virtuale

Configurare una rete virtuale. Per istruzioni, vedere la [Guida introduttiva alla rete virtuale](../virtual-network/quick-create-portal.md).

In questo articolo vengono usati i valori di esempio seguenti:

* **Gruppo di risorse:** RG-Azure-AWS
* **Area:** Stati Uniti orientali
* **Nome rete virtuale:** VNET-Azure
* **Spazio indirizzi IPv4:** 172.10.0.0/16
* **Nome subnet:** subnet-01
* **Intervallo di indirizzi subnet:** 172.10.1.0/24

### <a name="create-a-vpn-gateway"></a>Creare un gateway VPN

Creare un gateway VPN per la rete virtuale. Per istruzioni, vedere [esercitazione: creare e gestire un gateway VPN](tutorial-create-gateway-portal.md).

In questo articolo vengono usati i valori e le impostazioni di esempio seguenti:

* **Tipo di gateway:** VPN
* **Tipo di VPN:** basato su route
* **SKU:** VpnGw1
* **Generazione:** Generazione 1
* **Rete virtuale:** Deve essere il VNet per il quale si vuole creare il gateway.
* **Intervallo di indirizzi subnet del gateway:** 172.10.0.0/27
* **Indirizzo IP pubblico:** Creare un nuovo gruppo di risorse
* **Nome dell'indirizzo IP pubblico:** PIP-VPN-Azure-AWS
* **Abilitare la modalità Active-Active:** Disabilitare
* **Configurare BGP:** Disabilitare

Esempio:

:::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/summary.png" alt-text="Riepilogo del gateway di rete virtuale":::

## <a name="configure-aws"></a>Configurare AWS

1. Creare il cloud privato virtuale (VPC).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpc.png" alt-text="Crea informazioni VPC":::

1. Creare una subnet all'interno del VPC (rete virtuale).

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-subnet-vpc.png" alt-text="Creare la subnet":::

1. Creare un gateway cliente che punti all'indirizzo IP pubblico del gateway VPN di Azure. Il **gateway cliente** è una risorsa di AWS che contiene informazioni per AWS sul dispositivo del gateway cliente, che in questo caso è il gateway VPN di Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-customer-gw.png" alt-text="Crea gateway cliente":::

1. Creare il gateway privato virtuale.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpg.png" alt-text="Creare un gateway privato virtuale":::

1. Alleghi il gateway privato virtuale a VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-vpg.png" alt-text="Alleghi VPG a VPC":::

1. Selezionare il VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attaching-vpg.png" alt-text="Attach":::

1. Creare una connessione VPN da sito a sito.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-vpn-connection.png" alt-text="Crea connessione VPN":::

1. Impostare l'opzione di routing su **static** e puntare al prefisso Azure subnet-01 **(172.10.1.0/24).**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/set-static-route.png" alt-text="Impostazione di una route statica":::

1. Una volta completate le opzioni, **creare** la connessione.

1. Scaricare il file di configurazione. Per scaricare la configurazione corretta, modificare il fornitore, la piattaforma e il software in **generico**, perché Azure non è un'opzione valida.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/download-config.png" alt-text="Scarica configurazione":::

1. Il file di configurazione contiene la chiave precondivisa e l'indirizzo IP pubblico per ognuno dei due tunnel IPsec creati da AWS.

   **Tunnel 1**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1.png" alt-text="Tunnel 1":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-1-config.png" alt-text="Configurazione tunnel 1":::

   **Tunnel 2**

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2.png" alt-text="Tunnel 2":::

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/tunnel-2-config.png" alt-text="Configurazione tunnel 2":::

1. Una volta creati i tunnel, verrà visualizzato qualcosa di simile a questo esempio.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-details.png" alt-text="Dettagli della connessione VPN AWS":::

## <a name="create-local-network-gateway"></a>Creare un gateway di rete locale

In Azure il gateway di rete locale è una risorsa di Azure che in genere rappresenta una posizione locale. Viene popolato con le informazioni usate per connettersi al dispositivo VPN locale. Tuttavia, in questa configurazione il gateway di rete locale viene creato e popolato con le informazioni di connessione del gateway privato virtuale AWS. Per altre informazioni sui gateway di rete locale di Azure, vedere [impostazioni del gateway VPN di Azure](vpn-gateway-about-vpn-gateway-settings.md#lng).

Creare un gateway di rete locale in Azure. Per i passaggi, vedere [creare un gateway di rete locale](tutorial-site-to-site-portal.md#LocalNetworkGateway).

Specificare i valori seguenti:

* **Nome:** Nell'esempio viene usato lng-Azure-AWS.
* **Endpoint:** Indirizzo IP
* **Indirizzo IP:** Indirizzo IP pubblico dal gateway privato virtuale AWS e prefisso CIDR VPC. È possibile trovare l'indirizzo IP pubblico nel file di configurazione scaricato in precedenza.

AWS crea due tunnel IPsec per scopi di disponibilità elevata. Nell'esempio seguente viene illustrato l'indirizzo IP pubblico del tunnel IPsec #1.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/local-network-gateway.png" alt-text="Gateway di rete locale":::

## <a name="create-vpn-connection"></a>Crea connessione VPN

In questa sezione viene creata la connessione VPN tra il gateway di rete virtuale di Azure e il gateway AWS.

1. Creare la connessione di Azure. Per i passaggi necessari per creare una connessione, vedere [creare una connessione VPN](tutorial-site-to-site-portal.md#CreateConnection).

   Nell'esempio seguente, la chiave condivisa è stata ottenuta dal file di configurazione scaricato in precedenza. In questo esempio vengono usati i valori per il tunnel IPsec #1 creati da AWS e descritti nel file di configurazione.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection.png" alt-text="Oggetto connessione di Azure":::

1. Visualizzare la connessione. Dopo alcuni minuti, viene stabilita la connessione.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/connection-established.png" alt-text="Connessione funzionante":::

1. Verificare che il tunnel IPsec di AWS #1 sia **attivo**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-connection-established.png" alt-text="Verificare che il tunnel AWS sia attivo":::

1. Modificare la tabella di route associata a VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/edit-aws-route.png" alt-text="Modificare la route":::

1. Aggiungere la route alla subnet di Azure. Questa route verrà spostata attraverso il gateway VPC.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/save-aws-route.png" alt-text="Salvare la configurazione della route":::

## <a name="configure-second-connection"></a>Configura seconda connessione

In questa sezione viene creata una seconda connessione per garantire la disponibilità elevata.

1. Creare un altro gateway di rete locale che punti all'indirizzo IP pubblico del tunnel IPsec #2 in AWS. Si tratta del gateway standby.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-lng-standby.png" alt-text="Creare il gateway di rete locale":::

1. Creare la seconda connessione VPN da Azure a AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-connection-standby.png" alt-text="Creare la connessione del gateway di rete locale di standby":::

1. Visualizzare le connessioni del gateway VPN di Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-tunnels.png" alt-text="Stato connessione di Azure":::

1. Visualizzare le connessioni AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-tunnels.png" alt-text="Stato connessione AWS":::

Sono ora stabilite le connessioni.

## <a name="test-connections"></a>Testare le connessioni

1. Aggiungere un gateway Internet al VPC in AWS. Il gateway Internet è una connessione logica tra una VPN Amazon e Internet. Questa risorsa consente di connettersi attraverso la macchina virtuale di test dall'IP pubblico di AWS tramite Internet. Questa risorsa non è necessaria per la connessione VPN. Viene usato solo per eseguire il test.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/create-igw.png" alt-text="Creare il gateway Internet":::

1. Selezionare **Connetti a VPC**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw.png" alt-text="Collegamento del gateway Internet a VPC":::

1. Selezionare un VPC e **alleghi il gateway Internet**.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/attach-igw-2.png" alt-text="Collegamento del gateway":::

1. Creare una route per consentire le connessioni a **0.0.0.0/0** (Internet) tramite il gateway Internet.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/allow-internet-igw.png" alt-text="Configurare la route tramite il gateway":::

1. In Azure, la route viene creata automaticamente. È possibile controllare la route dalla macchina virtuale di Azure selezionando **vm > rete > interfaccia di rete > route valide**. Vengono visualizzate 2 Route, 1 route per connessione.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-effective-routes.png" alt-text="Verificare le route valide":::

1. Da una VM Linux in Azure, l'ambiente è simile all'esempio seguente.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-overview.png" alt-text="Panoramica di Azure dalla VM Linux":::

1. Da una VM Linux in AWS, l'ambiente è simile all'esempio seguente.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-overview.png" alt-text="Panoramica di AWS dalla VM Linux":::

1. Testare la connettività dalla macchina virtuale di Azure.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/azure-ping.png" alt-text="Eseguire il ping del test da Azure":::

1. Testare la connettività dalla macchina virtuale AWS.

   :::image type="content" source="./media/create-vpn-azure-aws-managed-solutions/aws-ping.png" alt-text="Ping test da AWS":::

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul supporto di AWS per IKEv2, vedere l' [articolo AWS](https://aws.amazon.com/about-aws/whats-new/2019/02/aws-site-to-site-vpn-now-supports-ikev2/).
