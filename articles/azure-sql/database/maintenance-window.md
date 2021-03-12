---
title: Finestra di manutenzione
description: Informazioni sul modo in cui è possibile configurare la finestra di manutenzione del database SQL di Azure e dell'istanza gestita.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.custom: references_regions
ms.date: 03/11/2021
ms.openlocfilehash: bd91c29ca97c2096c4d8f3df19dbb9eab306b8e7
ms.sourcegitcommit: 6776f0a27e2000fb1acb34a8dddc67af01ac14ac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103149750"
---
# <a name="maintenance-window-preview"></a>Finestra di manutenzione (anteprima)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

La funzionalità finestra di manutenzione consente di configurare la pianificazione della manutenzione per il [database SQL di Azure](sql-database-paas-overview.md) e le risorse dell' [istanza gestita di SQL di Azure](../managed-instance/sql-managed-instance-paas-overview.md) , rendendo prevedibili gli eventi di manutenzione di interesse e meno problematici. 

> [!Note]
> La funzionalità finestra di manutenzione non protegge da eventi non pianificati, ad esempio errori hardware, che potrebbero causare brevi interruzioni della connessione.

## <a name="overview"></a>Panoramica

Azure esegue periodicamente la [manutenzione pianificata](planned-maintenance.md) delle risorse del database SQL e dell'istanza gestita di SQL. Durante l'evento di manutenzione SQL di Azure, i database sono completamente disponibili, ma possono essere soggetti a failover brevi entro i rispettivi contratti di disponibilità per il [database SQL](https://azure.microsoft.com/support/legal/sla/sql-database) e l' [istanza gestita di SQL](https://azure.microsoft.com/support/legal/sla/azure-sql-sql-managed-instance), perché in alcuni casi è necessaria la riconfigurazione delle risorse.

La finestra di manutenzione è destinata ai carichi di lavoro di produzione che non sono resilienti ai failover di database o di istanza e non possono assorbire brevi interruzioni della connessione causati da eventi di manutenzione pianificata. Scegliendo una finestra di manutenzione preferita, è possibile ridurre al minimo l'effetto della manutenzione pianificata, perché si verifica al di fuori del picco orario di ufficio. I carichi di lavoro resilienti e i carichi di lavoro non di produzione possono basarsi sui criteri di manutenzione predefiniti di SQL Azure.

La finestra di manutenzione può essere configurata durante la creazione o per le risorse esistenti di Azure SQL. Può essere configurato usando il portale di Azure, PowerShell, l'interfaccia della riga di comando o l'API di Azure.

> [!Important]
> La configurazione della finestra di manutenzione è un'operazione asincrona a esecuzione prolungata, simile alla modifica del livello di servizio della risorsa SQL di Azure. La risorsa è disponibile durante l'operazione, ad eccezione di un breve failover che si verifica alla fine dell'operazione e in genere dura fino a 8 secondi anche in caso di transazioni a esecuzione prolungata interrotte. Per ridurre al minimo l'effetto del failover, è necessario eseguire l'operazione al di fuori delle ore di punta.

### <a name="gain-more-predictability-with-maintenance-window"></a>Ottenere maggiore prevedibilità con la finestra di manutenzione

Per impostazione predefinita, i criteri di manutenzione di Azure SQL bloccano gli aggiornamenti interessati durante il periodo dalle **8.00 alle 17.00 ora locale ogni giorno** per evitare eventuali rotture durante l'orario di lavoro di picco tipico. L'ora locale è determinata dalla posizione dell' [area di Azure](https://azure.microsoft.com/global-infrastructure/geographies/) che ospita la risorsa e può osservare l'ora legale in base alla definizione del fuso orario locale. 

È possibile adattare ulteriormente gli aggiornamenti di manutenzione a un tempo appropriato per le risorse SQL di Azure scegliendo tra due slot della finestra di manutenzione aggiuntiva:
 
* Finestra del giorno della settimana, dal 10 al 6 ora locale lunedì-giovedì
* Finestra del fine settimana, da 10:00 a 6 ora locale venerdì-domenica

Una volta effettuata la selezione della finestra di manutenzione e la configurazione del servizio è stata completata, la manutenzione pianificata verrà eseguita solo durante la finestra scelta.   

> [!Important]
> In rari casi in cui eventuali rimandi di azione potrebbero causare un grave effetto, ad esempio l'applicazione di patch di sicurezza critiche, la finestra di manutenzione configurata potrebbe essere temporaneamente sottoposta a override. 

### <a name="cost-and-eligibility"></a>Costo e idoneità

La configurazione e l'uso della finestra di manutenzione sono gratuite per tutti i [tipi di offerta](https://azure.microsoft.com/support/legal/offer-details/)idonei: con pagamento in base al consumo, provider di soluzioni cloud (CSP), Microsoft contratto Enterprise o contratto per i clienti Microsoft.

> [!Note]
> Per offerta di Azure si intende il tipo di sottoscrizione di Azure di cui si dispone. Ad esempio, una sottoscrizione con [tariffe con pagamento in base](https://azure.microsoft.com/offers/ms-azr-0003p/)al consumo, [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p/)e [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) sono tutte offerte di Azure. Ogni offerta o piano presenta termini e vantaggi diversi. L'offerta o il piano viene visualizzato nella panoramica della sottoscrizione. Per altre informazioni sul passaggio della sottoscrizione a un'offerta diversa, vedere [modificare la sottoscrizione di Azure in un'offerta diversa](/azure/cost-management-billing/manage/switch-azure-offer).

## <a name="advance-notifications"></a>Notifiche avanzate

Le notifiche di manutenzione possono essere configurate in modo da ricevere avvisi sui prossimi eventi di manutenzione pianificata per il database SQL di Azure 24 ore in anticipo, al momento della manutenzione e al termine della manutenzione. Per altre informazioni, vedere [notifiche di avanzamento](advance-notifications.md).

## <a name="availability"></a>Disponibilità

### <a name="supported-service-level-objectives"></a>Obiettivi del livello di servizio supportati

La scelta di una finestra di manutenzione diversa da quella predefinita è disponibile in tutte le SLOs **ad eccezione di**:
* Hyperscale 
* Pool di istanze
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
- Asia orientale
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

* Nell'istanza gestita di SQL di Azure, i nodi del gateway sono ospitati [all'interno del cluster virtuale](../../azure-sql/managed-instance/connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) e hanno la stessa finestra di manutenzione dell'istanza gestita, ma è comunque consigliabile usare i criteri di connessione di reindirizzamento per ridurre al minimo il numero di rotture durante l'evento di manutenzione.

Per altre informazioni sui criteri di connessione client nel database SQL di Azure, vedere [criteri di connessione del database SQL di Azure](../database/connectivity-architecture.md#connection-policy). 

Per altre informazioni sui criteri di connessione client nell'istanza gestita di SQL di Azure, vedere [tipi di connessione istanza gestita di SQL Azure](../../azure-sql/managed-instance/connection-types-overview.md).

## <a name="considerations-for-azure-sql-managed-instance"></a>Considerazioni sull'istanza gestita di SQL di Azure

Istanza gestita di SQL di Azure è costituita da componenti del servizio ospitati in un set dedicato di macchine virtuali isolate che vengono eseguite all'interno della subnet della rete virtuale del cliente. Queste macchine virtuali formano [i cluster virtuali](/azure/azure-sql/managed-instance/connectivity-architecture-overview#high-level-connectivity-architecture) che possono ospitare più istanze gestite. La finestra di manutenzione configurata nelle istanze di una subnet può influenzare il numero di cluster virtuali all'interno della subnet e la distribuzione delle istanze tra cluster virtuali. Questo potrebbe richiedere una considerazione di pochi effetti.

### <a name="maintenance-window-configuration-is-long-running-operation"></a>La configurazione della finestra di manutenzione è un'operazione di lunga durata 
Tutte le istanze ospitate in un cluster virtuale condividono la finestra di manutenzione. Per impostazione predefinita, tutte le istanze gestite sono ospitate nel cluster virtuale con la finestra di manutenzione predefinita. Specificando un'altra finestra di manutenzione per l'istanza gestita durante la creazione o successivamente, il servizio deve essere inserito in un cluster virtuale con la finestra di manutenzione corrispondente. Se nella subnet non è presente un cluster virtuale di questo tipo, è necessario crearne uno nuovo per adattarlo all'istanza. Per ospitare un'istanza aggiuntiva nel cluster virtuale esistente, potrebbe essere necessario ridimensionare il cluster. Entrambe le operazioni contribuiscono alla durata della configurazione della finestra di manutenzione per un'istanza gestita.
La durata prevista della configurazione della finestra di manutenzione nell'istanza gestita può essere calcolata usando [la durata stimata delle operazioni di gestione delle istanze](/azure/azure-sql/managed-instance/management-operations-overview#duration).

> [!Important]
> Un breve failover si verifica al termine dell'operazione di manutenzione e in genere dura fino a 8 secondi anche in caso di transazioni con esecuzione prolungata interrotte. Per ridurre al minimo l'effetto del failover è necessario pianificare l'operazione al di fuori delle ore di punta.

### <a name="ip-address-space-requirements"></a>Requisiti dello spazio degli indirizzi IP
Ogni nuovo cluster virtuale nella subnet richiede indirizzi IP aggiuntivi in base all' [allocazione di indirizzi IP del cluster virtuale](/azure/azure-sql/managed-instance/vnet-subnet-determine-size#determine-subnet-size). La modifica della finestra di manutenzione per l'istanza gestita esistente richiede anche una [capacità IP aggiuntiva temporanea](/azure/azure-sql/managed-instance/vnet-subnet-determine-size#address-requirements-for-update-scenarios) come nello scenario vcore di ridimensionamento per il livello di servizio corrispondente.

### <a name="ip-address-change"></a>Modifica degli indirizzi IP
La configurazione e la modifica della finestra di manutenzione causano la modifica dell'indirizzo IP dell'istanza, all'interno dell'intervallo di indirizzi IP della subnet.

> [!Important]
>  Assicurarsi che le regole NSG e firewall non blocchino il traffico dati dopo la modifica dell'indirizzo IP. 

## <a name="next-steps"></a>Passaggi successivi

* [Notifiche avanzate](advance-notifications.md)
* [Configura finestra di manutenzione](maintenance-window-configure.md)

## <a name="learn-more"></a>Altre informazioni

* [Domande frequenti sulla finestra di manutenzione](maintenance-window-faq.yml)
* [Database SQL di Azure](sql-database-paas-overview.md) 
* [Istanza gestita di SQL](../managed-instance/sql-managed-instance-paas-overview.md)
* [Pianificare gli eventi di manutenzione di Azure nel database SQL di Azure e nell'istanza gestita di SQL di Azure](planned-maintenance.md)





