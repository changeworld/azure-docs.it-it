---
title: Che cos'è HSM dedicato? - Modulo di protezione hardware dedicato di Azure | Microsoft Docs
description: Informazioni sul servizio HSM dedicato di Azure, che fornisce l'archiviazione di chiavi crittografiche in Azure.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 0e07839c3c5ce542335eeadc92e6a3c98fe87856
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606981"
---
# <a name="what-is-azure-dedicated-hsm"></a>Informazioni su Modulo di protezione hardware dedicato di Azure

Modulo di protezione hardware dedicato di Azure è un servizio di Azure che offre l'archiviazione di chiavi crittografiche in Azure. Tale servizio soddisfa i requisiti di sicurezza più rigorosi. È la soluzione ideale per i clienti che richiedono dispositivi convalidati dalla certificazione FIPS 140-2 Livello 3 e il controllo completo ed esclusivo dell'appliance HSM. 

 I dispositivi HSM vengono distribuiti a livello globale in diverse aree di Azure. È possibile eseguirne facilmente il provisioning come una coppia di dispositivi e configurarli per la disponibilità elevata. È anche possibile effettuare il provisioning dei dispositivi HSM tra varie aree per garantire il failover a livello regionale. Microsoft fornisce il servizio HSM dedicato usando le appliance [A790 del modello di modulo di protezione hardware Thales Luna 7](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) . Questo dispositivo offre i livelli più elevati di prestazioni e opzioni di integrazione crittografica. 

Una volta eseguito il provisioning, i dispositivi HSM sono connessi direttamente alla rete virtuale del cliente. Sono anche accessibili dalle applicazioni e dagli strumenti di gestione in locale quando si configura la connettività VPN da sito a sito o da punto a sito. I clienti ottengono il software e la documentazione per configurare e gestire i dispositivi HSM dal [portale di supporto clienti di Thales](https://supportportal.thalesgroup.com/csm).

## <a name="why-use-azure-dedicated-hsm"></a>Perché usare HSM dedicato di Azure?

### <a name="fips-140-2-level-3-compliance"></a>Conformità a FIPS 140-2 Livello 3

Molte organizzazioni hanno rigorose normative di settore che stabiliscono che le chiavi crittografiche devono essere archiviate in [FIPS 140-2 livello 3](https://csrc.nist.gov/publications/detail/fips/140/2/final) convalidato HSM. Il modulo di protezione hardware dedicato di Azure e una nuova offerta a tenant singolo, [Azure Key Vault HSM gestito (anteprima)](https://docs.microsoft.com/azure/key-vault/managed-hsm), aiutano i clienti di diversi segmenti del settore, come il settore dei servizi finanziari, le agenzie governative e altri, soddisfano i requisiti FIPS 140-2 Level-3. Anche se il servizio di [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) multi-tenant di Microsoft usa attualmente HSM convalidati FIPS 140-2 livello 2. 

### <a name="single-tenant-devices"></a>Dispositivi a tenant singolo

Molti dei nostri clienti richiedono un tenant singolo per i dispositivi di archiviazione crittografica. Il servizio HSM dedicato di Azure permette di effettuare il provisioning di un dispositivo fisico da uno dei data center Microsoft distribuiti a livello globale. Dopo che è stato effettuato il provisioning del dispositivo, solo il cliente può accedervi.

### <a name="full-administrative-control"></a>Controllo amministrativo completo

Molti clienti richiedono il controllo amministrativo completo e l'accesso esclusivo ai dispositivi per scopi amministrativi. Dopo il provisioning di un dispositivo, solo il cliente può accedere al dispositivo a livello amministrativo o applicativo.

 Microsoft non ha alcun controllo amministrativo dopo che il cliente accede al dispositivo per la prima volta e modifica la password. Da quel momento in poi, il cliente è un vero tenant singolo con il controllo amministrativo completo e capacità di gestione dell'applicazione. Microsoft mantiene un accesso a livello di monitoraggio (non un ruolo di amministratore) per i dati di telemetria tramite la connessione alla porta seriale. Questo tipo di accesso viene usato per i monitoraggi dell'hardware, ad esempio temperatura, stato di alimentazione e integrità della ventola. 
 
 Il cliente può scegliere di disabilitare questo monitoraggio, se necessario. Tuttavia, se lo disabilita, non riceverà gli avvisi di integrità proattivi da Microsoft.

### <a name="high-performance"></a>Prestazioni elevate

Il dispositivo Thales è stato selezionato per questo servizio per diversi motivi. Offre il supporto per diversi algoritmi di crittografia, una vasta gamma di sistemi operativi e numerose API. Il modello specifico distribuito offre prestazioni eccellenti con 10.000 operazioni al secondo per RSA-2048. Supporta 10 partizioni che possono essere usate per le istanze di applicazione univoche. Si tratta di un dispositivo a bassa latenza, capacità elevata e velocità effettiva elevata.

### <a name="unique-cloud-based-offering"></a>Offerta unica basata sul cloud

Microsoft ha riconosciuto un'esigenza specifica per un set unico di clienti. È l'unico provider di servizi cloud a offrire ai nuovi clienti un servizio HSM dedicato convalidato con certificazione FIPS 140-2 Livello 3 e un'integrazione di applicazioni basate sul cloud e locali di tale portata.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>HSM dedicato di Azure è la scelta giusta?

HSM dedicato di Azure è un servizio specializzato che soddisfa requisiti unici per un tipo specifico di organizzazione su larga scala. Di conseguenza, è previsto che la maggior parte dei clienti di Azure non rientri nel profilo d'uso per questo servizio. Molti troveranno che il servizio Azure Key Vault sia più appropriato e conveniente. Per aiutare il cliente nella scelta della soluzione giusta per i propri requisiti, sono stati identificati i criteri seguenti.

### <a name="best-fit"></a>Soluzione ottimale

HSM dedicato di Azure è la soluzione più adatta a scenari di trasferimento in modalità "lift-and-shift", che richiedono l'accesso diretto ed esclusivo ai dispositivi HSM. Ecco alcuni esempi:

- La migrazione delle applicazioni dalle macchine locali alle macchine virtuali di Azure
- La migrazione delle applicazioni da Amazon AWS EC2 alle macchine virtuali che usano il servizio AWS Cloud HSM Classic (Amazon non offre questo servizio ai nuovi clienti)
- L'esecuzione di software standard, come Apache/Ngnix SSL Offload, Oracle TDE e ADCS, nelle macchine virtuali di Microsoft Azure 

### <a name="not-a-fit"></a>Soluzione non idonea

Azure HSM dedicati non rappresenta una soluzione adatta per il tipo di scenario seguente: I servizi cloud Microsoft che supportano la crittografia con chiavi gestite dal cliente (ad esempio Azure Information Protection, Crittografia dischi di Azure, Azure Data Lake Store, Archiviazione di Azure, Database SQL di Azure, Customer Key per Office 365) non sono integrati con HSM dedicato di Azure.

### <a name="it-depends"></a>Dipende

Determinare se HSM dedicato di Azure rappresenti o meno una soluzione idonea per una specifica situazione dipende da una combinazione potenzialmente complessa di requisiti e compromessi. Un esempio è il requisito FIPS 140-2 Livello 3. Questo requisito è comune e il modulo di protezione hardware dedicato di Azure e una nuova offerta a tenant singolo, [Azure Key Vault HSM gestito (anteprima)](https://docs.microsoft.com/azure/key-vault/managed-hsm) sono attualmente le uniche opzioni per la riunione. Se questi requisiti non sono rilevanti, spesso si tratta di una scelta tra Azure Key Vault e il modulo di protezione hardware dedicato di Azure. Valutare i requisiti prima di prendere una decisione.

Le situazioni in cui sarà necessario valutare le opzioni disponibili includono: 

- Nuovo codice in esecuzione nella macchina virtuale di Azure di un cliente
- TDE di SQL Server in una macchina virtuale di Azure
- Crittografia lato client di archiviazione di Azure
- Tecnologia Always Encrypted di SQL Server e database SQL di Azure

## <a name="next-steps"></a>Passaggi successivi

Questo è un servizio altamente specializzato. È pertanto consigliabile assicurarsi di aver compreso a fondo i concetti principali illustrati in questa documentazione, inclusi i prezzi, il supporto e i contratti di servizio. 

Le [guide all'integrazione di Thales](https://cpl.thalesgroup.com/partners/overview) consentono di semplificare il provisioning di HSM in un ambiente di rete virtuale esistente. Sono inoltre disponibili guide sulle procedure che consentono di determinare come configurare l'architettura di distribuzione.

* [Disponibilità elevata](high-availability.md)
* [Sicurezza fisica](physical-security.md)
* [Rete](networking.md)
* [Supporto](supportability.md)
* [Monitoring](monitoring.md)
