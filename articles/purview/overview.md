---
title: Introduzione ad Azure Purview (anteprima)
description: Questo articolo offre una panoramica di Azure Purview, incluse le relative funzionalità e i problemi per cui è stato progettato. Azure Purview consente a qualsiasi utente di registrare, individuare, comprendere e utilizzare le origini dati.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 11/30/2020
ms.openlocfilehash: 3fadfa9d4467209ddbe997afabba27a3752f685d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419933"
---
# <a name="what-is-azure-purview"></a>Che cos'è Azure Purview?

> [!IMPORTANT]
> Azure Purview è attualmente in anteprima. Le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) includono termini legali aggiuntivi che si applicano a funzionalità di Azure in versione beta, in anteprima o in altro modo non ancora disponibili a livello generale.

Azure Purview è un servizio unificato di governance dei dati che facilita la gestione e la governance dei dati in ambienti locali, multi-cloud e SaaS. Consente di creare facilmente una mappa olistica e aggiornata del panorama dei dati con funzionalità di individuazione dei dati automatica, classificazione dei dati sensibili e derivazione dei dati end-to-end. Aiuta i consumer di dati a trovare dati di valore e affidabili.

Azure Purview Data Map fornisce le basi per l'individuazione dei dati e per un'efficace governance dei dati. È un servizio PaaS nativo del cloud che acquisisce i metadati relativi ai dati aziendali presenti nei sistemi operativi e di analisi in locale e nel cloud. Purview Data Map viene mantenuto automaticamente aggiornato con il sistema automatizzato di analisi e classificazione integrato. Gli utenti aziendali possono configurare e usare Purview Data Map tramite un'interfaccia utente intuitiva, mentre gli sviluppatori possono interagire a livello di codice con Data Map usando le API open source di Apache Atlas 2.0.

Azure Purview Data Map integra Purview Data Catalog e Purview Data Insights come esperienze unificate all'interno di Purview Studio.
 
Con Purview Data Catalog, gli utenti aziendali e gli utenti tecnici possono facilmente trovare dati rilevanti usando un'esperienza di ricerca con filtri basati su termini di glossario, classificazioni, etichette di riservatezza e altro ancora. Per esperti in materia, amministratori dei dati e responsabili dei dati, Purview Data Catalog fornisce funzionalità di cura dei dati come la gestione del glossario aziendale e l'assegnazione automatica di tag agli asset di dati con termini del glossario. I consumer e i producer di dati possono inoltre tracciare visivamente la derivazione di asset di dati a partire dai sistemi operativi locali, attraverso lo spostamento, la trasformazione e l'arricchimento con vari sistemi di archiviazione ed elaborazione dei dati nel cloud fino all'utilizzo in un sistema di analisi come Power BI.

Con Purview Data Insights, i responsabili dei dati e della sicurezza possono ottenere un quadro generale e una comprensione immediata dei dati analizzati, della posizione dei dati sensibili e di come vengono spostati.

## <a name="discovery-challenges-for-data-consumers"></a>Difficoltà di individuazione per i consumer di dati

L'individuazione di origini dati aziendali è sempre stato un processo organico basato su conoscenze pubblicamente note. Per le aziende che vogliono sfruttare al meglio i propri asset di informazioni, questo approccio presenta numerose sfide:

* Poiché non esiste alcuna posizione centrale in cui registrare le origini dati, gli utenti potrebbero non essere consapevoli dell'esistenza di un'origine finché non entrano in contatto con essa nell'ambito di un altro processo.
* Se gli utenti non conoscono la posizione di un'origine dati, non possono connettersi ai dati tramite un'applicazione client. Per poter utilizzare i dati, gli utenti devono conoscere la stringa di connessione o il percorso.
* Se gli utenti non conoscono la posizione della documentazione di un'origine dati, non possono comprendere le modalità d'uso previste per i dati. Documentazione e origini dati possono trovarsi in varie posizioni ed essere utilizzate tramite diversi tipi di esperienze.
* Se gli utenti hanno domande su un asset di informazioni, devono trovare l'esperto o il team responsabile dei dati e interagire offline. Non esiste una connessione esplicita tra dati e persone esperte del loro uso.
* Se gli utenti non conoscono il processo per richiedere l'accesso all'origine dati, l'individuazione dell'origine dati e della relativa documentazione non consentirà comunque di accedere ai dati.

## <a name="discovery-challenges-for-data-producers"></a>Difficoltà di individuazione per i produttori di dati

Mentre chi utilizza i dati si trova ad affrontare le difficoltà descritte in precedenza, anche gli utenti responsabili della produzione e della gestione degli asset di informazioni affrontano difficoltà specifiche:

* L'annotazione delle origini dati con metadati descrittivi è spesso una fatica inutile. Le applicazioni client in genere ignorano le descrizioni che vengono archiviate nell'origine dati.
* La creazione della documentazione per le origini dati può essere un processo difficile e mantenere la documentazione sincronizzata con le origini dati è una responsabilità continua. Gli utenti potrebbero non considerare attendibile una documentazione percepita come obsoleta.
* La creazione e la manutenzione della documentazione per le origini dati sono operazioni lunghe e complesse. Lo stesso vale per il lavoro necessario per rendere la documentazione disponibile per tutti coloro che usano l'origine dati.
* Limitare l'accesso alle origini dati e fare in modo che chi le utilizza sappia come richiedere l'accesso è una sfida costante.

Queste sfide, in combinazione, rappresentano un ostacolo significativo per le aziende che vogliono incoraggiare e promuovere l'uso e la comprensione dei dati aziendali.

## <a name="discovery-challenges-for-security-administrators"></a>Difficoltà di individuazione per gli amministratori della sicurezza

Gli utenti responsabili della sicurezza dei dati dell'organizzazione possono trovarsi ad affrontare tutte le sfide elencate sopra in qualità di consumer e producer di dati, a cui si possono aggiungere le sfide seguenti:

* I dati di un'organizzazione crescono costantemente e vengono archiviati e condivisi in nuove posizioni. L'attività di individuazione, protezione e governance dei dati sensibili non ha mai fine. Occorre assicurarsi che i contenuti dell'organizzazione siano condivisi con le persone e le applicazioni giuste e con le autorizzazioni corrette.
* Per comprendere i livelli di rischio nei dati dell'organizzazione è necessario esaminare in profondità i contenuti, cercando parole chiave, criteri RegEx e/o tipi di dati sensibili. I tipi di dati sensibili possono includere numeri di carta di credito, codici fiscali o numeri di conto corrente, per citarne alcuni. È necessario monitorare costantemente tutte le origini dati per individuare contenuti sensibili, in quanto anche la più piccola perdita di dati può essere un problema serio per l'organizzazione.
* Verificare che l'organizzazione sia costantemente conforme ai criteri di sicurezza aziendali è un'attività impegnativa, dal momento che i contenuti aumentano e cambiano di continuo e che i requisiti e criteri di conformità vengono periodicamente aggiornati per stare al passo col cambiamento delle realtà digitali. Agli amministratori della sicurezza viene spesso affidato l'incarico di verificare la sicurezza dei dati nel più breve tempo possibile.

## <a name="azure-purview-advantages"></a>Vantaggi di Azure Purview

Azure Purview è progettato per risolvere i problemi descritti nelle sezioni precedenti e aiutare le aziende a sfruttare al meglio gli asset di informazioni esistenti. Il catalogo rende le origini dati facilmente individuabili e comprensibili per gli utenti che gestiscono i dati.

Azure Purview fornisce un servizio basato sul cloud in cui è possibile registrare le origini dati. Durante la registrazione, i dati rimangono nella posizione esistente, ma una copia dei relativi metadati viene aggiunta ad Azure Purview, insieme a un riferimento alla posizione dell'origine dati. I metadati vengono anche indicizzati per semplificare l'individuazione di ogni origine dati tramite una ricerca e per rendere l'origine dati comprensibile per gli utenti che la individuano.

Dopo la registrazione di un'origine dati, è possibile arricchirne i metadati. I metadati possono essere aggiunti dall'utente che ha registrato l'origine dati o da un altro utente dell'organizzazione. Qualsiasi utente può annotare un'origine dati, fornendo descrizioni, tag o altri metadati per richiedere l'accesso all'origine dati. Questi metadati descrittivi integrano i metadati strutturali, come i nomi delle colonne e i tipi di dati, registrati dall'origine dati.

Individuazione e informazioni sulle origini dati e il relativo utilizzo è lo scopo principale di registrazione delle origini. Gli utenti aziendali possono aver bisogno dei dati per scopi di business intelligence, sviluppo di applicazioni, data science o qualsiasi altra attività in cui sono necessari dati appropriati. Usano la funzione di individuazione di Data Catalog per trovare rapidamente i dati necessari, comprenderli per valutare la loro idoneità allo scopo e utilizzarli aprendo l'origine dati nello strumento che preferiscono.

Allo stesso tempo, gli utenti possono contribuire al catalogo documentando e annotando le origini dati già registrate, oltre che aggiungendo tag. Possono anche registrare nuove origini dati, che vengono quindi individuate, comprese e utilizzate dalla community di utenti del catalogo.

## <a name="in-region-data-residency"></a>Residenza dei dati nell'area geografica
Azure competenza non sposta o archivia i dati dei clienti dall'area in cui viene distribuita.

## <a name="next-steps"></a>Passaggi successivi

Per acquisire familiarità con Azure Purview, vedere [Creare un account Azure Purview](create-catalog-portal.md).
