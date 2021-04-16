---
title: Domande frequenti - Monitoraggio di Azure per soluzioni SAP | Microsoft Docs
description: Questo articolo contiene le risposte alle domande frequenti sulle Monitoraggio di Azure per le soluzioni SAP.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 4b84f07f637b0a8925dec96c8c609101247ffd64
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377125"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Monitoraggio di Azure domande frequenti sulle soluzioni SAP (anteprima)
## <a name="frequently-asked-questions"></a>Domande frequenti

Questo articolo fornisce le risposte alle domande frequenti sulle Monitoraggio di Azure per le soluzioni SAP.  

 - **È necessario pagare per Monitoraggio di Azure per soluzioni SAP?**  
Non sono presenti costi di licenza per Monitoraggio di Azure per soluzioni SAP.  
Tuttavia, i clienti sono responsabili di sostenere il costo dei componenti del gruppo di risorse gestite.  

 - **In quali aree è disponibile questo servizio per l'anteprima pubblica?**  
Per l'anteprima pubblica, questo servizio sarà disponibile negli Stati Uniti orientali 2, negli Stati Uniti occidentali 2, negli Stati Uniti orientali e in Europa occidentale.  

 - **È necessario fornire le autorizzazioni per consentire la distribuzione del gruppo di risorse gestite nella sottoscrizione?**  
No, le autorizzazioni esplicite non sono necessarie.  

 - **Dove si trova la macchina virtuale dell'agente di raccolta?**  
Al momento della distribuzione di una Monitoraggio di Azure per soluzioni SAP, è consigliabile che i clienti scentino la stessa rete virtuale per il monitoraggio delle risorse SAP HANA server. È quindi consigliabile che la macchina virtuale dell'agente di raccolta risieda nella stessa rete virtuale SAP HANA server. Se i clienti usano un database non HANA, la macchina virtuale dell'agente di raccolta risiederà nella stessa rete virtuale del database non HANA.  

 - **Quali versioni di HANA sono supportate?**  
HANA 1.0 SPS 12 (versione successiva 120) e HANA 2.0 SPS03 o versione successiva  

 - **Quali configurazioni di distribuzione HANA sono supportate?**  
Le configurazioni seguenti sono supportate:
   - Nodo singolo (scalabilità verticale) e più nodi (scale-out)  
   - Contenitore di database singolo (HANA 1.0 SPS 12) e contenitori di database multipli (HANA 1.0 SPS 12 o HANA 2.0)  
   - Failover automatico dell'host (n+1) e HSR  

 - **Quali SQL Server sono supportate?**  
SQL Server 2012 SP4 o versione successiva.  

 - **Quali SQL Server sono supportate?**  
Le configurazioni seguenti sono supportate:
   - Istanze autonome predefinite o denominate in una macchina virtuale  
   - Istanze del cluster o istanze in una configurazione AlwaysOn quando si usa il nome virtuale della risorsa cluster o il nome del listener AlwaysOn. Attualmente non vengono raccolte metriche specifiche del cluster o AlwaysOn    
   - database SQL di Azure (PAAS) non è attualmente supportato  

 - **Cosa accade se si elimina accidentalmente il gruppo di risorse gestite?**  
Il gruppo di risorse gestite è bloccato per impostazione predefinita. Di conseguenza, le probabilità di eliminazione accidentale del gruppo di risorse gestite da parte dei clienti sono minuscole.  
Se un cliente elimina il gruppo di risorse gestite, Monitoraggio di Azure per soluzioni SAP smetterà di funzionare. Il cliente dovrà distribuire una nuova risorsa Monitoraggio di Azure per soluzioni SAP e ricominciare.  

 - **Quali ruoli sono necessari nella sottoscrizione di Azure per distribuire Monitoraggio di Azure per soluzioni SAP risorsa?**  
Ruolo collaboratore.  

 - **Qual è il contratto di servizio per questo prodotto?**  
Le anteprime sono escluse dai contratti di servizio. Leggere il termine di licenza completo tramite l'immagine Monitoraggio di Azure per soluzioni SAP marketplace.  

 - **È possibile monitorare l'intero panorama tramite questa soluzione?**  
È attualmente possibile monitorare il database HANA, l'infrastruttura sottostante, il cluster a disponibilità elevata, Microsoft SQL Server, la disponibilità di SAP Netweaver e le metriche di disponibilità dell'istanza dell'applicazione SAP in anteprima pubblica.  

 - **Questo servizio sostituisce SAP Solution Manager?**  
No. I clienti possono comunque usare SAP Solution Manager per il monitoraggio dei processi aziendali.  

 - **Qual è il valore di questo servizio rispetto alle soluzioni tradizionali come SAP HANA o Studio?**  
Monitoraggio di Azure per soluzioni SAP non è specifico del database HANA. Monitoraggio di Azure per soluzioni SAP supporta anche AnyDB.  

- **Quali versioni di SAP NetWeaver sono supportate?**  
SAP NetWeaver 7.0 o versione successiva.  

- **Quali configurazioni di SAP NetWeaver sono supportate?**  
Supporta le configurazioni ABAP, Java e del server applicazioni SAP NetWeaver a doppio stack.

- **Perché è necessario rimuovere la protezione dei metodi per il monitoraggio delle applicazioni SAP NetWeaver?**  
Nelle versioni SAP >= 7.3, la maggior parte dei metodi del servizio Web è protetta per impostazione predefinita. Per recuperare le metriche di disponibilità e prestazioni chiamando questi metodi, è necessario rimuovere la protezione dai metodi seguenti: GetQueueStatistic, ABAPGetWPTable, GetProcessList, EnqGetStatistic e GetSystemInstancelist.

- **Esiste un rischio per la rimozione della protezione dei metodi Web SAPCONTROL?**  
In generale, la rimozione della protezione dei metodi [](https://launchpad.support.sap.com/#/notes/1439348)Web SAPCONTROL non costituisce un rischio per la sicurezza. Tuttavia, se i clienti vogliono limitare o impedire l'accesso ai metodi Web non protetti tramite le porte server (5XX13/5XX14) di sapstartsrv, è possibile farlo aggiungendo un filtro nell'elenco di controllo di accesso (ACL) sap, la nota [OSS](https://service.sap.com/sap/support/notes/1495075) descrive la configurazione necessaria per ottenere questo risultato. 

- **È necessario riavviare le istanze di SAP dopo aver eseguito le configurazioni di sistema per la configurazione del provider SAP NetWeaver?**  
Sì, dopo aver creato metodi non protetti tramite modifiche di configurazione SAP, sarà necessario riavviare i rispettivi sistemi SAP per assicurarsi che le modifiche alla configurazione siano aggiornate.  

## <a name="next-steps"></a>Passaggi successivi

- Creare la prima risorsa Monitoraggio di Azure per soluzioni SAP.
