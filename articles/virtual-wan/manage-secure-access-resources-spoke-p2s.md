---
title: Gestire l'accesso sicuro alle risorse in spoke reti virtuali per i client P2S
titleSuffix: Azure Virtual WAN
description: Questo articolo illustra come usare la rete WAN virtuale di Azure e le regole del firewall di Azure per gestire l'accesso sicuro alle reti virtuali per i client VPN (da punto a sito) dell'utente.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cherylmc
ms.openlocfilehash: 751d11fcd4b5d4c33145ee7f2b7b49971b8927ae
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048260"
---
# <a name="manage-secure-access-to-resources-in-spoke-vnets-for-user-vpn-clients"></a>Gestire l'accesso sicuro alle risorse in spoke reti virtuali per i client VPN utente

Questo articolo illustra come usare le regole e i filtri della rete WAN virtuale e del firewall di Azure per gestire l'accesso sicuro per le connessioni alle risorse in Azure tramite IKEv2 da punto a sito o connessioni VPN aperte. Questa configurazione è utile se si dispone di utenti remoti per i quali si vuole limitare l'accesso alle risorse di Azure o per proteggere le risorse in Azure.

I passaggi descritti in questo articolo consentono di creare l'architettura nel diagramma seguente per consentire ai client VPN utente di accedere a una risorsa specifica (VM1) in un VNet spoke connesso all'hub virtuale, ma non altre risorse (VM2). Usare questo esempio di architettura come linee guida di base.

:::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/diagram.png" alt-text="Diagramma: hub virtuale protetto" :::

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [Prerequisites](../../includes/virtual-wan-before-include.md)]

* Sono disponibili i valori per la configurazione di autenticazione che si vuole usare. Ad esempio, un server RADIUS, Azure Active Directory l'autenticazione o [generare ed esportare i certificati](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

## <a name="create-a-virtual-wan"></a>Creare una rete WAN virtuale

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="define-p2s-configuration-parameters"></a><a name= "p2s-config"></a>Definire i parametri di configurazione di P2S

La configurazione da punto a sito (P2S) definisce i parametri per la connessione dei client remoti. Questa sezione consente di definire i parametri di configurazione di P2S e di creare la configurazione che verrà usata per il profilo client VPN. Le istruzioni seguite dipendono dal metodo di autenticazione che si vuole usare.

### <a name="authentication-methods"></a>Metodi di autenticazione

Quando si seleziona il metodo di autenticazione, sono disponibili tre opzioni. Ogni metodo presenta requisiti specifici. Selezionare uno dei metodi seguenti e quindi completare la procedura.

* **Autenticazione Azure Active Directory:** Ottenere quanto segue:

   * **ID applicazione** dell'applicazione aziendale VPN di Azure registrata nel tenant del Azure ad.
   * **Autorità emittente**. Esempio: `https://sts.windows.net/your-Directory-ID`.
   * **Tenant Azure ad**. Esempio: `https://login.microsoftonline.com/your-Directory-ID`.

* **Autenticazione basata su RADIUS:** Ottenere l'indirizzo IP del server RADIUS, il segreto del server RADIUS e le informazioni sul certificato.

* **Certificati di Azure:** Per questa configurazione sono necessari i certificati. È necessario generare o ottenere i certificati. Per ogni client è necessario un certificato client. Inoltre, è necessario caricare le informazioni sul certificato radice (chiave pubblica). Per ulteriori informazioni sui certificati richiesti, vedere [generare ed esportare i certificati](../vpn-gateway/vpn-gateway-certificates-point-to-site.md).

[!INCLUDE [Define parameters](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-the-hub-and-gateway"></a><a name="hub"></a>Creare hub e gateway

In questa sezione viene creato l'hub virtuale con un gateway da punto a sito. Quando si configura, è possibile usare i valori di esempio seguenti:

* **Spazio di indirizzi IP privato dell'hub:** 10.0.0.0/24
* **Pool di indirizzi client:** 10.5.0.0/16
* **Server DNS personalizzati:** È possibile elencare fino a 5 server DNS

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="generate-vpn-client-configuration-files"></a><a name="generate"></a>Generare i file di configurazione del client VPN

In questa sezione vengono generati e scaricati i file del profilo di configurazione. Questi file vengono usati per configurare il client VPN nativo nel computer client. Per informazioni sul contenuto dei file del profilo client, vedere [configurazione da punto a sito-certificati](../vpn-gateway/point-to-site-vpn-client-configuration-azure-cert.md).

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="clients"></a>Configurare client VPN

Usare il profilo scaricato per configurare i client di accesso remoto. La procedura per ogni sistema operativo è diversa; seguire le istruzioni applicabili al sistema in uso.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="connect-the-spoke-vnet"></a><a name="connect-spoke"></a>Connettere il VNet spoke

In questa sezione si connette la rete virtuale spoke all'hub WAN virtuale.

[!INCLUDE [Connect spoke virtual network](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="create-virtual-machines"></a><a name="create-vm"></a>Creare macchine virtuali

In questa sezione vengono create due macchine virtuali in VNet, VM1 e VM2. Nel diagramma di rete si usano 10.18.0.4 e 10.18.0.5. Quando si configurano le macchine virtuali, assicurarsi di selezionare la rete virtuale creata (disponibile nella scheda rete). Per la procedura di creazione di una macchina virtuale, vedere [Guida introduttiva: creare una macchina virtuale](../virtual-machines/windows/quick-create-portal.md).

## <a name="secure-the-virtual-hub"></a><a name="secure"></a>Proteggere l'hub virtuale

Un hub virtuale standard non ha criteri di sicurezza predefiniti per proteggere le risorse nelle reti virtuali spoke. Un hub virtuale protetto usa il firewall di Azure o un provider di terze parti per gestire il traffico in ingresso e in uscita per proteggere le risorse in Azure.

Convertire l'hub in un hub protetto usando l'articolo seguente: [configurare il firewall di Azure in un hub WAN virtuale](howto-firewall.md).

## <a name="create-rules-to-manage-and-filter-traffic"></a><a name= "create-rules"></a> Creare regole per gestire e filtrare il traffico

Creare regole che impongono il comportamento del firewall di Azure. Proteggendo l'hub, si garantisce che tutti i pacchetti che entrano nell'hub virtuale siano soggetti all'elaborazione del firewall prima di accedere alle risorse di Azure.

Una volta completati questi passaggi, si creerà un'architettura che consente agli utenti VPN di accedere alla macchina virtuale con l'indirizzo IP privato 10.18.0.4, ma **non** di accedere alla macchina virtuale con l'indirizzo IP privato 10.18.0.5

1. Nella portale di Azure passare a **Firewall Manager**.
1. In sicurezza selezionare **criteri del firewall di Azure**.
1. Selezionare **Crea criterio firewall di Azure**.
1. In **Dettagli criterio** Digitare un nome e selezionare l'area in cui è distribuito l'hub virtuale.
1. Selezionare **Avanti: Impostazioni DNS (anteprima)** .
1. Selezionare **Avanti: Regole**.
1. Nella scheda **Regole** selezionare **Aggiungi una raccolta regole**.
1. Consente di specificare un nome per la raccolta. Impostare il tipo come **rete**. Aggiungere un valore di priorità **100**.
1. Immettere il nome della regola, il tipo di origine, l'origine, il protocollo, le porte di destinazione e il tipo di destinazione, come illustrato nell'esempio riportato di seguito. Quindi selezionare **Aggiungi**. Questa regola consente a qualsiasi indirizzo IP del pool di client VPN di accedere alla macchina virtuale con l'indirizzo IP privato 10.18.04, ma non tutte le altre risorse connesse all'hub virtuale. Creare le regole desiderate in base alle regole di architettura e autorizzazioni desiderate.

   :::image type="content" source="./media/manage-secure-access-resources-spoke-p2s/rules.png" alt-text="Regole del firewall" :::

1. Selezionare **Avanti: Intelligence sulle minacce**.
1. Selezionare **Avanti: Hub**.
1. Nella scheda **Hub** selezionare **Associa hub virtuali**.
1. Selezionare l'hub virtuale creato in precedenza e quindi selezionare **Aggiungi**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.

Il completamento di questo processo può richiedere più di 5 minuti.

## <a name="route-traffic-through-azure-firewall"></a><a name="send"></a>Instradare il traffico attraverso il firewall di Azure

In questa sezione è necessario assicurarsi che il traffico venga instradato attraverso il firewall di Azure.

1. Nel portale di **gestione firewall** selezionare **Hub virtuali protetti**.
1. Selezionare l'hub virtuale creato.
1. In **Impostazioni** selezionare **Configurazione della sicurezza**.
1. In **Traffico privato** selezionare **Send via Azure Firewall** (Invia tramite Firewall di Azure).
1. Verificare che la connessione VNet e il traffico privato della connessione Branch siano protetti dal firewall di Azure.
1. Selezionare **Salva**.

## <a name="validate"></a><a name="validate"></a>Convalida

Verificare la configurazione dell'hub protetto.

1. Connettersi all' **hub virtuale protetto** tramite VPN dal dispositivo client.
1. Effettuare il ping dell'indirizzo IP **10.18.0.4** dal client. Verrà visualizzata una risposta.
1. Effettuare il ping dell'indirizzo IP **10.18.0.5** dal client. Non è possibile visualizzare una risposta.

### <a name="considerations"></a>Considerazioni

* Assicurarsi che la **tabella route valide** nell'hub virtuale protetto disponga dell'hop successivo per il traffico privato da parte del firewall. Per accedere alla tabella route valide, passare alla risorsa dell' **hub virtuale** . In **connettività** selezionare **routing**, quindi selezionare **Route effettive**. Da qui, selezionare la tabella di route **predefinita** .
* Verificare di aver creato le regole nella sezione [creare regole](#create-rules) . Se questi passaggi vengono persi, le regole create non verranno effettivamente associate all'hub e la tabella di route e il flusso di pacchetti non useranno il firewall di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti sulla rete WAN virtuale](virtual-wan-faq.md).
* Per altre informazioni sul firewall di Azure, vedere le [domande frequenti su firewall di Azure](../firewall/firewall-faq.yml).
