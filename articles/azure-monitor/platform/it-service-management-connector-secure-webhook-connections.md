---
title: IT Service Management Connector-esportazione sicura in monitoraggio di Azure
description: Questo articolo illustra come connettere i prodotti/servizi ITSM con l'esportazione sicura in monitoraggio di Azure per monitorare e gestire centralmente gli elementi di lavoro ITSM.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: ee56d65452cb8535c5197e1b3524bd4e9c9ab9ea
ms.sourcegitcommit: 697638c20ceaf51ec4ebd8f929c719c1e630f06f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97857410"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>Connettere Azure agli strumenti ITSM usando l'esportazione sicura

Questo articolo illustra come configurare la connessione tra il prodotto o il servizio di gestione dei servizi IT (ITSM) usando l'esportazione sicura.

L'esportazione protetta è una versione aggiornata di [IT Service Management Connector (connettore)](./itsmc-overview.md). Entrambe le versioni consentono di creare elementi di lavoro in uno strumento ITSM quando monitoraggio di Azure invia avvisi. La funzionalità include gli avvisi relativi a metriche, log e log attività.

CONNETTORE usa le credenziali di nome utente e password. L'esportazione protetta ha un'autenticazione più avanzata perché usa Azure Active Directory (Azure AD). Azure AD è il servizio Microsoft basato sul cloud per la gestione delle identità e dell'accesso. Consente agli utenti di accedere alle risorse interne o esterne. L'uso di Azure AD con ITSM consente di identificare gli avvisi di Azure (tramite l'ID dell'applicazione Azure AD) inviati al sistema esterno.

> [!NOTE]
> La possibilità di connettere Azure agli strumenti ITSM tramite l'esportazione protetta è in anteprima.

## <a name="secure-export-architecture"></a>Architettura di esportazione sicura

L'architettura di esportazione protetta introduce le nuove funzionalità seguenti:

* **Nuovo gruppo di azioni**: gli avvisi vengono inviati allo strumento ITSM tramite il gruppo di azioni di Webhook sicuro, anziché il gruppo di azioni ITSM usato da connettore.
* **Autenticazione Azure ad**: viene eseguita l'autenticazione tramite Azure ad anziché le credenziali nome utente/password.

## <a name="secure-export-data-flow"></a>Flusso di dati di esportazione protetta

I passaggi del flusso di dati di esportazione protetta sono:

1. Monitoraggio di Azure invia un avviso configurato per l'uso dell'esportazione protetta.
2. Il payload dell'avviso viene inviato da un'azione webhook sicura allo strumento ITSM.
3. L'applicazione ITSM verifica con Azure AD se l'avviso è autorizzato ad accedere allo strumento ITSM.
4. Se l'avviso è autorizzato, l'applicazione:
   
   1. Consente di creare un elemento di lavoro, ad esempio un evento imprevisto, nello strumento ITSM.
   2. Associa l'ID dell'elemento di configurazione (CI) al database di gestione clienti (CMDB).

![Diagramma che illustra come lo strumento ITSM comunica con Azure A D, avvisi di Azure e un gruppo di azione.](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>Vantaggi dell'esportazione sicura

I principali vantaggi dell'integrazione sono:

* **Autenticazione migliore**: Azure ad offre un'autenticazione più sicura senza i timeout che in genere si verificano in connettore.
* **Avvisi risolti nello strumento ITSM**: gli avvisi delle metriche implementano gli Stati "attivato" e "risolto". Quando la condizione viene soddisfatta, lo stato dell'avviso è "attivato". Quando la condizione non viene più soddisfatta, lo stato dell'avviso è "risolto". In connettore gli avvisi non possono essere risolti automaticamente. Con l'esportazione protetta, lo stato risolto passa allo strumento ITSM e pertanto viene aggiornato automaticamente.
* **[Schema di avviso comune](./alerts-common-schema.md)**: in connettore lo schema del payload dell'avviso è diverso in base al tipo di avviso. Nell'esportazione protetta esiste uno schema comune per tutti i tipi di avviso. Questo schema comune contiene la CI per tutti i tipi di avviso. Tutti i tipi di avviso saranno in grado di associare l'integrazione continua con CMDB.

## <a name="next-steps"></a>Passaggi successivi

* [Creare elementi di lavoro di Connettore di Gestione dei servizi IT da avvisi di Azure](./itsmc-overview.md)
