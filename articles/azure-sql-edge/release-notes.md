---
title: Note sulla versione di Azure SQL Edge
description: Note sulla versione in cui sono illustrate le novità o le modifiche apportate alle immagini Edge di Azure SQL.
keywords: Note sulla versione di SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: e078fb91b3279b6f4321cd51dfb094f82bbe5f14
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98696380"
---
# <a name="azure-sql-edge-release-notes"></a>Note sulla versione di Azure SQL Edge 

Questo articolo descrive le novità e le modifiche apportate a ogni nuova build di Azure SQL Edge.

## <a name="azure-sql-edge-102"></a>Azure SQL Edge 1.0.2

15.0.2000.1554 di compilazione del motore SQL

### <a name="fixes"></a>Correzioni

- Flusso T-SQL  
   - Correzione della proprietà e delle autorizzazioni per gli oggetti di streaming
   - Miglioramenti della registrazione con la rotazione del log e il prefisso del log
   - Analisi di flusso di Azure: miglioramenti della registrazione, miglioramento del codice di errore/messaggi di errore negli adapter 

- ONNX
    - Correzioni di bug per lo scenario di query parallele e gli errori di pulizia del modello
    - Aggiornamento del runtime di ONNX alla 1.5.1

## <a name="azure-sql-edge-101"></a>Azure SQL Edge 1.0.1

15.0.2000.1553 di compilazione del motore SQL

### <a name="whats-new"></a>Novità

- Consente di Date_Bucket espressioni definite nelle colonne calcolate.

### <a name="fixes"></a>Correzioni

- Correzione dei criteri di conservazione per eliminare una tabella con un criterio di conservazione abilitato con un timeout infinito
- Supporto della distribuzione di DacFx per le funzionalità di streaming e le funzionalità dei criteri di conservazione 
- Correzione della distribuzione di DacFx per abilitare la distribuzione da una cartella annidata in un URL SAS 
- STIMA della correzione per supportare nomi di colonna lunghi nei messaggi di errore

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM)

15.0.2000.1552 di compilazione del motore SQL

### <a name="whats-new"></a>Novità
- Immagini del contenitore basate su Ubuntu 18,04 
- Supporto della `IGNORE NULL` `RESPECT NULL` sintassi and con `LAST_VALUE()` le `FIRST_VALUE()` funzioni e 
- Miglioramenti dell'affidabilità per la stima con ONNX
- Supporto per la pulizia in base ai criteri di conservazione dei dati:
   - Supporto del buffer circolare per un'attività di pulizia della conservazione per la risoluzione dei problemi
- Supporto per nuove funzionalità: 
   - Recupero rapido
   - Ottimizzazione automatica delle query
   - Scenari di esecuzione parallela
- Miglioramenti del risparmio energetico per la modalità a basso consumo
- Supporto delle nuove funzionalità di streaming: 
   - [Finestre snapshot](/stream-analytics-query/snapshot-window-azure-stream-analytics): un nuovo tipo di finestra consente di raggruppare gli eventi che arrivano nello stesso momento.
   - [Topone](/stream-analytics-query/topone-azure-stream-analytics) e [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) possono essere abilitati come funzioni analitiche. È possibile restituire i record ordinati in base alla colonna scelta. Non è necessario che facciano parte di una finestra. 
   - Miglioramenti apportati a [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Correzioni
- Messaggi di errore aggiuntivi e dettagli per la risoluzione dei problemi relativi alle operazioni di streaming T-SQL 
- Miglioramenti per la conservazione della durata della batteria in modalità inattiva 
- Correzioni del motore di flusso T-SQL: 
   - Pulizia per i processi di streaming interrotti 
   - Correzioni per la localizzazione 
   - Gestione Unicode migliorata 
   - Debug migliorato per lo streaming T-SQL Edge SQL, che consente agli utenti di eseguire query sugli errori di errore del processo da get_streaming_job
- Pulizia basata sui criteri di conservazione dei dati: 
    - Correzioni per gli scenari di creazione e pulizia dei criteri di conservazione
- Correzioni per le attività del timer in background per migliorare il risparmio energetico per la modalità a basso consumo

### <a name="known-issues"></a>Problemi noti 
- Impossibile utilizzare la funzione T-SQL Date_Bucket in una colonna calcolata.


## <a name="ctp-23"></a>CTP 2.3
15.0.2000.1549 di compilazione del motore SQL
### <a name="whats-new"></a>Novità
- Supporto per le origini personalizzate nella funzione Date_Bucket () 
- Supporto per i file BACPAC come parte della distribuzione SQL
- Supporto per la pulizia in base ai criteri di conservazione dei dati:      
   - Supporto DDL per l'abilitazione dei criteri di conservazione 
   - Pulizia per stored procedure e attività di pulizia in background
   - Eventi estesi per il monitoraggio delle attività di pulizia

### <a name="fixes"></a>Correzioni
- Messaggi di errore aggiuntivi e dettagli per la risoluzione dei problemi relativi alle operazioni di streaming T-SQL 
- Miglioramenti per la conservazione della durata della batteria in modalità inattiva 
- Motore di streaming T-SQL: 
   - Correzione per la filigrana bloccata nella finestra di salto in flusso 
   - Correzione della gestione delle eccezioni del Framework per assicurarsi che venga raccolto come errore eseguibile dall'utente


## <a name="ctp-22"></a>CTP 2.2
15.0.2000.1546 di compilazione del motore SQL
### <a name="whats-new"></a>Novità
- Supporto per i contenitori non radice 
- Supporto per la raccolta di dati di utilizzo e diagnostica 
- Aggiornamenti di streaming T-SQL:
   - Supporto per i caratteri Unicode per i nomi di oggetti di flusso

### <a name="fixes"></a>Correzioni
- Aggiornamenti di streaming T-SQL:
   - Miglioramenti alla pulizia del processo
   - Miglioramenti della registrazione e della diagnostica
- Miglioramento delle prestazioni per l'inserimento dei dati

## <a name="ctp-21"></a>CTP 2.1 
15.0.2000.1545 di compilazione del motore SQL
### <a name="fixes"></a>Correzioni
- Consentire ai modelli PREDICT-with-ONNX di gestire un problema di CPUID in ARM 
- Gestione migliorata del percorso di errore all'avvio dello streaming T-SQL
- Correzione del valore del ritardo della filigrana nelle metriche del processo quando non sono presenti dati.
- Correzione di un problema con l'adattatore di output quando l'adapter dispone di uno schema variabile tra i batch  

## <a name="ctp-20"></a>CTP 2.0 
15.0.2000.1401 di compilazione del motore SQL
### <a name="whats-new"></a>Novità
-   Nome prodotto aggiornato ad *Azure SQL Edge*
-  Date_Bucket funzione:
    - Supporto per i tipi date, Time e DateTime
- STIMA con ONNX:
    - Requisito ONNX per il parametro RUNTIME  
- Supporto dello streaming T-SQL (anteprima limitata) 
 
### <a name="known-issues"></a>Problemi noti

- Problema: potenziali errori con l'applicazione di DACPAC all'avvio a causa di un problema di temporizzazione.
- Soluzione temporanea: riavviare SQL Server. In caso contrario, il contenitore tenterà di applicare DACPAC.

### <a name="request-support"></a>Richiedere supporto
È possibile richiedere supporto nella [pagina supporto](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Selezionare i campi seguenti: 
- **Tipo di problema**: *Tecnico* 
- **Servizio**: *IOT Edge*
- **Tipo di problema**: *il problema riguarda un modulo IOT Edge*
- **Sottotipo di problema**: *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="Screenshot che mostra un ticket di supporto di esempio.":::

## <a name="ctp-15"></a>CTP 1,5
15.0.2000.1331 di compilazione del motore SQL
### <a name="whats-new"></a>Novità
- Date_Bucket funzione:
    - Supporto per il tipo DateTimeOffset
- PREVEDERE con i modelli ONNX:
  - Supporto NVARCHAR
 
## <a name="ctp-14"></a>CTP 1,4
15.0.2000.1247 di compilazione del motore SQL
### <a name="whats-new"></a>Novità
-   PREVEDERE con i modelli ONNX:
    - Supporto VARCHAR
    - Migrazione a ONNX Runtime versione 1,0 

- Sono abilitate le funzionalità seguenti:
  - Supporto CDC
  - Tabella di cronologia con compressione
  - Un fattore di scala maggiore per il log Read-Ahead
  - Distribuzione di modalità batch ES filtro
  - Ottimizzazioni Read-Ahead
 
## <a name="ctp-13"></a>CTP 1,3
15.0.2000.1147 di compilazione del motore SQL
### <a name="whats-new"></a>Novità
- Distribuzione del portale di Azure: 
    - Supporto per la distribuzione di immagini AMD64 e ARM
    - Supporto per la creazione di processi di streaming
    - Distribuzione di DACPAC
- PREVEDERE con i modelli ONNX:
    - Supporto per tipi numerici
- Sono abilitate le funzionalità seguenti:
    - Distribuzione aggregazione all'analisi dell'archivio colonne
    - Analisi di buon passo
- Lavoro di riduzione dell'utilizzo della memoria e del footprint
