---
title: Strumenti di sviluppo del confidential computing di Azure
description: Usare strumenti e librerie per sviluppare applicazioni per il confidential computing
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 571c1a4ce545976db09f46a07d963d5344c02c29
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791014"
---
# <a name="application-development-on-intel-sgx"></a>Sviluppo di applicazioni in Intel SGX 


L'infrastruttura di confidential computing richiede strumenti e software specifici. Questa pagina illustra in modo specifico i concetti relativi allo sviluppo di applicazioni per macchine virtuali di confidential computing di Azure in esecuzione in Intel SGX. Prima di leggere questa pagina, [leggere l'introduzione di macchine virtuali Intel SGX ed enclave](confidential-computing-enclaves.md). 

Per sfruttare le potenzialità delle enclavi e degli ambienti isolati, è necessario usare strumenti che supportano il confidential computing. Sono disponibili diversi strumenti che supportano lo sviluppo di applicazioni enclave. È ad esempio possibile usare questi framework open source: 

- [The Open Enclave Software Development Kit (OE SDK)](#oe-sdk)
- [The EGo Software Development Kit](#ego)
- [CCF (Confidential Consortium Framework)](#ccf)

## <a name="overview"></a>Panoramica

Un'applicazione creata con enclavi viene partizionata in due modi:

1. Un componente "non attendibile" (l'host)
1. Un componente "attendibile" (l'enclave)


![Sviluppo di app](media/application-development/oe-sdk.png)


**L'host** è dove l'applicazione enclave è in esecuzione ed è un ambiente non attendibile. Il codice dell’enclave distribuito sull'host non è accessibile dall'host. 

**L'enclave** è il punto in cui viene eseguito il codice dell'applicazione e i relativi dati memorizzati nella cache/memoria. I calcoli protetti devono essere eseguiti nell'enclave per garantire la protezione continua di segreti e dati sensibili. 


Durante la progettazione dell'applicazione è importante identificare e determinare quale parte dell'applicazione deve essere eseguita nelle enclavi. Il codice che si sceglie di inserire nel componente attendibile viene isolato dal resto dell'applicazione. Una volta inizializzata l'enclave e caricato il codice in memoria, tale codice non può essere letto o cambiato dai componenti non attendibili. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave Software Development Kit (OE SDK) <a id="oe-sdk"></a>

Se si vuole scrivere codice da eseguire in un'enclave, usare una libreria o un framework supportato dal provider. [Open Enclave SDK](https://github.com/openenclave/openenclave) è un SDK open source che consente l'astrazione per diversi componenti hardware abilitati per il confidential computing. 

Open Enclave SDK è progettato come singolo livello di astrazione per qualsiasi componente hardware e per qualsiasi CSP. Open Enclave SDK può essere usato nelle macchine virtuali di confidential computing di Azure per creare ed eseguire applicazioni sulle enclavi.

## <a name="ego-software-development-kit"></a>EGo Software Development Kit <a id="ego"></a>

[EGo](https://ego.dev/) è un SDK open source che consente di eseguire applicazioni scritte nel linguaggio di programmazione Go all'interno degli enclave. EGo si basa su OE SDK e include una libreria Go in-enclave per l'attestazione e il sealing. Molte applicazioni Go esistenti vengono eseguite in EGo senza modifiche.  

## <a name="confidential-consortium-framework-ccf"></a>Confidential Consortium Framework (CCF) <a id="ccf"></a>

[Ccf](https://github.com/Microsoft/CCF) è una rete distribuita di nodi, ognuno dei quali esegue i propri enclave. La rete di nodi attendibili consente di eseguire un libro mastro distribuito. Il libro mastro fornisce componenti sicuri e affidabili da usare per il protocollo. 

![Nodi CCF](media/application-development/ccf.png)

Questo framework open source consente una riservatezza elevata, granularità fine e governance del consorzio per la blockchain. Con ogni nodo che usa teE, è possibile garantire il consenso sicuro e l'elaborazione delle transazioni.


## <a name="next-steps"></a>Passaggi successivi 
- [Distribuire una macchina virtuale DCsv2-Series di elaborazione riservata](quick-create-portal.md)
- [Scaricare e installare OE SDK e iniziare a sviluppare applicazioni](https://github.com/openenclave/openenclave)
