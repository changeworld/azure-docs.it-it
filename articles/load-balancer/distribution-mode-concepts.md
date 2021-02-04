---
title: Modalità di distribuzione Azure Load Balancer
description: Inizia a imparare a usare le diverse modalità di distribuzione di Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551317"
---
# <a name="azure-load-balancer-distribution-modes"></a>Modalità di distribuzione Azure Load Balancer

Azure Load Balancer supporta due modalità di distribuzione per il routing delle connessioni all'applicazione con carico bilanciato:

* Basato su hash
* Affinità dell'IP di origine

## <a name="hash-based"></a>Basato su hash

La modalità di distribuzione predefinita per Azure Load Balancer è un hash a cinque tuple. 

La tupla è composta da:
* **IP di origine**
* **Porta di origine**
* **IP di destinazione**
* **Porta di destinazione**
* **Protocol type (Tipo di protocollo)**

L'hash viene usato per eseguire il mapping del traffico ai server disponibili. L'algoritmo fornisce la viscosità solo all'interno di una sessione di trasporto. I pacchetti che si trovano nella stessa sessione vengono indirizzati allo stesso indirizzo IP del Data Center dietro l'endpoint con carico bilanciato. Quando il client avvia una nuova sessione dallo stesso IP di origine, la porta di origine cambia e fa in modo che il traffico passi a un endpoint del data center diverso.

![Modalità di distribuzione basata su hash a cinque tuple](./media/distribution-mode-concepts/load-balancer-distribution.png)

La modalità basata su hash ha un tipo di configurazione:

* **None (basato su hash)** : specifica che le richieste successive provenienti dallo stesso client possono essere gestite da qualsiasi macchina virtuale.

## <a name="source-ip-affinity"></a>Affinità dell'IP di origine

Questa modalità di distribuzione è nota anche come affinità di sessione o affinità IP client. La modalità usa un hash a due Tuple (IP di origine e IP di destinazione) o a tre Tuple (IP di origine, IP di destinazione e tipo di protocollo) per eseguire il mapping del traffico ai server disponibili. 

Utilizzando l'affinità IP di origine, le connessioni avviate dallo stesso computer client passano allo stesso endpoint del Data Center.

Nella figura seguente viene illustrata una configurazione a due Tuple. Si noti come le due tuple vengono eseguite tramite il servizio di bilanciamento del carico nella macchina virtuale 1 (VM1). Quindi viene eseguito il backup di VM1 in VM2 e VM3.

![Modalità di distribuzione affinità di sessione con due Tuple](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

La modalità di affinità IP di origine ha due tipi di configurazione:

* **IP client (affinità IP di origine 2-tupla)** : specifica che le richieste successive provenienti dallo stesso indirizzo IP client verranno gestite dalla stessa macchina virtuale.
* **IP e protocollo client (affinità IP di origine 3-tupla)** : specifica che le richieste successive provenienti dalla stessa combinazione di indirizzo IP e protocollo client verranno gestite dalla stessa macchina virtuale.

## <a name="use-cases"></a>Casi d'uso

Affinità IP di origine con IP e protocollo client (affinità IP di origine 3-tupla), risolve un'incompatibilità tra Azure Load Balancer e gateway di Desktop remoto (Gateway Desktop remoto). 

Un altro scenario d'uso è il caricamento di contenuti multimediali. Il caricamento dei dati avviene tramite UDP, ma il piano di controllo viene raggiunto tramite TCP:

* Un client avvia una sessione TCP all'indirizzo pubblico con carico bilanciato e viene indirizzata a un DIP specifico. Il canale viene lasciato attivo per monitorare lo stato della connessione.
* Viene avviata una nuova sessione UDP dallo stesso computer client allo stesso endpoint pubblico con carico bilanciato. La connessione viene indirizzata allo stesso endpoint DIP della connessione TCP precedente. Il caricamento del supporto può essere eseguito con una velocità effettiva elevata mantenendo un canale di controllo tramite TCP.

> [!NOTE]
> Quando il set con carico bilanciato viene modificato (rimozione o aggiunta di una macchina virtuale), la distribuzione delle richieste client viene ricalcolata. Non è possibile dipendere da nuove connessioni da client esistenti che terminano nello stesso server. Inoltre, l'uso della modalità di distribuzione dell'affinità IP di origine può comportare una distribuzione diversa del traffico. I client in esecuzione dietro i proxy possono essere visti come un'applicazione client univoca.


## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come configurare la modalità di distribuzione di Azure Load Balancer, vedere [configurare la modalità di distribuzione per Azure Load Balancer](load-balancer-distribution-mode.md).

