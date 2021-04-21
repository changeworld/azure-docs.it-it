---
title: Domande frequenti - Servizio Migrazione del database di Azure
description: Domande frequenti sull'uso di Servizio Migrazione del database di Azure per eseguire migrazioni di database.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 29240f371d39ef2d3234e3207cc91768478bf3ff
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748504"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Domande frequenti sull'uso di Servizio Migrazione del database di Azure

Questo articolo elenca le domande frequenti sull'uso Servizio Migrazione del database di Azure insieme alle risposte correlate.

## <a name="overview"></a>Panoramica

**D. Che cos'Servizio Migrazione del database di Azure?**
Servizio Migrazione del database di Azure è un servizio completamente gestito progettato per consentire migrazioni semplici da più origini di database alle piattaforme dati di Azure con tempi di inattività minimi. Il servizio è attualmente disponibile a disponibilità generale, con attività di sviluppo in corso incentrate su:

* Affidabilità e prestazioni.
* Aggiunta iterativa di coppie origine-destinazione.
* Investimento continuo su migrazioni senza problemi.

**D. Quali coppie di origine/destinazione Servizio Migrazione del database di Azure attualmente supportate?**
Il servizio supporta attualmente un'ampia gamma di coppie di origine/destinazione o scenari di migrazione. Per un elenco completo dello stato di ogni scenario di migrazione disponibile vedere l'articolo [Stato degli scenari di migrazione supportati dal Servizio Migrazione del database di Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/dms/resource-scenario-status.md).

**D. Quali versioni di SQL Server supportano Servizio Migrazione del database di Azure come origine?**
Durante la migrazione da SQL Server, le origini supportate per Servizio Migrazione del database di Azure sono SQL Server 2005 fino SQL Server 2019.

**D: Quando si usa Servizio Migrazione del database di Azure, qual è la differenza tra una migrazione offline e una migrazione online?**
È possibile usare Servizio Migrazione del database di Azure per eseguire migrazioni offline e online. Con una migrazione *offline,* il tempo di inattività dell'applicazione inizia all'avvio della migrazione. Con una *migrazione online,* il tempo di inattività è limitato al tempo di riduzione al termine della migrazione. È consigliabile testare una migrazione offline per determinare se il tempo di inattività è accettabile. In caso contrario, eseguire una migrazione online.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium. Per altre informazioni, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/database-migration/) di Servizio Migrazione del database di Azure.

**D. In che modo Servizio Migrazione del database di Azure confronto con altri strumenti di migrazione di database Microsoft, ad esempio Database Migration Assistant (DMA) o SQL Server Migration Assistant (SSMA)?**
Servizio Migrazione del database di Azure è il metodo preferito per la migrazione del database Microsoft Azure su larga scala. Per altre informazioni sul confronto tra Servizio Migrazione del database di Azure e altri strumenti di migrazione del database Microsoft e per consigli [sull'uso](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)del servizio per vari scenari, vedere il post di blog Differentiating Microsoft Database Migration Tools and Services (Differenziazione di strumenti e servizi di migrazione del database di Microsoft).

**D. Qual è Servizio Migrazione del database di Azure confronto con l'Azure Migrate offerta?**
Azure Migrate assiste nella migrazione di macchine virtuali locali a IaaS di Azure. Il servizio valuta l'idoneità alla migrazione e il dimensionamento in base alle prestazioni e offre stime dei costi per l'esecuzione delle macchine virtuali locali in Azure. Azure Migrate è utile per le migrazioni in modalità lift-and-shift di carichi di lavoro basati su VM locali a VM IaaS di Azure. Tuttavia, a differenza Servizio Migrazione del database di Azure, Azure Migrate non è un servizio di migrazione di database specializzato per le piattaforme di database relazionali PaaS di Azure, ad esempio database SQL di Azure o Istanza gestita di SQL di Azure.

**D. Il Servizio Migrazione del database archivia i dati dei clienti?**
No. Il Servizio Migrazione del database non archivia i dati dei clienti.

## <a name="setup"></a>Configurazione

**D. Quali sono i prerequisiti per l'uso Servizio Migrazione del database di Azure?**
Esistono diversi prerequisiti necessari per garantire che l'Servizio Migrazione del database di Azure senza problemi durante le migrazioni del database. Alcuni dei prerequisiti si applicano a tutti gli scenari (coppie di origine-destinazione) supportati dal servizio, mentre altri prerequisiti sono univoci per uno scenario specifico.

In base ai prerequisiti del Servizio Migrazione del database di Azure comuni a tutti gli scenari di migrazione supportati, è necessario:

* Creare una rete virtuale di Microsoft Azure per il Servizio Migrazione del database di Azure usando il modello di distribuzione di Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
* Assicurarsi che le regole del gruppo di sicurezza di rete della rete virtuale non blocchino la porta 443 per ServiceTags di ServiceBus, Storage e AzureMonitor. Per informazioni dettagliate sul filtro del traffico dei gruppi di sicurezza di rete della rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.

Per un elenco di tutti i prerequisiti necessari per concorrere a scenari di migrazione [](./dms-overview.md) specifici usando Servizio Migrazione del database di Azure, vedere le esercitazioni correlate nella documentazione di Servizio Migrazione del database di Azure su docs.microsoft.com.

**D. Ricerca per categorie trovare l'indirizzo IP per Servizio Migrazione del database di Azure in modo da poter creare un elenco elementi consentiti per le regole del firewall usate per accedere al database di origine per la migrazione?**
Potrebbe essere necessario aggiungere regole del firewall che consentano Servizio Migrazione del database di Azure accedere al database di origine per la migrazione. L'indirizzo IP per il servizio è dinamico, ma se si usa ExpressRoute, questo indirizzo viene assegnato privatamente dalla rete aziendale. Il modo più semplice per identificare l'indirizzo IP appropriato è cercare nello stesso gruppo di risorse della risorsa Servizio Migrazione del database di Azure di cui è stato effettuato il provisioning per trovare l'interfaccia di rete associata. Il nome della risorsa dell'interfaccia di rete inizia in genere con il prefisso NIC ed è seguito da una sequenza alfanumerica univoca, ad esempio NIC-jj6tnztnmarpsskr82rbndyp. Se si seleziona questa risorsa dell'interfaccia di rete, è possibile visualizzare l'indirizzo IP che deve essere incluso nell'elenco indirizzi consentiti nella pagina del portale di Azure della panoramica.

Potrebbe essere necessario includere nell'elenco indirizzi consentiti anche l'origine della porta su cui SQL Server è in ascolto. Per impostazione predefinita, si tratta della porta 1433, ma l'istanza di SQL Server di origine potrebbe essere configurata per l'ascolto anche su altre porte. In questo caso, è necessario includere anche tali porte nell'elenco indirizzi consentiti. È possibile determinare la porta su cui SQL Server è in ascolto usando una query DMV:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

È anche possibile determinare la porta su cui SQL Server è in ascolto eseguendo una query sul log degli errori di SQL Server:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**D. Ricerca per categorie configurare un Rete virtuale di Microsoft Azure?**
Anche se sono presenti più esercitazioni Microsoft che illustrano il processo di configurazione di una rete virtuale, la documentazione ufficiale viene visualizzata nell'articolo [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md).

## <a name="usage"></a>Utilizzo

**D. Che cos'è un riepilogo dei passaggi necessari per usare Servizio Migrazione del database di Azure eseguire una migrazione del database?**
Durante una semplice migrazione di database tipica, è necessario:

1. Creare uno o più database di destinazione.
2. Valutare i database di origine.
    * Per migrazioni omogenee, valutare i database esistenti usando [DMA.](https://www.microsoft.com/download/details.aspx?id=53595)
    * Per migrazioni eterogenee (da origini di competenza), valutare i database esistenti con [SSMA.](/sql/ssma/sql-server-migration-assistant) Si usa anche SSMA per convertire gli oggetti di database ed eseguire la migrazione dello schema alla piattaforma di destinazione.
3. Creare un'istanza del servizio Migrazione del database di Azure.
4. Creare un progetto di migrazione specificando i database di origine, i database di destinazione e le tabelle di cui eseguire la migrazione.
5. Avviare il carico completo.
6. Selezionare la convalida successiva.
7. Eseguire un passaggio manuale dell'ambiente di produzione al nuovo database basato sul cloud.

## <a name="troubleshooting-and-optimization"></a>Risoluzione dei problemi e ottimizzazione

**D. Si sta configurando un progetto di migrazione nel Servizio Migrazione del database e si hanno difficoltà a connettersi al database di origine. Cosa dovrei fare?**
In caso di problemi di connessione al sistema di database di origine durante la migrazione, creare una macchina virtuale nella stessa subnet della rete virtuale con cui si è impostata l'istanza del Servizio Migrazione del database. Nella macchina virtuale dovrebbe essere possibile eseguire un test di connessione, ad esempio usando un file UDL per testare una connessione a SQL Server o scaricando Robo 3T per testare le connessioni MongoDB. Se il test di connessione ha esito positivo, non dovrebbe verificarsi un problema di connessione al database di origine. Se il test di connessione non riesce, contattare l'amministratore di rete.

**D. Perché l'Servizio Migrazione del database di Azure non è disponibile o è stata arrestata?**
Se l'utente arresta in modo esplicito Servizio Migrazione del database di Azure (DMS) o se il servizio è inattivo per un periodo di 24 ore, il servizio sarà in stato arrestato o sospeso automaticamente. In entrambi i casi, il servizio non sarà disponibile e risulterà in stato di arresto.  Per riprendere l'esecuzione delle migrazioni attive, riavviare il servizio.

**D. Sono disponibili raccomandazioni per ottimizzare le prestazioni dei Servizio Migrazione del database di Azure?**
È possibile eseguire alcune operazioni per velocizzare la migrazione del database con il servizio:

* Usare il piano tariffario per utilizzo generico per più CPU quando si crea l'istanza del servizio per consentire al servizio di sfruttare più vCPU per la parallelizzazione e un trasferimento dei dati più veloce.
* Aumentare temporaneamente le prestazioni dell'istanza di destinazione del database SQL di Azure passando allo SKU del piano Premium durante l'operazione di migrazione dei dati per ridurre al minimo la limitazione delle richieste del database SQL di Azure che potrebbe influire sulle attività di trasferimento dei dati quando si usano SKU di livello inferiore.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica del Servizio Migrazione del database di Azure e informazioni sulla disponibilità a livello di area, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](dms-overview.md).
