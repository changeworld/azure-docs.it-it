---
title: Modifiche importanti previste per il Centro sicurezza di Azure
description: Prossime modifiche al Centro sicurezza di Azure che può essere opportuno conoscere e per le quali potrebbe essere necessaria una pianificazione
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: d5de16c8156762a229d6c707080bc197dc206a7c
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475591"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Modifiche importanti che interesseranno il Centro sicurezza di Azure

> [!IMPORTANT]
> Le informazioni contenute in questa pagina si riferiscono a prodotti o funzionalità non definitivi che possono subire modifiche sostanziali prima dell'eventuale rilascio della versione commerciale. Microsoft non rilascia alcuna garanzia, espressa o implicita, in merito alle informazioni fornite in questa pagina.

Questa pagina descrive le modifiche pianificate per il Centro sicurezza. Illustra le modifiche al prodotto previste che potrebbero influire su elementi come il punteggio di sicurezza o i flussi di lavoro.

Se si cercano le note sulla versione più recenti, vedere [Novità del Centro sicurezza di Azure](release-notes.md).


## <a name="planned-changes"></a>Modifiche pianificate

- [Le raccomandazioni per la protezione del carico di lavoro Kubernetes verranno presto rilasciate per la disponibilità generale (GA)](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [Due consigli dal controllo di sicurezza "Applica aggiornamenti del sistema" obsoleti](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Miglioramenti alla raccomandazione di classificazione dei dati SQL](#enhancements-to-sql-data-classification-recommendation)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>Le raccomandazioni per la protezione del carico di lavoro Kubernetes verranno presto rilasciate per la disponibilità generale (GA)

**Data stimata per la modifica:** 2021 febbraio

I consigli sulla protezione del carico di lavoro Kubernetes descritti in [proteggere i carichi di lavoro di Kubernetes](kubernetes-workload-protections.md) sono attualmente in anteprima. Mentre una raccomandazione è in anteprima, non esegue il rendering di una risorsa non integro e non è inclusa nei calcoli del Punteggio sicuro.

Questi consigli verranno presto rilasciati per la disponibilità generale (GA), quindi *verranno* inclusi nel calcolo del punteggio. Se non sono già stati corretti, questo potrebbe causare un lieve effetto sul punteggio sicuro.

Correggerli laddove possibile (informazioni su come correggere le [raccomandazioni nel centro sicurezza di Azure](security-center-remediate-recommendations.md)).

Le raccomandazioni per la protezione del carico di lavoro Kubernetes sono:

- Il componente aggiuntivo di criteri di Azure per Kubernetes deve essere installato e abilitato nei cluster
- È consigliabile applicare limiti per la CPU e la memoria dei contenitori
- I contenitori con privilegi devono essere evitati
- Per i contenitori deve essere imposto il file system radice non modificabile (di sola lettura)
- È consigliabile evitare i contenitori con escalation dei privilegi
- È consigliabile evitare l'esecuzione di contenitori come utente radice
- I contenitori che condividono spazi dei nomi host sensibili devono essere evitati
- Per i contenitori devono essere imposte le funzionalità Linux con privilegi minimi
- L'utilizzo dei montaggi dei volumi HostPath dei pod deve essere limitato a un elenco noto
- I contenitori devono essere in ascolto solo sulle porte consentite
- I servizi devono essere in ascolto solo sulle porte consentite
- L'utilizzo della rete host e delle porte deve essere limitato
- La sovrascrittura o la disabilitazione del profilo AppArmor dei contenitori deve essere limitata
- Le immagini del contenitore devono essere distribuite solo da registri attendibili             

Altre informazioni su questi consigli sono disponibili in [proteggere i carichi di lavoro Kubernetes](kubernetes-workload-protections.md).

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Due consigli dal controllo di sicurezza "Applica aggiornamenti del sistema" obsoleti 

**Data stimata per la modifica:** 2021 febbraio

Le due raccomandazioni seguenti sono state pianificate per essere deprecate nel 2021 febbraio:

- **Per applicare gli aggiornamenti del sistema, è necessario riavviare i computer**. Questo potrebbe causare un lieve effetto sul punteggio sicuro.
- **È necessario installare l'agente di monitoraggio nei computer**. Questa raccomandazione si riferisce solo ai computer locali e la relativa logica verrà trasferita a un'altra raccomandazione, **log Analytics problemi di integrità dell'agente devono essere risolti nei computer**. Questo potrebbe causare un lieve effetto sul punteggio sicuro.

Si consiglia di controllare le configurazioni di esportazione continua e di automazione del flusso di lavoro per verificare se tali raccomandazioni sono incluse. Inoltre, tutti i dashboard o altri strumenti di monitoraggio che potrebbero utilizzarli devono essere aggiornati di conseguenza.

Per ulteriori informazioni su questi consigli, vedere la pagina di riferimento relativa alle [raccomandazioni sulla sicurezza](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Miglioramenti alla raccomandazione di classificazione dei dati SQL

**Data stimata per la modifica:** Q2 2021

La versione corrente dei **dati sensibili delle raccomandazioni nei database SQL deve essere classificata** in applica il controllo di sicurezza della **classificazione dei dati** verrà sostituita con una nuova versione più allineata alla strategia di classificazione dei dati di Microsoft. Di conseguenza:

- La raccomandazione non influirà più sul punteggio sicuro
- Il controllo di sicurezza ("applica classificazione dati") non influirà più sul punteggio sicuro
- Anche l'ID raccomandazione cambierà (attualmente b0df6f56-862d-4730-8597-38c0fd4ebd59)



## <a name="next-steps"></a>Passaggi successivi

Per tutte le modifiche recenti al prodotto, vedere [Novità del Centro sicurezza di Azure](release-notes.md).
