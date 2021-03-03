---
title: Registrare analisi di origine e configurazione Oracle (anteprima) in Azure
description: Questo articolo illustra come registrare l'origine Oracle in Azure e come configurare un'analisi.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 3ae81a9c05f0c88a52db6409bf870ee0427f09db
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101696171"
---
# <a name="register-and-scan-oracle-source-preview"></a>Registrare e analizzare l'origine Oracle (anteprima)

Questo articolo illustra come registrare un database Oracle in ambito di competenza e come configurare un'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

L'origine Oracle supporta l' **analisi completa** per estrarre i metadati da un database Oracle e recuperare la derivazione **tra gli** asset di dati.

## <a name="prerequisites"></a>Prerequisiti

1.  Configurare il runtime di [integrazione self-hosted](https://www.microsoft.com/download/details.aspx?id=39717)più recente.
    Per altre informazioni, vedere [creare e configurare un runtime di integrazione self-hosted](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).

2.  Assicurarsi che [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) sia installato nella macchina virtuale in cui è installato il runtime di integrazione self-hosted.

3.  Assicurarsi che \" Visual C++ Redistributable 2012 Update 4 \" sia installato nel computer del runtime di integrazione self-hosted. Se non è \' ancora installato, scaricarlo da [qui](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Sarà necessario scaricare manualmente un driver JDBC Oracle denominato ' Oracle. JDBC. driver. OracleDriver \' nella macchina virtuale in cui è in esecuzione il runtime di integrazione self-hosted.

    > [!Note] 
    > Il driver deve essere accessibile a tutti gli account nella VM. Non installarlo in un account utente.

5.  Le versioni supportate del database Oracle sono 6I in 19C.

6.  Autorizzazione utente: per garantire una corretta analisi per la prima volta, è richiesta l'autorizzazione completa per il tipo di amministratore sys.

    Per le analisi successive, è necessario un accesso in sola lettura alle tabelle di sistema. L'utente deve disporre dell'autorizzazione per la creazione di una sessione e del ruolo selezionare il \_ ruolo del catalogo \_ assegnato. In alternativa, è possibile che all'utente sia concessa l'autorizzazione SELECT per ogni singola tabella di sistema da cui questo connettore esegue una query sui metadati:
       > Concedi creazione sessione all' \[ utente \] ; \
        Concedi selezione su tutti \_ gli utenti all' \[ utente \] ; \
        Concedi selezione su \_ oggetti DBA all' \[ utente \] ; \
        Concedi \_ commenti alla scheda DBA seleziona \_ per l' \[ utente \] ; \
        Concedi selezione in \_ percorsi esterni DBA \_ all' \[ utente \] ; \
        Concedi selezione nelle \_ directory DBA all' \[ utente \] ; \
        Concedi SELECT su DBA \_ mviews all' \[ utente \] ; \
        Concedi SELECT su DBA \_ CLU \_ colonne all' \[ utente \] ; \
        Concedi selezione sulle \_ colonne della scheda DBA \_ all' \[ utente \] ; \
        Concedi all'utente i commenti SELECT su DBA \_ \_ \[ \] ; \
        Concedi \_ all'utente i vincoli SELECT per DBA \[ \] ; \
        concedere le colonne SELECT su DBA \_ const \_ all' \[ utente \] ; \
        Concedi selezione sugli \_ indici DBA all' \[ utente \] ; \
        Concedi SELECT su DBA \_ IND \_ colonne all' \[ utente \] ; \
        Concedi \_ all'utente le procedure SELECT per DBA \[ \] ; \
        Concedi \_ all'utente i sinonimi SELECT su DBA \[ \] ; \
        Concedi \_ all'utente le visualizzazioni SELECT per DBA \[ \] ; \
        Concedi selezione nell' \_ origine DBA all' \[ utente \] ; \
        Concedi selezione sui \_ trigger DBA all' \[ utente \] ; \
        Concedi gli argomenti SELECT \_ per DBA all' \[ utente \] ; \
        Concedi selezione in \_ sequenza DBA all' \[ utente \] ; \
        Concedi la selezione \_ per le dipendenze DBA all' \[ utente \] ; \
        Concedi selezione nell' \_ \$ istanza V all' \[ utente \] ; \
        Concedi Select on v \_ \$ database to \[ User \] ;
    
## <a name="setting-up-authentication-for-a-scan"></a>Configurazione dell'autenticazione per un'analisi

L'unica autenticazione supportata per un'origine Oracle è l' **autenticazione di base**.

## <a name="register-an-oracle-source"></a>Registrare un'origine Oracle

Per registrare una nuova origine Oracle nel Catalogo dati, eseguire le operazioni seguenti:

1.  Passare all'account di competenza.
2.  Selezionare **origini** nel percorso di spostamento a sinistra.
3.  Selezionare **Registra**
4.  In registra origini selezionare **Oracle**. Selezionare **Continua**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="Registra origini" border="true":::

Nella schermata **registra origini (Oracle)** eseguire le operazioni seguenti:

1.  Immettere un **nome** che l'origine dati sarà elencata nel catalogo.

2.  Immettere il nome **host** per la connessione a un'origine Oracle. Può essere:
    - Nome host utilizzato da JDBC per connettersi al server di database. Ad esempio, MyDatabaseServer.com o
    - Un indirizzo IP. Ad esempio, 192.169.1.2 o
    - Stringa di connessione JDBC completo. Ad esempio, \
        JDBC: Oracle: thin: @ (descrizione = (LOAD \_ balance = on) (Address = (Protocol = TCP) (host = OracleServer1) (Port = 1521)) (Address = (Protocol = TCP) (host = oracleserver2) (Port = 1521)) (Address = (Protocol = TCP) (host = oracleserver3) (Port = 1521)) (Connect \_ Data = ( \_ nome servizio = ORCL)))

3.  Immettere il **numero di porta** utilizzato da JDBC per connettersi al server di database (1521 per impostazione predefinita per Oracle).

4.  Immettere il **nome del servizio Oracle** utilizzato da JDBC per connettersi al server di database.

5.  Selezionare una raccolta o crearne una nuova (facoltativo)

6.  Completare la registrazione dell'origine dati.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="opzioni di registrazione delle origini" border="true":::

## <a name="creating-and-running-a-scan"></a>Creazione ed esecuzione di un'analisi

Per creare ed eseguire una nuova analisi, procedere come segue:

1.  Nel centro di gestione fare clic su Runtime di integrazione. Assicurasi che sia configurato un runtime di integrazione self-hosted. Se non è configurato, attenersi alla procedura descritta di [seguito](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) per creare un runtime di integrazione self-hosted.

2.  Passare a **origini**.

3.  Selezionare l'origine Oracle registrata.

4.  Selezionare **+ nuova analisi**.

5.  Fornire i dettagli seguenti:

    a.  **Nome**: il nome dell'analisi

    b.  **Connetti tramite Integration Runtime**: selezionare il runtime di integrazione self-hosted configurato

    c.  **Credenziale**: selezionare le credenziali per la connessione all'origine dati. Verificare di:
    - Selezionare autenticazione di base durante la creazione di una credenziale.        
    - Specificare il nome utente utilizzato da JDBC per connettersi al server di database nel campo di input del nome utente        
    - Archiviare la password utente utilizzata da JDBC per connettersi al server di database nella chiave privata.

    d.  **Schema**: elenco subset di schemi da importare espressi come un elenco separato da punti e virgola. Ad esempio, Schema1; Schema2. Se l'elenco è vuoto, verranno importati tutti gli schemi utente. Tutti gli schemi di sistema (ad esempio SysAdmin) e gli oggetti di sistema vengono ignorati per impostazione predefinita. Quando l'elenco è vuoto, vengono importati tutti gli schemi disponibili.
        I modelli di nomi di schema accettabili utilizzando la sintassi delle espressioni LIKE SQL includono, ad esempio, using%. %; B % C%; D
       -   inizia con A o        
       -   termina con B o        
       -   contiene C o        
       -   è uguale a D

    L'utilizzo di caratteri non e speciali non è accettabile.

6.  **Percorso driver**: specificare il percorso della posizione del driver JDBC nella macchina virtuale in cui è in esecuzione il runtime di integrazione self-hosted. Deve corrispondere al percorso della cartella JAR valida.

7.  **Memoria massima disponibile**: memoria massima (in GB) disponibile nella macchina virtuale del cliente da usare per l'analisi dei processi. Questo dipende dalle dimensioni dell'origine SAP S/4HANA da analizzare.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="analizza Oracle" border="true":::

8.  Fare clic su **continua**.

9.  Scegliere il **trigger di analisi**. Si può configurare una pianificazione oppure eseguire l'analisi una sola volta.

10.  Esaminare l'analisi e fare clic su **Salva ed Esegui**.

## <a name="viewing-your-scans-and-scan-runs"></a>Visualizzazione delle analisi e delle relative esecuzioni

1. Passare al centro di gestione. Selezionare **Origini dati** nella sezione **Sources and scanning** (Origini e analisi).

2. Selezionare l'origine dati desiderata. Verrà visualizzato un elenco delle analisi esistenti per tale origine dati.

3. Selezionare l'analisi di cui visualizzare i risultati.

4. Questa pagina mostra tutte le analisi eseguite in precedenza insieme alle metriche e allo stato di ognuna. Visualizza anche se l'analisi è stata pianificata o avviata manualmente, il numero di asset con classificazioni applicate, il numero totale di asset individuati, le ore di inizio e di fine dell'analisi e la durata totale.

## <a name="manage-your-scans"></a>Gestire le analisi

Per gestire o eliminare un'analisi, procedere come segue:

1. Passare al centro di gestione. Selezionare **Origini dati** nella sezione **Sources and scanning** (Origini e analisi), quindi selezionare l'origine dati desiderata.

2. Selezionare l'analisi da gestire. Per modificare l'analisi, selezionare **Modifica**.

3. Per eliminare l'analisi, selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)