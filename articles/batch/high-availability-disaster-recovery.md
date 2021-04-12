---
title: Disponibilità elevata e ripristino di emergenza
description: Informazioni su come progettare un'applicazione Batch per un'interruzione a livello di area.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: 51bcb0cfa35aacd24c0f79082491ef1fc7040889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831007"
---
# <a name="design-your-batch-application-for-high-availability"></a>Progettare l'applicazione batch per la disponibilità elevata

Azure Batch è disponibile in tutte le aree di Azure, ma quando viene creato un account batch, è necessario associarlo a un'area specifica. a cui faranno riferimento tutte le operazioni relative all'account Batch. I pool e le macchine virtuali associate, ad esempio, dovranno essere creati nella stessa area dell'account Batch.

Quando si progetta un'applicazione che usa Batch, è necessario considerare la possibilità che Batch non sia disponibile in una determinata area. È possibile riscontrare una situazione rara in cui si è verificato un problema con l'intera area, l'intero servizio batch nell'area o l'account batch specifico.

Se l'applicazione o la soluzione che usa Batch deve essere sempre disponibile e quindi deve essere progettata in modo che ne venga eseguito il failover in un'altra area o che il carico di lavoro venga suddiviso tra due o più aree. Entrambi gli approcci richiedono almeno due account Batch, che devono essere creati in aree diverse.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Più account Batch in più aree

L'uso di più account batch in diverse aree consente all'applicazione di continuare l'esecuzione se un account batch in un'area diventa non disponibile. Se l'applicazione deve essere a disponibilità elevata, la presenza di più account è particolarmente importante.

In alcuni casi, le applicazioni possono essere progettate per usare intenzionalmente due o più aree. Quando ad esempio è richiesta una notevole capacità, è possibile che sia necessario usare più aree per gestire un'applicazione su larga scala o far fronte a un'espansione futura. Queste applicazioni richiedono anche più account batch (uno per area usata).

## <a name="design-considerations-for-providing-failover"></a>Considerazioni di progettazione per consentire il failover

Quando si fornisce la possibilità di eseguire il failover in un'area alternativa, è necessario prendere in considerazione tutti i componenti di una soluzione. non è sufficiente disporre semplicemente di un secondo account batch. Nella maggior parte delle applicazioni Batch, ad esempio, è necessario un account di archiviazione di Azure, che deve trovarsi nella stessa area dell'account Batch per poter ottenere prestazioni accettabili.

Quando si progetta una soluzione di cui poter eseguire il failover, tenere presente quanto segue:

- Creare in anticipo tutti gli account necessari in ogni area, ad esempio l'account Batch e l'account di archiviazione. Spesso non è previsto alcun addebito per la creazione di account e i costi si accumulano solo quando viene usato l'account o quando vengono archiviati i dati.
- Assicurarsi che le [quote](batch-quota-limit.md) appropriate siano impostate in anticipo su tutti gli account, in modo da poter allocare il numero di core necessari usando l'account batch.
- Usare modelli e/o script per automatizzare la distribuzione dell'applicazione in un'area.
- Mantenere costantemente aggiornati i file binari dell'applicazione e i dati di riferimento in tutte le aree. In questo modo, infatti, l'area può essere portata online rapidamente senza dover attendere il caricamento e la distribuzione dei file. Se, ad esempio, si installa e si fa riferimento a un'applicazione personalizzata da installare nei nodi del pool tramite i pacchetti dell'applicazione Batch, quando viene generata una nuova versione dell'applicazione, è necessario che venga caricata in ogni account Batch e che ad essa faccia riferimento la configurazione del pool. In alternativa, è possibile impostarla come versione predefinita.
- Nell'applicazione che chiama batch, archiviazione e altri servizi, semplificare il passaggio dei client o il carico a diverse aree.
- Si consiglia di passare spesso a un'area alternativa come parte del normale funzionamento. Ad esempio, con due distribuzioni in aree separate, passare all'area alternativa ogni mese.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla creazione di account Batch con il [portale di Azure](batch-account-create-portal.md), l'[interfaccia della riga di comando di Azure](./scripts/batch-cli-sample-create-account.md), [PowerShell](batch-powershell-cmdlets-get-started.md) o l'[API di gestione Batch](batch-management-dotnet.md).
- Informazioni sulle [quote predefinite associate a un account batch](batch-quota-limit.md) e sul modo in cui è possibile aumentare le quote.
