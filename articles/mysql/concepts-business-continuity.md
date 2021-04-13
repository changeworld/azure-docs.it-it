---
title: Continuità aziendale-database di Azure per MySQL
description: Informazioni sulla continuità aziendale (ripristino temporizzato, interruzione del data center, ripristino geografico) quando si usa il servizio database di Azure per MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: e58508e561a33a73e6f6752fc12bc5938b8d7488
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309782"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---single-server"></a>Panoramica della continuità aziendale con database di Azure per MySQL-server singolo

Questo articolo descrive le funzionalità offerte da database di Azure per MySQL per la continuità aziendale e il ripristino di emergenza. Informazioni sulle opzioni per il ripristino da eventi di arresto improvviso che potrebbero provocare la perdita di dati o la disattivazione del database e dell'applicazione. Informazioni sulle operazioni da eseguire quando si verifica un errore generato da un utente o da un'applicazione che influisce sull'integrità dei dati, se in un'area di Azure si verifica un'interruzione o quando l'applicazione richiede manutenzione.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Funzionalità che è possibile usare per assicurare la continuità aziendale

Quando si sviluppa il piano di continuità aziendale, è necessario conoscere il tempo massimo accettabile prima che l'applicazione venga ripristinata completamente dopo l'evento di arresto improvviso. Si tratta dell'obiettivo del tempo di ripristino (RTO). È anche necessario conoscere la perdita massima di aggiornamenti di dati recenti (intervallo di tempo) che l'applicazione riesce a tollerare durante il ripristino dopo l'evento di arresto improvviso, ovvero l'obiettivo del punto di recupero (RPO).

Il server singolo di database di Azure per MySQL offre funzionalità di continuità aziendale e ripristino di emergenza che includono backup con ridondanza geografica con la possibilità di avviare il ripristino geografico e di distribuire le repliche di lettura in un'area diversa. Ognuna presenta caratteristiche diverse per il tempo di recupero e la potenziale perdita di dati. Con la funzionalità di [ripristino geografico](concepts-backup.md) , viene creato un nuovo server usando i dati di backup replicati da un'altra area. Il tempo complessivo necessario per il ripristino e il recupero dipende dalle dimensioni del database e dalla quantità di log da ripristinare. Il tempo complessivo necessario per stabilire il server varia da pochi minuti ad alcune ore. Con le [repliche di lettura](concepts-read-replicas.md), i log delle transazioni dal database primario vengono trasmessi in modo asincrono alla replica. In caso di interruzione di un database primario a causa di un errore a livello di zona o di area, il failover alla replica fornisce una RTO più breve e una riduzione della perdita di dati.

> [!NOTE]
> Il ritardo tra il database primario e la replica dipende dalla latenza tra i siti, dalla quantità di dati da trasmettere e soprattutto dal carico di lavoro di scrittura del server primario. Carichi di lavoro di scrittura intensivi possono generare un ritardo significativo. 
>
> A causa della natura asincrona della replica usata per le repliche di lettura, **non devono** essere considerate come una soluzione a disponibilità elevata, poiché i ritardi più elevati possono significare più RTO e RPO. Solo per i carichi di lavoro in cui il ritardo rimane inferiore nelle ore di punta e non di punta del carico di lavoro, le repliche di lettura possono fungere da alternativa a disponibilità elevata. In caso contrario, le repliche di lettura sono destinate a una vera e propria scalabilità in lettura per i carichi di lavoro pesanti pronti e per gli scenari di ripristino di emergenza

Nella tabella seguente vengono confrontati RTO e RPO in uno scenario di **carico di lavoro tipico** :

| **Capacità** | **Base** | **Utilizzo generico** | **Ottimizzato per la memoria** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Ripristino temporizzato dal backup | Qualsiasi punto di ripristino compreso nel periodo di conservazione <br/> RTO-varia <br/>RPO < 15 min| Qualsiasi punto di ripristino compreso nel periodo di conservazione <br/> RTO-varia <br/>RPO < 15 min | Qualsiasi punto di ripristino compreso nel periodo di conservazione <br/> RTO-varia <br/>RPO < 15 min |
| Ripristino geografico dai backup con replica geografica | Non supportato | RTO-varia <br/>RPO < 1 ora | RTO-varia <br/>RPO < 1 ora |
| Repliche in lettura | RTO minuti * <br/>RPO < 5 min * | RTO minuti * <br/>RPO < 5 min *| RTO minuti * <br/>RPO < 5 min *|

 \* RTO e RPO **possono essere molto più elevati** in alcuni casi, a seconda dei diversi fattori, tra cui la latenza tra siti, la quantità di dati da trasmettere e un carico di lavoro di scrittura database primario.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Ripristinare un server in seguito a errore di un'applicazione o di un utente

È possibile utilizzare i backup del servizio per ripristinare un server da vari eventi di disturbo. Un utente può accidentalmente eliminare alcuni dati, una tabella importante o addirittura un intero database. Un'applicazione potrebbe sovrascrivere accidentalmente dei dati con dati errati a causa di un difetto dell'applicazione e così via.

È possibile eseguire un ripristino temporizzato per creare una copia del server a un punto valido nel tempo. Questo punto nel tempo deve essere compreso nel periodo di conservazione di backup configurato per il server. Dopo il ripristino dei dati nel nuovo server, è possibile sostituire il server originale con il server appena ripristinato o copiare i dati necessari dal server ripristinato nel server originale.

> [!IMPORTANT]
> I server eliminati possono essere ripristinati solo entro **cinque giorni** dall'eliminazione dopo il quale vengono eliminati i backup. È possibile accedere al backup del database e ripristinarlo solo dalla sottoscrizione di Azure che ospita il server. Per ripristinare un server eliminato, vedere la [procedura documentata](howto-restore-dropped-server.md). Per proteggere le risorse del server, post-distribuzione, da eliminazioni accidentali o modifiche impreviste, gli amministratori possono sfruttare [blocchi di gestione](../azure-resource-manager/management/lock-resources.md).

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Eseguire il ripristino in seguito un'interruzione del data center a livello di area di Azure

Anche se raramente, un data center di Azure può subire un'interruzione del servizio. Quando si verifica un'interruzione, viene generata un'interruzione delle attività che potrebbe durare solo pochi minuti, ma anche ore.

Una delle opzioni è attendere che il server torni online al termine dell'interruzione del data center. Questa opzione funziona per le applicazioni che possono permettersi di avere il server offline per un certo periodo, ad esempio gli ambienti di sviluppo. Quando il data center registra un'interruzione del servizio, non si sa quanto tempo essa durerà, quindi questa opzione funziona solo se è possibile rinunciare al server per un periodo di tempo.

## <a name="geo-restore"></a>Ripristino geografico

La funzionalità di ripristino geografico ripristina il server usando i backup con ridondanza geografica. I backup sono ospitati nell' [area abbinata](../best-practices-availability-paired-regions.md)del server. Questi backup sono accessibili anche quando l'area in cui il server è ospitato è offline. È possibile eseguire il ripristino da questi backup a qualsiasi altra area e riportare il server online. Per altre informazioni sul ripristino geografico, vedere l' [articolo Concetti relativi a backup e ripristino](concepts-backup.md).

> [!IMPORTANT]
> Il ripristino geografico è possibile solo se è stato effettuato il provisioning del server con l'archivio di backup con ridondanza geografica. Per passare da backup con ridondanza locale a backup con ridondanza geografica per un server esistente, è necessario eseguire un dump del server esistente usando mysqldump e quindi ripristinarlo in un nuovo server configurato per i backup con ridondanza geografica.

## <a name="cross-region-read-replicas"></a>Repliche Read tra aree

È possibile usare le repliche di lettura tra aree per migliorare la continuità aziendale e la pianificazione del ripristino di emergenza. Le repliche di lettura vengono aggiornate in modo asincrono usando la tecnologia di replica dei log binari di MySQL. Altre informazioni sulle repliche di lettura, le aree disponibili e su come eseguire il failover dall' [articolo leggere i concetti relativi alle repliche](concepts-read-replicas.md). 

## <a name="faq"></a>Domande frequenti

### <a name="where-does-azure-database-for-mysql-store-customer-data"></a>Dove il database di Azure per MySQL archivia i dati dei clienti?
Per impostazione predefinita, database di Azure per MySQL non sposta o archivia i dati del cliente all'esterno dell'area in cui viene distribuito. Tuttavia, i clienti possono scegliere di abilitare i [backup con ridondanza geografica](concepts-backup.md#backup-redundancy-options) o creare una [replica di lettura tra aree](concepts-read-replicas.md#cross-region-replication) per archiviare i dati in un'altra area.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [backup automatizzati in Database di Azure per MySQL](concepts-backup.md).
- Informazioni su come effettuare il ripristino usando il [portale di Azure](howto-restore-server-portal.md) o l'[interfaccia della riga di comando di Azure](howto-restore-server-cli.md).
- Informazioni sulle [repliche in lettura in Database di Azure per MySQL](concepts-read-replicas.md).
