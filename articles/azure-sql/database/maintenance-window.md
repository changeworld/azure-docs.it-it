---
title: Finestra di manutenzione
description: Informazioni sul modo in cui è possibile configurare il database SQL di Azure e Istanza gestita finestra di manutenzione.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/02/2021
ms.openlocfilehash: 9dc4d17ea95362dd915bd1dfdfd82f4cdec611b8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692811"
---
# <a name="maintenance-window-preview"></a>Finestra di manutenzione (anteprima)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

La funzionalità finestra di manutenzione consente la configurazione di pianificazioni della finestra di manutenzione stimabili per il [database SQL di Azure](sql-database-paas-overview.md) e [istanza gestita di SQL](../managed-instance/sql-managed-instance-paas-overview.md). 

Per altre informazioni sugli eventi di manutenzione, vedere [pianificare gli eventi di manutenzione di Azure nel database SQL di Azure e istanza gestita SQL di Azure](planned-maintenance.md).

## <a name="overview"></a>Panoramica

Azure esegue periodicamente aggiornamenti di manutenzione pianificata sul database SQL di Azure e sulle risorse di SQL Istanza gestita che includono spesso aggiornamenti per hardware sottostante, software che include sistema operativo sottostante e il motore SQL. Durante un aggiornamento di manutenzione, le risorse sono completamente disponibili e accessibili, ma alcuni aggiornamenti di manutenzione richiedono un failover Poiché Azure porta le istanze offline per un breve periodo di tempo per l'applicazione degli aggiornamenti di manutenzione, in media di otto secondi.  Gli aggiornamenti di manutenzione pianificata si verificano una volta ogni 35 giorni in media, il che significa che il cliente può prevedere circa un evento di manutenzione pianificata al mese per ogni database SQL di Azure o istanza gestita di SQL e solo durante gli slot della finestra di manutenzione selezionati dal cliente.   

La finestra di manutenzione è destinata ai carichi di lavoro aziendali che non sono resilienti a problemi di connettività intermittenti che possono derivare da eventi di manutenzione pianificata.

La finestra di manutenzione può essere configurata usando il portale di Azure, PowerShell, l'interfaccia della riga di comando o l'API di Azure. Può essere configurato durante la creazione o per i database SQL esistenti e per le istanze gestite di SQL.

### <a name="gain-more-predictability-with-maintenance-window"></a>Ottenere maggiore prevedibilità con la finestra di manutenzione

Per impostazione predefinita, tutti i database SQL di Azure e i database delle istanze gestite vengono aggiornati al giorno dalle 17.00 alle 8.00 locali per evitare interruzioni massime dell'orario di ufficio. L'ora locale è determinata dall' [area di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) che ospita la risorsa. È possibile modificare ulteriormente gli aggiornamenti di manutenzione in un momento appropriato per il database scegliendo tra due slot della finestra di manutenzione aggiuntiva:

* Finestra **predefinita** , da 17.00 a 8 ora locale lunedì-domenica 
* Finestra del giorno della settimana, dalle 22:00 alle 6.00 ora locale lunedì-giovedì
* Finestra del fine settimana, da 10:00 a 6 ora locale venerdì-domenica

Una volta effettuata la selezione della finestra di manutenzione, tutti gli aggiornamenti pianificati per la manutenzione saranno eseguiti solo durante la finestra scelta.   

> [!Note]
> Oltre agli aggiornamenti pianificati per la manutenzione, in rari casi gli eventi di manutenzione non pianificata possono causare indisponibilità. 

### <a name="cost-and-eligibility"></a>Costo e idoneità

La scelta di una finestra di manutenzione è gratuita per i [tipi di offerta](https://azure.microsoft.com/support/legal/offer-details/)di sottoscrizione seguenti: con pagamento in base al consumo, provider di soluzioni cloud (CSP), Microsoft Enterprise o contratto per i clienti Microsoft.

> [!Note]
> Per offerta di Azure si intende il tipo di sottoscrizione di Azure di cui si dispone. Ad esempio, una sottoscrizione con [tariffe con pagamento in base](https://azure.microsoft.com/offers/ms-azr-0003p/)al consumo, [Azure in Open](https://azure.microsoft.com/en-us/offers/ms-azr-0111p/)e [Visual Studio Enterprise](https://azure.microsoft.com/en-us/offers/ms-azr-0063p/) sono tutte offerte di Azure. Ogni offerta o piano presenta termini e vantaggi diversi. L'offerta o il piano viene visualizzato nella panoramica della sottoscrizione. Per altre informazioni sul passaggio della sottoscrizione a un'offerta diversa, vedere [modificare la sottoscrizione di Azure in un'offerta diversa](/azure/cost-management-billing/manage/switch-azure-offer).

## <a name="advance-notifications"></a>Notifiche avanzate

Le notifiche di manutenzione possono essere configurate in modo da avvisare i clienti in caso di eventi di manutenzione pianificata imminenti 24 ore prima, al momento della manutenzione e al termine della finestra di manutenzione. Per altre informazioni, vedere [notifiche di avanzamento](advance-notifications.md).

## <a name="availability"></a>Disponibilità

### <a name="supported-service-level-objectives"></a>Obiettivi del livello di servizio supportati

La scelta di una finestra di manutenzione diversa da quella predefinita è disponibile in tutte le SLOs **ad eccezione di**:
* Hyperscale 
* VCore Gen4 legacy
* Basic, S0 e S1 
* DC, Fsv2, serie M

### <a name="azure-region-support"></a>Supporto per le aree di Azure

La scelta di una finestra di manutenzione diversa da quella predefinita è attualmente disponibile nelle aree geografiche seguenti:

- Australia orientale
- Australia sudorientale
- Brasile meridionale
- Canada centrale
- Stati Uniti centrali
- Stati Uniti orientali
- Stati Uniti Orientali 2
- Giappone orientale
- NorthCentral US
- Europa settentrionale
- Del US
- Asia sudorientale
- Regno Unito meridionale
- Europa occidentale
- Stati Uniti occidentali
- Stati Uniti occidentali 2

## <a name="gateway-maintenance-for-azure-sql-database"></a>Manutenzione del gateway per il database SQL di Azure

Per ottenere il massimo vantaggio dalle finestre di manutenzione, assicurarsi che le applicazioni client utilizzino i criteri di connessione di reindirizzamento. Reindirizzamento è il criterio di connessione consigliato, in cui i client stabiliscono connessioni direttamente al nodo che ospita il database, causando una riduzione della latenza e una maggiore velocità effettiva.  

* Nel database SQL di Azure tutte le connessioni che usano il criterio di connessione proxy possono essere interessate sia dalla finestra di manutenzione scelta che dalla finestra di manutenzione del nodo del gateway. Tuttavia, le connessioni client che utilizzano i criteri di connessione di reindirizzamento consigliati non sono interessate da un failover di manutenzione del nodo del gateway. 

* Nell'istanza gestita di SQL di Azure, i nodi del gateway si trovano [all'interno del cluster virtuale](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) e hanno la stessa finestra di manutenzione dell'istanza gestita, pertanto l'uso dei criteri di connessione proxy non espone potenzialmente le connessioni a una finestra di manutenzione aggiuntiva.

Per altre informazioni sui criteri di connessione client nel database SQL di Azure, vedere [criteri di connessione del database SQL di Azure](../database/connectivity-architecture.md#connection-policy). 

Per altre informazioni sui criteri di connessione client nell'istanza gestita di SQL di Azure, vedere [tipi di connessione istanza gestita SQL di Azure](../../azure-sql/managed-instance/connection-types-overview.md).


## <a name="next-steps"></a>Passaggi successivi

* [Notifiche avanzate](advance-notifications.md)
* [Configura finestra di manutenzione](maintenance-window-configure.md)

## <a name="learn-more"></a>Altre informazioni

* [Domande frequenti sulla finestra di manutenzione](maintenance-window-faq.yml)
* [Database SQL di Azure](sql-database-paas-overview.md) 
* [Istanza gestita di SQL](../managed-instance/sql-managed-instance-paas-overview.md)
* [Pianificare gli eventi di manutenzione di Azure nel database SQL di Azure e in Azure SQL Istanza gestita](planned-maintenance.md)




