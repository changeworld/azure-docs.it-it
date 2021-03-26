---
title: Supporto - Modulo HSM dedicato di Azure | Microsoft Docs
description: Opzioni di supporto e aree di responsabilità per il modulo HSM dedicato di Azure in diversi scenari
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: cd949bdb7c489478df6a16d6dccd0bf358637604
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606930"
---
# <a name="azure-dedicated-hsm-supportability"></a>Supporto di HSM dedicato di Azure

Il servizio HSM dedicato di Azure offre un dispositivo fisico ad uso esclusivo del cliente, con controllo amministrativo completo e piena responsabilità di gestione. Il dispositivo reso disponibile è un [modello di modulo di protezione hardware Thales Luna 7 A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms). Dopo il provisioning al cliente, Microsoft non avrà alcun accesso amministrativo, oltre a una connessione alla porta seriale come ruolo di monitoraggio.  In mancanza di accesso, Microsoft non può avere alcuna responsabilità a livello di manutenzione del software o amministrazione di sistema. Di conseguenza, i clienti sono responsabili delle attività operative più comuni.
I clienti sono completamente responsabili delle applicazioni che usano HSM e dovrebbero collaborare con Thales per assistenza o assistenza basata sulla consulenza. Vista la portata della proprietà del cliente in termini di igiene operativa, Microsoft non può offrire alcun tipo di garanzia di disponibilità elevata per questo servizio. È responsabilità del cliente assicurarsi che le proprie applicazioni siano configurate correttamente per ottenere la disponibilità elevata. Microsoft monitorerà e gestirà l'integrità del dispositivo e la connettività di rete.

## <a name="getting-support"></a>Richiesta di assistenza

Il supporto tecnico per HSM dedicato è uno sforzo comune tra Microsoft e Thales. Eventuali problemi hardware o problemi relativi al percorso di rete verranno risolti da Microsoft e qualsiasi cosa da fare con il modulo di protezione hardware effettivo, ad esempio configurazione, software, firmware e sviluppo di applicazioni, verrà risolto da Thales. Questo modello di supporto garantisce il routing più rapido al supporto più efficace. In caso di dubbi su un particolare problema, generare una richiesta di supporto con Microsoft per assicurarsi che l'utente venga indirizzato in modo appropriato. Microsoft rimane impegnato in tutti gli scenari di supporto e si impegna a garantire la migliore esperienza di supporto per i clienti.

## <a name="thales-support"></a>Supporto di Thales

I clienti che usano il servizio HSM dedicato sono idonei per il supporto da Thales in base al piano di supporto più. Questa operazione richiede solo un processo di registrazione tramite il portale di supporto di Thales. Un ID cliente e le istruzioni verranno fornite come parte del coinvolgimento iniziale con Microsoft per ottenere l'accesso al servizio HSM dedicato. Il meccanismo per ottenere supporto da Thales è tramite il [portale di supporto clienti](https://supportportal.thalesgroup.com/csm).
Un punto importante è che Thales fornirà tutto il software e la documentazione necessari per usare il modulo di protezione hardware (ad esempio, il software di accesso client e gli SDK) tramite download nel portale per il supporto clienti.

### <a name="software-components"></a>Componenti software

Nella configurazione dei dispositivi HSM vengono usati diversi componenti software:

* Software client
* SDK
* Strumenti

### <a name="guidance"></a>Indicazioni

Thales rende disponibili le linee guida per l'amministrazione e la configurazione tramite il [portale di supporto clienti di Thales](https://supportportal.thalesgroup.com/csm). Una volta effettuato l'accesso usando un ID cliente valido, questi documenti sono disponibili per il download. Thales fornisce inoltre una serie di guide all'integrazione per aiutare i clienti con diversi scenari e integrazioni software. Per ulteriori informazioni, vedere il [sito partner Thales per Microsoft](https://cpl.thalesgroup.com/partners/overview).

### <a name="support"></a>Supporto

Eventuali problemi o domande a livello di software in relazione all'uso di HSM come parte del servizio HSM dedicato devono essere risolti direttamente nel supporto di Thales. Tutti i componenti software elencati sopra e qualsiasi configurazione del modulo di protezione hardware personalizzata con post-provisioning verranno risolti da Thales. Per ulteriori informazioni, vedere il [portale di supporto clienti di Thales](https://supportportal.thalesgroup.com/csm).

### <a name="consulting-services"></a>Servizi di consulenza

Per assistenza nella progettazione, nello sviluppo e nella distribuzione di applicazioni personalizzate che usano il modulo di protezione hardware, contattare il rappresentante dell'account Thales.

## <a name="microsoft-support"></a>Supporto tecnico Microsoft

Microsoft assicurerà che i dispositivi HSM fisici siano accessibili alla rete e in uno stato operativo per l'uso esclusivo di un singolo cliente. I clienti sono responsabili della configurazione, dell'amministrazione e della gestione del dispositivo. Le responsabilità di Microsoft includono:

* Assicurarsi che il dispositivo disponga di alimentazione e raffreddamento
* Mantenere lo stato operativo del modulo di protezione hardware (ad esempio, scenari di risoluzione dei problemi)
* Il dispositivo sia accessibile in rete.

I problemi seguenti devono essere segnalati a Microsoft:

* Guasti dei componenti
* Guasto completo del dispositivo
* Problemi di accesso alla rete
* Problemi di provisioning e deprovisioning.

Microsoft dispone di accesso alla porta seriale fisico al dispositivo tramite un ruolo di monitoraggio, ovvero un ruolo non amministrativo, che consente la telemetria dell'integrità di base.  In questo modo Microsoft può fornire la notifica tempestiva di problemi al cliente, a meno che il cliente non scelga di limitare il privilegio. 

### <a name="provisioning-and-decommissioning"></a>Provisioning e rimozione delle autorizzazioni

Dopo aver ottenuto una registrazione approvata per il servizio HSM dedicato, il cliente sarà in grado di creare risorse HSM, attualmente tramite PowerShell o l'interfaccia della riga di comando e non dal portale di Azure. La risorsa attraversa un processo di allocazione in cui viene eseguito il mapping di un dispositivo fisico in un'area specificata a una rete virtuale del cliente predefinita. Una volta visibile sulla rete virtuale, il cliente può accedere al dispositivo e configurarlo ulteriormente in base ai requisiti. I clienti accedono alla HSM dedicata usando il software client e gli strumenti di Thales. Il processo di creazione delle risorse è supportato da Microsoft. Il processo di configurazione personalizzato e oltre sono supportati da Thales. (vedere il supporto di Thales precedente). Quando un cliente smette di utilizzare un modulo di protezione hardware, deve reimpostarlo (impostarlo su zero) per garantire la non persistenza dei dati. Il processo di reimpostazione del dispositivo rimuove tutti i dati e la configurazione personalizzata. Microsoft dealloca il dispositivo e lo restituisce al pool in uno stato originario. Ciò significa che quando il dispositivo viene restituito al pool non vi è alcuna traccia dell'attività del cliente precedente. 

### <a name="hardware-issues"></a>Problemi hardware

Il dispositivo HSM ha alimentatori e ventole ridondanti e sostituibili.  Tuttavia, la rimozione delle unità fan provocherà comunque un evento di manomissione. Se si verifica il guasto di un componente, Microsoft seguirà il processo più appropriato per risolvere il problema a livello di componente in modo da causare un'interruzione minima e il minimo rischio per la disponibilità del servizio ai clienti.
Qualsiasi errore più grave del dispositivo comporterà la sostituzione del dispositivo da parte di un nuovo dispositivo dal pool gratuito. Il cliente include semplicemente il nuovo dispositivo nella coppia a disponibilità elevata esistente in modo da sincronizzarlo e tornare allo stato operativo completo. I dispositivi contenenti dati del modulo di protezione hardware verranno rimossi e distrutti all'interno del data center. 

### <a name="networking-issues"></a>Problemi di rete

In caso di problemi di accesso di rete al dispositivo HSM, i clienti devono contattare il supporto tecnico Microsoft. Un semplice test per l'accesso di rete consiste nell'usare SSH per connettersi al dispositivo HSM. In caso di errore, contattare il supporto tecnico Microsoft.

## <a name="service-level-expectations-for-support"></a>Aspettative del livello di servizio per il supporto

Per i livelli di servizio del supporto tecnico Microsoft, vedere il [piano di supporto per Azure](https://azure.microsoft.com/support/plans/).
Per i livelli di servizio di supporto Thales, vedere l'articolo relativo al [supporto di Thales](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Passaggi successivi

Prima di eseguire il provisioning di dispositivi o di iniziare la progettazione o distribuzione di applicazioni, è consigliabile aver ben compreso tutti i concetti chiave.

* [Architettura di distribuzione](deployment-architecture.md)
* [Disponibilità elevata](high-availability.md)
* [Sicurezza fisica](physical-security.md)
* [Rete](networking.md)

