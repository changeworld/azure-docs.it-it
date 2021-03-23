---
title: Vantaggi dell'utilizzo di Azure NetApp Files per la distribuzione di SQL Server | Microsoft Docs
description: Mostra i vantaggi delle prestazioni dell'analisi dettagliata dei costi sull'uso di Azure NetApp Files per la distribuzione di SQL Server.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: b-juche
ms.openlocfilehash: 7bbedae3d2dd080819b295d815bec68309916a6e
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803695"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>Vantaggi dell'utilizzo di Azure NetApp Files per la distribuzione di SQL Server

Azure NetApp Files riduce SQL Server costo totale di proprietà (TCO) rispetto alle soluzioni di archiviazione a blocchi.  Con l'archiviazione a blocchi, le macchine virtuali hanno imposto limiti per le operazioni di I/O e larghezza di banda per le operazioni su disco, i limiti della larghezza di banda di rete vengono applicati solo ai Azure NetApp Files.  In altre parole, non vengono applicati limiti di I/O a livello di macchina virtuale per Azure NetApp Files. Senza questi limiti di I/O, SQL Server in esecuzione su macchine virtuali più piccole connesse a Azure NetApp Files può essere eseguito e SQL Server in esecuzione su macchine virtuali molto più grandi. Il dimensionamento delle istanze in modo da ridurre il costo di calcolo al 25% del tag di prezzo precedente.  *È possibile ridurre i costi di calcolo con Azure NetApp Files.*  

I costi di calcolo, tuttavia, sono di piccole dimensioni rispetto ai costi di licenza SQL Server.  Microsoft SQL Server [licenza](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf) è associata al numero di core fisici. Di conseguenza, la riduzione delle dimensioni dell'istanza introduce un risparmio di costi ancora maggiore per le licenze software. *È possibile ridurre i costi di licenza software con Azure NetApp Files.*

Il costo dell'archiviazione è variabile a seconda delle dimensioni effettive del database. Indipendentemente dalla risorsa di archiviazione selezionata, la capacità ha un costo, indipendentemente dal fatto che si tratti di un disco gestito o di una condivisione file.  Man mano che aumentano le dimensioni del database e lo spazio di archiviazione aumenta di costi, lo spazio di archiviazione contribuisce all'aumento del TCO, che influisce sul costo complessivo.  Di conseguenza, l'asserzione viene modificata come segue: *è possibile ridurre i costi di distribuzione SQL Server con Azure NetApp files.* 

Questo articolo illustra l'analisi dettagliata dei costi e i vantaggi in termini di prestazioni relativi all'uso di Azure NetApp Files per la distribuzione di SQL Server. Non solo le istanze più piccole hanno una CPU sufficiente per consentire il funzionamento del database solo con blocchi in istanze più grandi, *in molti casi le istanze più piccole sono ancora più efficienti rispetto alle più grandi controparti basate su disco a causa del Azure NetApp files.* 

## <a name="detailed-cost-analysis"></a>Analisi dettagliata dei costi 

I due set di elementi grafici in questa sezione illustrano l'esempio di TCO.  Il numero e il tipo di dischi gestiti, il livello di servizio Azure NetApp Files e la capacità per ogni scenario sono stati selezionati per ottenere le prestazioni migliori.  Ogni grafico è costituito da computer raggruppati (D16 con Azure NetApp Files, rispetto a D64 con disco gestito da esempio) e i prezzi sono suddivisi per ogni tipo di computer.  

Il primo set di elementi grafici Mostra il costo complessivo della soluzione usando le dimensioni di un database da 1 TiB, confrontando il D16s_v3 con il D64, il D8 al D32 e il D4 al D16. Le operazioni di i/o al secondo previste per ogni configurazione sono indicate da una linea verde o gialla e corrispondono all'asse Y del lato destro.

[![Immagine che mostra il costo complessivo della soluzione usando una dimensione del database di 1 tib. ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


Il secondo set di elementi grafici Mostra il costo complessivo usando un database 50-TiB. In caso contrario, i confronti sono uguali a D16 rispetto a Azure NetApp Files rispetto a D64 con Block by example. 

[![Immagine che mostra i costi complessivi utilizzando le dimensioni del database 50-Tib. ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>Prestazioni e molto altro  

Per fornire l'asserzione di riduzione dei costi significativa sono necessarie numerose prestazioni: le istanze più grandi nell'inventario generale di Azure supportano 80.000 IOPS del disco per esempio. Un singolo volume Azure NetApp Files può raggiungere 80.000 IOPS del database e le istanze, ad esempio D16, sono in grado di utilizzare lo stesso. Il D16, normalmente in grado di 25.600 IOPS del disco, è pari al 25% della dimensione di D64.  Il D64s_v3 è in grado di 80.000 IOPS del disco e, di conseguenza, presenta un ottimo punto di confronto di livello superiore.

Il D16s_v3 può guidare un volume Azure NetApp Files a 80.000 IOPS del database. Come dimostrato dallo strumento di benchmarking di SQL Storage benchmark (SSB), l'istanza di D16 ha eseguito un carico di lavoro 125% maggiore rispetto a quello ottenibile sul disco dall'istanza di D64.  Per informazioni dettagliate sullo strumento, vedere la sezione [strumento di test SSB](#ssb-testing-tool) .

Utilizzando una dimensione di working set 1-TiB e un 80% di letture, 20% di carico di lavoro SQL Server aggiornamento, sono state misurate le funzionalità di prestazioni della maggior parte delle istanze della classe di istanza D. la maggior parte, non tutte, perché le istanze D2 e D64 sono state escluse dai test. Il primo è stato escluso perché non supporta la rete accelerata e quest'ultimo perché è il punto di confronto. Vedere il grafico seguente per comprendere i limiti di D4s_v3, D8s_v3, D16s_v3 e D32s_v3, rispettivamente.  I test di archiviazione su disco gestito non vengono visualizzati nel grafico. I valori di confronto vengono disegnati direttamente dalla [tabella limiti della macchina virtuale di Azure](../virtual-machines/dv3-dsv3-series.md) per il tipo di istanza della classe D.

Con Azure NetApp Files, ogni istanza della classe D può soddisfare o superare le funzionalità di prestazioni del disco delle istanze due volte più grandi.  *È possibile ridurre significativamente i costi delle licenze software con Azure NetApp Files.*  

* Il D4 al 75% di utilizzo della CPU corrisponde alle funzionalità disco del D16.  
    * Il valore di D16 è limitato a 25.600 IOPS del disco.  
* Il D8 al 75% di utilizzo della CPU corrisponde alle funzionalità disco del D32.  
    * Il valore di D32 è limitato a 51.200 IOPS del disco.  
* Il D16 al 55% di utilizzo della CPU corrisponde alle funzionalità disco del D64.  
    * Il valore di D64 è limitato a 80.000 IOPS del disco.  
* Il D32 al 15% di utilizzo della CPU corrisponde anche alle funzionalità disco del D64.  
    * Il valore di D64, come indicato in precedenza, è limitato a 80.000 IOPS del disco.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>Test limiti CPU S3B-prestazioni e potenza di elaborazione

Il diagramma seguente riepiloga il test dei limiti della CPU S3B:

![Diagramma che mostra la percentuale media della CPU per SQL Server a istanza singola su Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

La scalabilità è solo una parte della storia. L'altra parte è latenza.  Per le macchine virtuali più piccole è possibile ottenere una velocità di I/O molto più elevata, ma è un'operazione da eseguire con una bassa latenza a singola cifra, come illustrato di seguito.  

* Il D4 ha guidato 26.000 IOPS rispetto Azure NetApp Files alla latenza 2,3-ms.  
* Il D8 ha guidato 51.000 IOPS rispetto Azure NetApp Files alla latenza 2,0-ms.  
* Il D16 ha guidato 88.000 IOPS rispetto Azure NetApp Files alla latenza 2,8-ms.
* Il D32 ha guidato 80.000 IOPS rispetto Azure NetApp Files alla latenza 2,4-ms.  

### <a name="s3b-per-instance-type-latency-results"></a>Risultati della latenza del tipo S3B per istanza

Il diagramma seguente mostra la latenza per SQL Server a istanza singola su Azure NetApp Files:

![Diagramma che mostra la latenza per SQL Server a istanza singola su Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>Strumento di test SSB 
 
Lo strumento di benchmark [TPC-E](http://www.tpc.org/tpce/) , per impostazione predefinita, mette in evidenza il *calcolo* anziché l' *archiviazione*. I risultati dei test illustrati in questa sezione si basano su uno strumento di test di stress denominato SQL Storage benchmark (SSB).  Il benchmark di archiviazione SQL Server può guidare un'esecuzione su larga scala di SQL in un database SQL Server per simulare un carico di lavoro OLTP, simile allo [strumento SLOB2 Oracle benchmarking](https://kevinclosson.net/slob/). 

Lo strumento SSB genera un carico di lavoro basato su selezione e aggiornamento che invia le istruzioni dette direttamente al database SQL Server in esecuzione nella macchina virtuale di Azure.  Per questo progetto, i carichi di lavoro SSB dilagano da 1 a 100 SQL Server utenti, con 10 o 12 punti intermedi a 15 minuti per numero di utenti.  Tutte le metriche delle prestazioni da queste esecuzioni sono state dal punto di vista di PerfMon, per la ripetibilità SSB è stata eseguita tre volte per ogni scenario. 

I test sono stati configurati come 80% SELECT e il 20% di istruzione UPDATE, quindi 90% lettura casuale.  Il database stesso, creato da SSB, ha dimensioni di 1000 GB. È costituito da 15 tabelle utente e 9 milioni righe per ogni tabella utente e 8192 byte per riga. 

Il benchmark SSB è uno strumento open source.  È disponibile gratuitamente nella pagina di [GitHub benchmark di archiviazione SQL](https://github.com/NetApp/SQL_Storage_Benchmark.git).  


## <a name="in-summary"></a>In Riepilogo  

Con Azure NetApp Files, è possibile aumentare le prestazioni di SQL Server riducendo significativamente il costo totale di proprietà. 

## <a name="next-steps"></a>Passaggi successivi

* [Creare un volume SMB per Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Architetture di soluzioni con Azure NetApp Files-SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

