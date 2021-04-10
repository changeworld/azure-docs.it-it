---
title: Vantaggi della migrazione all'API di servizi multimediali V3
description: Questo articolo elenca i vantaggi della migrazione da Media Services V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 9dd3525f4efec3c49950839306ee5419c7850c69
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275414"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>Passaggio 1: informazioni sui vantaggi della migrazione all'API di servizi multimediali V3

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-1.svg)

## <a name="use-the-latest-api"></a>Usare l'API più recente

Si consiglia di iniziare a usare la versione 2020-05-01 dell'API servizi multimediali di Azure V3 ora per ottenere i vantaggi perché le nuove funzionalità, le funzionalità e le ottimizzazioni delle prestazioni sono disponibili solo nell'API V3 corrente.

È possibile modificare la versione dell'API nel portale, gli SDK più recenti, l'interfaccia della riga di comando più recente e l'API REST con la stringa di versione corretta.

Sono stati apportati miglioramenti significativi a servizi multimediali con v3.  

## <a name="benefits-of-media-services-v3"></a>Vantaggi di Servizi multimediali v3

| **Funzionalità V3** | **Vantaggi** |
| --- | --- |
| **Azure portal** | |
| Aggiornamenti portale di Azure | Il portale di Azure è stato aggiornato in modo da includere la gestione delle entità API V3. Consente ai clienti di usare il portale per avviare lo streaming live, inviare processi di trasformazione V3, gestire i criteri di protezione del contenuto, gli endpoint di streaming, ottenere l'accesso alle API, gestire gli account di archiviazione collegati ed eseguire attività di monitoraggio. |
| **Account e archiviazione** | |
| Controllo degli accessi in base al ruolo di Azure (RBAC) | I clienti possono ora definire i propri ruoli e controllare l'accesso a ogni entità nell'API ARM di servizi multimediali. Questo consente di controllare l'accesso alle risorse tramite gli account AAD. |
| Identità gestite | Le identità gestite eliminano la necessità per gli sviluppatori di gestire le credenziali fornendo un'identità per la risorsa di Azure in Azure AD. Vedere i dettagli sulle identità gestite [qui](../../active-directory/managed-identities-azure-resources/overview.md). |
| Supporto del collegamento privato | I clienti potranno accedere agli endpoint di servizi multimediali per il recapito delle chiavi, LiveEvents e le entità streamingendpoint tramite un PrivateEndpoint nei VNet. |
| [Chiavi](concept-use-customer-managed-keys-byok.md) gestite dal cliente o supporto BYOK (Bring your own key) | I clienti possono crittografare i dati nel proprio account di servizi multimediali usando una chiave nella Azure Key Vault. |
| **Asset** | |
| Un asset può avere più [localizzatori di streaming](stream-streaming-locators-concept.md) ognuno con diverse impostazioni di creazione [dinamica dei pacchetti](encode-dynamic-packaging-concept.md) e di crittografia dinamica. | Per ogni asset è consentito un limite di 100 localizzatori di streaming. I clienti possono archiviare un'unica copia del contenuto multimediale nell'asset, ma condividono diversi URL di streaming con diversi criteri di streaming o criteri di protezione del contenuto basati su un pubblico di destinazione.
| **Elaborazione dei processi** ||
| V3 introduce il concetto di [trasformazioni](transform-jobs-concept.md)   per l'elaborazione di processi basati su file. | Una trasformazione consente di creare configurazioni riutilizzabili, creare modelli di Azure Resource Manager e isolare le impostazioni di elaborazione tra più clienti o tenant. |
| Per l'elaborazione di processi basati su file, è possibile usare un URL HTTP (S) come input. | Non è necessario che il contenuto sia già archiviato in Azure, né che sia necessario creare asset di input. |
| **Eventi Live** ||
| Eventi Live Premium 1080p | Il nuovo SKU di eventi Live consente ai clienti di ottenere la codifica cloud con output fino a 1080p per la risoluzione. |
| Nuovo supporto per lo streaming live a [bassa latenza](live-event-latency-reference.md) per gli eventi live. | Ciò consente agli utenti di controllare gli eventi live più vicini al tempo reale rispetto a quando questa impostazione non è abilitata. |
| L'anteprima eventi Live supporta la creazione [dinamica dei pacchetti](encode-dynamic-packaging-concept.md)   e la crittografia dinamica. | Questo consente la protezione del contenuto nei pacchetti anteprima e DASH e HLS. |
| Programmi di sostituzione degli output Live | L'output in tempo reale è più semplice da usare rispetto all'entità Program nelle API v2. |
| L'inserimento RTMP per gli eventi Live è migliorato, con supporto per più codificatori | Aumenta la stabilità e fornisce la flessibilità del codificatore di origine. |
| Gli eventi Live possono trasmettere in streaming 24x7 | È possibile ospitare un evento Live e far coinvolgere i destinatari per periodi più lunghi. |
| Trascrizione in tempo reale sugli eventi Live | La trascrizione Live consente ai clienti di trascrivere automaticamente il linguaggio parlato in testo in tempo reale durante la trasmissione di eventi live. Questo migliora significativamente l'accessibilità degli eventi live. |
| [Modalità standby](live-event-outputs-concept.md#standby-mode) in eventi Live | Gli eventi Live in stato standby sono meno costosi rispetto all'esecuzione di eventi live. Ciò consente ai clienti di mantenere un set di eventi live pronti per iniziare entro pochi secondi a un costo inferiore rispetto alla gestione di un set di eventi Live in esecuzione. I prezzi ridotti per gli eventi live standby diventeranno effettivi nel febbraio 2021 per la maggior parte delle aree, con il resto da seguire nel 2021 aprile.
|**Protezione del contenuto** ||
| [Protezione](drm-content-key-policy-concept.md)   del contenuto supporta funzionalità multichiave. | I clienti possono ora usare più chiavi di crittografia del contenuto nei localizzatori di streaming. |
| **Monitoring** | |
| Supporto per le notifiche di [Azure EventGrid](monitoring/reacting-to-media-services-events.md) | Le notifiche EventGrid sono più ricche di funzionalità. Sono disponibili più tipi di notifiche, il supporto più ampio di SDK per la ricezione delle notifiche nella propria applicazione e più servizi di Azure esistenti che possono fungere da gestori di eventi. |
| [Supporto e integrazione di monitoraggio di Azure nella portale di Azure](monitoring/monitor-events-portal-how-to.md) | Ciò consente ai clienti di visualizzare l'utilizzo delle quote di account di servizi multimediali, le statistiche in tempo reale degli endpoint di streaming e le statistiche di inserimento e archiviazione per gli eventi live. I clienti possono ora impostare gli avvisi ed eseguire le azioni necessarie in base ai dati delle metriche in tempo reale. |
