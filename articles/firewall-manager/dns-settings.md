---
title: Impostazioni DNS dei criteri del firewall di Azure
description: È possibile configurare i criteri del firewall di Azure con le impostazioni del server DNS e del proxy DNS.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: how-to
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: a02879c5322add16f89f77e2da39daec7d2b5f31
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633649"
---
# <a name="azure-firewall-policy-dns-settings"></a>Impostazioni DNS dei criteri del firewall di Azure

È possibile configurare un server DNS personalizzato e abilitare il proxy DNS per i criteri del firewall di Azure. È possibile configurare queste impostazioni quando si distribuisce il firewall o in un secondo momento dalla pagina **impostazioni DNS** .

## <a name="dns-servers"></a>Server DNS

Un server DNS gestisce e risolve i nomi di dominio in indirizzi IP. Per impostazione predefinita, il firewall di Azure usa DNS di Azure per la risoluzione dei nomi. L'impostazione del **server DNS** consente di configurare i server DNS per la risoluzione dei nomi del firewall di Azure. È possibile configurare uno o più server.

### <a name="configure-custom-dns-servers"></a>Configurare server DNS personalizzati

1. Selezionare il criterio del firewall.
2. In **Impostazioni** selezionare **impostazioni DNS**.
3. In **server DNS** è possibile digitare o aggiungere i server DNS esistenti precedentemente specificati nella rete virtuale.
4. Selezionare **Salva**.
5. Il firewall ora indirizza il traffico DNS ai server DNS specificati per la risoluzione dei nomi.

## <a name="dns-proxy"></a>Proxy DNS

È possibile configurare il firewall di Azure in modo che funga da proxy DNS. Un proxy DNS funge da intermediario per le richieste DNS dalle macchine virtuali client a un server DNS. Se si configura un server DNS personalizzato, è necessario abilitare il proxy DNS per evitare la mancata corrispondenza della risoluzione DNS e abilitare il filtro FQDN nelle regole di rete.

Se non si Abilita il proxy DNS, le richieste DNS dal client possono andare a un server DNS in un momento diverso o restituire una risposta diversa rispetto a quella del firewall. Il proxy DNS inserisce il firewall di Azure nel percorso delle richieste del client per evitare l'incoerenza.

Per la configurazione del proxy DNS sono necessari tre passaggi:

1. Abilitare il proxy DNS nelle impostazioni DNS del firewall di Azure.
2. Facoltativamente, configurare il server DNS personalizzato o usare il valore predefinito specificato.
3. Infine, è necessario configurare l'indirizzo IP privato del firewall di Azure come indirizzo DNS personalizzato nelle impostazioni del server DNS della rete virtuale. Ciò garantisce che il traffico DNS venga indirizzato al firewall di Azure.

### <a name="configure-dns-proxy"></a>Configurare il proxy DNS

Per configurare il proxy DNS, è necessario configurare l'impostazione dei server DNS della rete virtuale per l'uso dell'indirizzo IP privato del firewall. Abilitare quindi il proxy DNS nelle **impostazioni DNS** dei criteri del firewall di Azure.

#### <a name="configure-virtual-network-dns-servers"></a>Configurare i server DNS della rete virtuale

1. Selezionare la rete virtuale in cui il traffico DNS verrà instradato attraverso il firewall di Azure.
2. In **Impostazioni** selezionare **Server DNS**.
3. Selezionare **personalizzata** in **server DNS**.
4. Immettere l'indirizzo IP privato del firewall.
5. Selezionare **Salva**.

#### <a name="enable-dns-proxy"></a>Abilita proxy DNS

1. Selezionare i criteri del firewall di Azure.
2. In **Impostazioni** selezionare **impostazioni DNS**.
3. Per impostazione predefinita, il **proxy DNS** è disabilitato. Se abilitata, il firewall è in ascolto sulla porta 53 e invia le richieste DNS ai server DNS configurati.
4. Esaminare la configurazione dei **server DNS** per assicurarsi che le impostazioni siano appropriate per l'ambiente in uso.
5. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

[Filtro FQDN nelle regole di rete](fqdn-filtering-network-rules.md)