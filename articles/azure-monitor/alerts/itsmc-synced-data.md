---
title: Dati sincronizzati dal prodotto ITSM all'area di lavoro LA
description: Questo articolo fornisce una panoramica dei dati sincronizzati dal prodotto ITSM all'area di lavoro di LA.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: fd570950190ceabac413aca2d68368e5e722a3da
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614397"
---
# <a name="data-synced-from-your-itsm-product"></a>Dati sincronizzati dal prodotto ITSM

Gli eventi imprevisti e le richieste di modifica vengono sincronizzati dallo strumento ITSM all'area di lavoro Log Analytics, in base alla configurazione della connessione (usando il campo "Sincronizza dati"):
* [ServiceNow](./itsmc-connections-servicenow.md)
* [System Center Service Manager](./itsmc-connections-scsm.md)
* [Cherwell](./itsmc-connections-cherwell.md)
* [Provance](./itsmc-connections-provance.md)

## <a name="synced-data"></a>Dati sincronizzati

In questa sezione vengono illustrati alcuni esempi di dati raccolti da connettore.

I campi in **ServiceDesk_CL** variano a seconda del tipo di elemento di lavoro importato nel log Analytics. Ecco un elenco di campi per due tipi di elemento di lavoro:

**Elemento di lavoro:** **Eventi imprevisti**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- ServiceDeskConnectionName
- ID Service Desk
- State
- Urgenza
- Impatto
- Priorità
- Riassegnazione
- Created By (Creato da)
- Resolved By (Risolto da)
- Closed By (Chiuso da)
- Source (Sorgente)
- Assegnato a
- Category
- Titolo
- Descrizione
- Data di creazione
- Data di chiusura
- Data di risoluzione
- Data ultima modifica
- Computer

**Elemento di lavoro:** **Richieste di modifica**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- ID Service Desk
- Created By (Creato da)
- Closed By (Chiuso da)
- Source (Sorgente)
- Assegnato a
- Titolo
- Type
- Category
- State
- Riassegnazione
- Conflict Status (Stato di conflitto)
- Urgenza
- Priorità
- Rischio
- Impatto
- Assegnato a
- Data di creazione
- Data di chiusura
- Data ultima modifica
- Data richiesta
- Planned Start Date (Data di inizio pianificata)
- Planned End Date (Data di fine pianificata)
- Work Start Date (Data di inizio lavoro)
- Work End Date (Data di fine pianificata)
- Descrizione
- Computer

## <a name="servicenow-example"></a>Esempio di ServiceNow 
### <a name="output-data-for-a-servicenow-incident"></a>Dati di output per un evento imprevisto ServiceNow

| Campo Log Analytics | Campo di ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | State |
| Urgency_s |Urgenza |
| Impact_s |Impatto|
| Priority_s | Priorità |
| CreatedBy_s | Aperto da |
| ResolvedBy_s | Risolto da|
| ClosedBy_s  | Chiuso da |
| Source_s| Tipo di contatto |
| AssignedTo_s | Assegnato a  |
| Category_s | Category |
| Title_s|  Breve descrizione |
| Description_s|  Note |
| CreatedDate_t|  Aperto |
| ClosedDate_t| closed|
| ResolvedDate_t|Risolto|
| Computer  | Elemento di configurazione |

### <a name="output-data-for-a-servicenow-change-request"></a>Dati di output per una richiesta di modifica ServiceNow

| Log Analytics | Campo di ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Richiesto da |
| ClosedBy_s | Chiuso da |
| AssignedTo_s | Assegnato a  |
| Title_s|  Breve descrizione |
| Type_s|  Type |
| Category_s|  Category |
| CRState_s|  State|
| Urgency_s|  Urgenza |
| Priority_s| Priorità|
| Risk_s| Rischio|
| Impact_s| Impatto|
| RequestedDate_t  | Data richiesta |
| ClosedDate_t | Data di chiusura |
| PlannedStartDate_t  | Data di inizio pianificata |
| PlannedEndDate_t  | Data di fine pianificata |
| WorkStartDate_t  | Data di inizio effettiva |
| WorkEndDate_t | Data di fine effettiva|
| Description_s | Descrizione |
| Computer  | Elemento di configurazione |

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi di Connettore di Gestione dei servizi IT](./itsmc-resync-servicenow.md)
