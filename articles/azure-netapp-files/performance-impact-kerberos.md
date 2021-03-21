---
title: Conseguenze sulle prestazioni di Kerberos nei volumi Azure NetApp Files NFSv 4.1 | Microsoft Docs
description: Descrive le opzioni di sicurezza disponibili, i vettori di prestazioni testati e l'effetto sulle prestazioni previsto di Kerberos nei volumi Azure NetApp Files NFSv 4.1.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: e9054f11c8f55e9fe00266840a9f6e257f14e659
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101746091"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>Conseguenze sulle prestazioni di Kerberos nei volumi Azure NetApp Files NFSv 4.1

Azure NetApp Files supporta la [crittografia client NFS in modalità Kerberos](configure-kerberos-encryption.md) (krb5, Krb5i e krb5p) con crittografia AES-256. Questo articolo descrive l'effetto sulle prestazioni di Kerberos nei volumi NFSv 4.1. 

## <a name="available-security-options"></a>Opzioni di sicurezza disponibili 

Le opzioni di sicurezza attualmente disponibili per i volumi NFSv 4.1 sono le seguenti: 

* **sec = sys** USA UID UNIX locali e gid usando AUTH_SYS per autenticare le operazioni NFS.
* **sec = krb5** USA Kerberos V5 anziché gli UID UNIX locali e GID per autenticare gli utenti.
* **sec = krb5i** USA Kerberos V5 per l'autenticazione utente ed esegue il controllo dell'integrità delle operazioni NFS usando i checksum protetti per impedire la manomissione dei dati.
* **sec = krb5p** USA Kerberos V5 per l'autenticazione degli utenti e il controllo dell'integrità. Crittografa il traffico NFS per impedire lo sniffing del traffico. Questa opzione è l'impostazione più sicura, ma comporta anche il sovraccarico delle prestazioni.

## <a name="performance-vectors-tested"></a>Vettori di prestazioni testati

In questa sezione viene descritto il singolo effetto sulle prestazioni lato client delle varie `sec=*` Opzioni.

* L'effetto sulle prestazioni è stato testato a due livelli: concorrenza bassa (carico basso) e concorrenza elevata (limiti massimi di I/O e velocità effettiva).  
* Sono stati testati tre tipi di carichi di lavoro:  
    * Operazioni di lettura/scrittura casuali di piccole dimensioni (usando FIO)
    * Operazioni di lettura/scrittura sequenziali di grandi dimensioni (con FIO)
    * Carichi di lavoro pesanti dei metadati generati da applicazioni come Git

## <a name="expected-performance-impact"></a>Conseguenze sulle prestazioni previste 

Ci sono due aree di interesse: caricamento leggero e limite superiore. Gli elenchi seguenti descrivono l'impostazione di sicurezza relativa all'effetto sulle prestazioni in base alle impostazioni di sicurezza e allo scenario. Tutti i confronti vengono eseguiti in base al `sec=sys` parametro di sicurezza. Il test è stato eseguito su un singolo volume, usando un singolo client. 

Conseguenze sulle prestazioni di krb5:

* Concorrenza bassa (r/w):
    * Latenza sequenziale aumentata 0,3 ms.
    * La latenza di I/O casuale è aumentata 0,2 ms.
    * Latenza di I/O dei metadati aumentata 0,2 ms.
* Concorrenza elevata (r/w): 
    * La velocità effettiva massima sequenziale non è stata influenzata da krb5.
    * Il numero massimo di I/O casuali diminuisce del 30% per i carichi di lavoro di lettura puri con l'effetto complessivo che si sposta su zero quando il carico di lavoro passa alla scrittura pura. 
    * Numero massimo di carichi di lavoro dei metadati diminuito del 30%.

Conseguenze sulle prestazioni di Krb5i: 

* Concorrenza bassa (r/w):
    * Latenza sequenziale aumentata 0,5 ms.
    * La latenza di I/O casuale è aumentata 0,2 ms.
    * Latenza di I/O dei metadati aumentata 0,2 ms.
* Concorrenza elevata (r/w): 
    * Il numero massimo di velocità effettiva sequenziale è diminuito del 70% a prescindere dalla combinazione del carico di lavoro.
    * Il numero massimo di I/O casuali diminuisce del 50% per i carichi di lavoro di lettura puri con l'effetto complessivo che diminuisce fino al 25% mentre il carico di lavoro passa alla scrittura pura. 
    * Numero massimo di carichi di lavoro dei metadati diminuito del 30%.

Conseguenze sulle prestazioni di krb5p:

* Concorrenza bassa (r/w):
    * Latenza sequenziale aumentata 0,8 ms.
    * La latenza di I/O casuale è aumentata 0,2 ms.
    * Latenza di I/O dei metadati aumentata 0,2 ms.
* Concorrenza elevata (r/w): 
    * Il numero massimo di velocità effettiva sequenziale è diminuito del 85% a prescindere dalla combinazione del carico di lavoro. 
    * Il numero massimo di I/O casuali diminuisce del 65% per i carichi di lavoro di lettura puri con l'effetto complessivo che diminuisce fino al 43% mentre il carico di lavoro passa alla scrittura pura. 
    * Numero massimo di carichi di lavoro dei metadati diminuito del 30%.

## <a name="next-steps"></a>Passaggi successivi  

* [Configurare la crittografia Kerberos NFS v4.1 per Azure NetApp Files](configure-kerberos-encryption.md) 
