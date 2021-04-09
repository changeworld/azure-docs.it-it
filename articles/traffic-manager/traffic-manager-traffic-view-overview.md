---
title: Visualizzazione traffico di Gestione traffico di Microsoft Azure
description: In questa introduzione viene illustrato il funzionamento di visualizzazione traffico di gestione traffico.
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 01/22/2021
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 66376655c61903761d93ea228c6d72fa05734353
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743050"
---
# <a name="traffic-manager-traffic-view"></a>Visualizzazione traffico di Gestione traffico

Gestione traffico fornisce il routing a livello di DNS (Domain Name System). Questo servizio consente agli utenti finali di essere indirizzati a endpoint integri in base al metodo di routing scelto. Visualizzazione traffico fornisce Gestione traffico con una visualizzazione delle basi utenti (a livello di granularità del resolver DNS) e del relativo modello di traffico. Quando si abilita Visualizzazione traffico, tali informazioni vengono elaborate per offrire approfondimenti attuabili. 

Con Visualizzazione traffico è possibile:
- comprendere dove si trovano le basi utente (fino a una granularità di livello del resolver DNS locale).
- visualizzare il volume di traffico (osservato come query DNS gestite da Gestione traffico di Azure) proveniente da tali aree.
- sapere qual è la latenza rappresentativa riscontrata dagli utenti.
- immergersi in modelli di traffico specifici da ognuna di queste basi utente alle aree di Azure in cui sono presenti endpoint. 

Ad esempio, è possibile usare Visualizzazione traffico per comprendere quali aree hanno una grande quantità di traffico, ma soffrono di latenze più elevate. Queste informazioni vengono quindi usate per pianificare l'espansione del footprint in nuove aree di Azure. In questo modo gli utenti avranno un'esperienza di latenza più bassa.

## <a name="how-traffic-view-works"></a>Funzionamento di Visualizzazione traffico

Visualizzazione traffico funziona osservando le query in arrivo ricevute negli ultimi sette giorni per un profilo. Dalle informazioni sulle query in ingresso, Visualizzazione traffico estrae l'IP di origine del resolver DNS usato per rappresentare la posizione degli utenti. Queste informazioni vengono raggruppate a livello di resolver DNS per creare aree di base dell'utente. Gestione traffico mantiene le informazioni geografiche degli indirizzi IP. Gestione traffico esamina quindi le aree di Azure a cui la query viene indirizzata e crea una mappa del flusso di traffico per gli utenti di tali aree.
 
Nel passaggio successivo, gestione traffico mette in correlazione l'area di base utente al mapping dell'area di Azure con le tabelle di latenza di intelligence di rete. Questa tabella viene mantenuta per le diverse reti degli utenti finali per comprendere la latenza media riscontrata dagli utenti da tali aree durante la connessione alle aree di Azure. Tutti questi calcoli vengono quindi combinati in base al livello IP del resolver DNS locale prima di essere presentati all'utente. È possibile usare le informazioni in vari modi.

La frequenza di aggiornamento dei dati di visualizzazione traffico dipende da più variabili di servizio interne. Tuttavia, i dati vengono aggiornati ogni 24 ore.

>[!NOTE]
>La latenza descritta in Visualizzazione traffico è una latenza rappresentativa tra l'utente finale e le aree di Azure a cui è stato connesso e non è la latenza di ricerca DNS. Visualizzazione traffico fa una stima del massimo sforzo della latenza tra il resolver DNS locale e l'area di Azure a cui è stata indirizzata la query. Se i dati disponibili sono insufficienti, la latenza restituita sarà Null. 

## <a name="visual-overview"></a>Panoramica della visualizzazione

Quando si passa alla sezione **Visualizzazione traffico** nella pagina di Traffic Manager, viene visualizzata una mappa geografica con una sovrapposizione di visualizzazione traffico Insights. La mappa fornisce informazioni sulla base di utenti e gli endpoint per il profilo di Gestione traffico.

![Gestione traffico Visualizzazione traffico vista geografica][1]

### <a name="user-base-information"></a>Informazioni sulla base di utenti

Per i resolver DNS locali per i quali sono disponibili informazioni sulla posizione, vengono visualizzati nella mappa. Il colore del resolver DNS indica la latenza media riscontrata dagli utenti finali che hanno usato il resolver DNS per le query di Gestione traffico.

Se si passa il mouse su una posizione del resolver DNS nella mappa, verranno visualizzati i dati seguenti:
- l'indirizzo IP del resolver DNS
- il volume di traffico delle query DNS rilevato da Gestione traffico dall'indirizzo
- gli endpoint a cui è stato indirizzato il traffico dal resolver DNS, come linea tra l'endpoint e il resolver DNS 
- la latenza media da tale posizione all'endpoint, rappresentata come colore della linea di connessione

### <a name="endpoint-information"></a>Informazioni sugli endpoint

Le aree di Azure in cui si trovano gli endpoint vengono visualizzate come punti blu nella mappa. Se l'endpoint è esterno e non è stato eseguito il mapping di un'area di Azure, questi vengono visualizzati nella parte superiore della mappa. Selezionare qualsiasi endpoint per visualizzare le diverse posizioni (in base al resolver DNS usato) da cui il traffico è stato indirizzato a tale endpoint. Le connessioni vengono visualizzate come una linea tra l'endpoint e il percorso del resolver DNS. Sono colorate in base alla latenza rappresentativa tra la coppia. È possibile visualizzare il nome dell'endpoint e l'area di Azure in cui viene eseguito. Viene visualizzato anche il volume totale di richieste che vengono indirizzate da questo profilo di gestione traffico.


## <a name="tabular-listing-and-raw-data-download"></a>Elenco tabulare e download di dati non elaborati

È possibile visualizzare i dati di Visualizzazione traffico in formato tabulare nel portale di Azure. Esiste una voce per ogni coppia di IP/endpoint del resolver DNS che mostra:

* Indirizzo IP del resolver DNS.
* Nome.
* Posizione geografica dell'area di Azure in cui si trova l'endpoint, se disponibile.
* Volume delle richieste associate al resolver DNS a tale endpoint.
* Latenza rappresentativa associata agli utenti finali che usano il DNS (se disponibile). 

È possibile anche scaricare i dati di Visualizzazione traffico come file CSV che possono essere usati come parte di un flusso di lavoro di analisi scelto.

## <a name="billing"></a>Fatturazione

Quando si usa Visualizzazione traffico, la fatturazione viene addebitata in base al numero di punti dati usati per creare le informazioni fornite. L'unico tipo di punto dati attualmente usato corrisponde alle query ricevute dal profilo di Gestione traffico. Per altri dettagli sui prezzi, visitare la [pagina dei prezzi di Gestione Traffico](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faqs"></a>Domande frequenti

* [A cosa serve Visualizzazione traffico?](./traffic-manager-faqs.md#what-does-traffic-view-do)

* [Quali sono i vantaggi dell'utilizzo di Visualizzazione traffico?](./traffic-manager-faqs.md#how-can-i-benefit-from-using-traffic-view)

* [In che modo Visualizzazione traffico differisce dalle metriche di Gestione traffico disponibili tramite Monitoraggio di Azure?](./traffic-manager-faqs.md#how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor)

* [Visualizzazione traffico usa le informazioni della subnet client EDNS?](./traffic-manager-faqs.md#does-traffic-view-use-edns-client-subnet-information)

* [Quanti giorni di dati usa Visualizzazione traffico?](./traffic-manager-faqs.md#how-many-days-of-data-does-traffic-view-use)

* [In che modo Visualizzazione traffico gestisce gli endpoint esterni?](./traffic-manager-faqs.md#how-does-traffic-view-handle-external-endpoints)

* [È necessario abilitare Visualizzazione traffico per ogni profilo della sottoscrizione?](./traffic-manager-faqs.md#do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription)

* [Come si disabilita Visualizzazione traffico?](./traffic-manager-faqs.md#how-can-i-turn-off-traffic-view)

* [Come funziona la fatturazione di Visualizzazione traffico?](./traffic-manager-faqs.md#how-does-traffic-view-billing-work)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [sul funzionamento di gestione traffico](traffic-manager-overview.md)
- Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico
- Informazioni su come [creare un profilo di Gestione traffico](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-traffic-view-overview/trafficview.png