---
title: Disponibilità a livello di area e residenza dei dati per i Servizi di comunicazione di Azure
description: Informazioni sulla residenza dei dati e sulle problematiche correlate alla privacy nei Servizi di comunicazione di Azure
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 933b5605cf38be90d419673a94e23e4c36f0ef36
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495709"
---
# <a name="region-availability-and-data-residency"></a>Disponibilità a livello di area e residenza dei dati

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

I Servizi di comunicazione di Azure consentono di aiutare i clienti a soddisfare i requisiti in materia di privacy e dati personali. Gli sviluppatori che usano Servizi di comunicazione con una relazione diretta con gli utenti che usano l'applicazione sono potenzialmente il titolare del trattamento dei dati. Poiché i servizi di comunicazione di Azure archiviano e crittografano i dati inattivi per conto dell'utente, è molto probabile che si tratta di un processore di questi dati. In questa pagina viene riepilogato il modo in cui il servizio conserva i dati e come è possibile identificare, esportare ed eliminare i dati.

## <a name="data-residency"></a>Residenza dei dati

Quando si crea una risorsa Servizi di comunicazione, è necessario specificare un'**area geografica**, non un data center di Azure. Tutti i dati inattivi archiviati da Servizi di comunicazione verranno conservati in tale area geografica, in un data center selezionato internamente da Servizi di comunicazione. I dati possono transitare o essere elaborati in altre aree geografiche. Questi endpoint globali sono necessari per offrire agli utenti finali un'esperienza a prestazioni elevate e a bassa latenza, indipendentemente dalla loro posizione.

## <a name="data-residency-and-events"></a>Eventi e residenza dei dati

Tutti gli argomenti del sistema di griglia di eventi configurati con i servizi di comunicazione di Azure verranno creati in un percorso globale. Per supportare il recapito affidabile, un argomento di sistema di griglia di eventi globale può archiviare i dati degli eventi in qualsiasi data center Microsoft. Quando si configura griglia di eventi con servizi di comunicazione di Azure, si distribuiscono i dati degli eventi a griglia di eventi, ovvero una risorsa di Azure sotto il proprio controllo. Sebbene i servizi di comunicazione di Azure siano configurati per l'uso di griglia di eventi di Azure, si è responsabili della gestione della risorsa di griglia di eventi e dei dati archiviati al suo interno.

## <a name="relating-humans-to-azure-communication-services-identities"></a>Correlazione tra gli utenti e le identità di Servizi di comunicazione di Azure

L'applicazione gestisce la relazione tra gli utenti e le identità del servizio di comunicazione. Se si vogliono eliminare i dati per un utente, è necessario eliminare i dati relativi a tutte le identità del servizio di comunicazione correlate all'utente.

Esistono due categorie di dati del servizio di comunicazione:
- **Dati dell'API.** Questi dati vengono creati e gestiti dalle API del servizio di comunicazione, ad esempio i messaggi di chat gestiti tramite le API di chat.
- **Log di Monitoraggio di Azure**. Questi dati vengono creati dal servizio e gestiti tramite la piattaforma dati di Monitoraggio di Azure. Questi dati includono dati di telemetria e metriche che consentono di comprendere l'utilizzo di Servizi di comunicazione. Questi dati non sono gestiti dalle API del servizio di comunicazione.

## <a name="api-data"></a>Dati dell'API

### <a name="identities"></a>Identità

Servizi di comunicazione di Azure gestisce una directory di identità. Usare l'API [DeleteIdentity](/rest/api/communication/communicationidentity/delete) per rimuoverle. Se si elimina un'identità, tutti i token di accesso associati verranno revocati e verranno eliminati i messaggi di chat. Per altre informazioni su come rimuovere un'identità [vedere questa pagina](../quickstarts/access-tokens.md).

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

Usando il portale di Azure o le API di Azure Resource Manager con Servizi di comunicazione, è possibile creare dati personali. [Usare questa pagina per informazioni su come gestire i dati personali nei sistemi di Azure Resource Manager.](../../azure-resource-manager/management/resource-manager-personal-data.md)

### <a name="telephone-number-management"></a>Gestione dei numeri di telefono

Servizi di comunicazione di Azure gestisce una directory di numeri di telefono associati a una risorsa Servizi di comunicazione. Usare le [API di amministrazione del numero di telefono](/rest/api/communication/phonenumberadministration) per recuperare i numeri di telefono ed eliminarli:

- `Get All Phone Numbers`
- `Release Phone Number`

### <a name="chat"></a>Chat

I thread e i messaggi delle chat vengono conservati fino a quando non vengono eliminati in modo esplicito. Un thread completamente inattivo verrà eliminato automaticamente dopo 30 giorni. Usare le [API di chat](/rest/api/communication/chat/chatthread) per ottenere, elencare, aggiornare ed eliminare i messaggi.

- `Get Thread`
- `Get Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>sms

I messaggi SMS inviati e ricevuti sono temporaneamente elaborati dal servizio e non vengono conservati.

### <a name="pstn-voice-calling"></a>Chiamata vocale PSTN

La comunicazione audio e video è temporaneamente elaborata dal servizio e nella risorsa non vengono conservati dati diversi dai log di Monitoraggio di Azure.

### <a name="internet-voice-and-video-calling"></a>Chiamate vocali e videochiamate Internet

La comunicazione audio e video è temporaneamente elaborata dal servizio e nella risorsa non vengono conservati dati diversi dai log di Monitoraggio di Azure.

## <a name="azure-monitor-and-log-analytics"></a>Monitoraggio di Azure e Log Analytics

I Servizi di comunicazione di Azure trasmetteranno i dati di registrazione di Monitoraggio di Azure per acquisire informazioni sull'integrità operativa e sull'utilizzo del servizio. Alcuni di questi log includono le identità del servizio di comunicazione e i numeri di telefono come dati dei campi. Per eliminare eventuali dati potenzialmente personali [usare queste procedure per Monitoraggio di Azure](../../azure-monitor/logs/personal-data-mgmt.md). È anche possibile configurare un [periodo di conservazione predefinito per Monitoraggio di Azure](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- [Richieste dei soggetti interessati di Azure per GDPR e CCPA](/microsoft-365/compliance/gdpr-dsr-azure)
- [Centro protezione Microsoft](https://www.microsoft.com/trust-center/privacy/data-location)
- [Mappa interattiva di Azure - Dove risiedono i dati dei clienti?](https://azuredatacentermap.azurewebsites.net/)
