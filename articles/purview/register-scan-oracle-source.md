---
title: Registrare l'origine Oracle e le analisi di configurazione (anteprima) in Azure Purview
description: Questo articolo descrive come registrare l'origine Oracle in Azure Purview e configurare un'analisi.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 40c5e0ff2c2301607f5a548ff05c742c5c5a948d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517063"
---
# <a name="register-and-scan-oracle-source-preview"></a>Registrare ed analizzare l'origine Oracle (anteprima)

Questo articolo descrive come registrare una data base Oracle in Purview e configurare un'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

L'origine Oracle supporta **l'analisi completa** per estrarre metadati da un database Oracle e recupera **la derivazione** tra asset di dati.

## <a name="prerequisites"></a>Prerequisiti

1.  Configurare il runtime di [integrazione self-hosted più recente.](https://www.microsoft.com/download/details.aspx?id=39717)
    Per altre informazioni, vedere [Creare e configurare un runtime di integrazione self-hosted.](../data-factory/create-self-hosted-integration-runtime.md)

2.  Assicurarsi che [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) sia installato nella macchina virtuale in cui è installato il runtime di integrazione self-hosted.

3.  Assicurarsi che Visual C++ Redistributable 2012 Update 4 sia installato nel computer del runtime di integrazione \" \" self-hosted. Se non \' è ancora installato, scaricarlo da [qui.](https://www.microsoft.com/download/details.aspx?id=30679)

4.  È necessario scaricare manualmente un driver Oracle [](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) JDBC da qui nella macchina virtuale in cui è in esecuzione il runtime di integrazione self-hosted.

    > [!Note] 
    > Il driver deve essere accessibile a tutti gli account nella VM. Non installarlo in un account utente.

5.  Le versioni di database Oracle supportate sono da 6i a 19c.

6.  Autorizzazione utente: è necessario un accesso di sola lettura alle tabelle di sistema. L'utente deve disporre dell'autorizzazione per creare una sessione e del ruolo SELECT \_ CATALOG \_ ROLE assegnato. In alternativa, l'utente può avere l'autorizzazione SELECT concessa per ogni singola tabella di sistema da cui il connettore esegue query sui metadati:
       > concedere la creazione di una sessione \[ \] all'utente ;\
        grant select on all \_ users to \[ user \] ;\
        grant select on dba \_ objects to \[ user \] ;\
        grant select on dba \_ tab \_ comments to \[ user \] ;\
        grant select on dba \_ external locations to user \_ \[ \] ;\
        grant select on dba \_ directories to \[ user \] ;\
        grant select on dba \_ mviews to \[ user \] ;\
        grant select on dba \_ clu \_ columns to user \[ \] ;\
        grant select on dba \_ tab columns to user \_ \[ \] ;\
        grant select on dba \_ col \_ comments to \[ user \] ;\
        grant select on dba \_ constraints to \[ user \] ;\
        grant select on dba \_ cons \_ columns to user \[ \] ;\
        grant select on dba \_ indexes to \[ user \] ;\
        grant select on dba \_ ind \_ columns to user \[ \] ;\
        grant select on dba \_ procedures to \[ user \] ;\
        grant select on dba \_ synonyms to \[ user \] ;\
        grant select on dba \_ views to \[ user \] ;\
        grant select on dba \_ source to \[ user \] ;\
        grant select on dba \_ triggers to \[ user \] ;\
        grant select on dba \_ arguments to \[ user \] ;\
        grant select on dba \_ sequences to \[ user \] ;\
        grant select on dba \_ dependencies to \[ user \] ;\
        grant select on V \_ \$ INSTANCE to user \[ \] ;\
        grant select on v \_ \$ database to user \[ \] ;
    
## <a name="setting-up-authentication-for-a-scan"></a>Configurazione dell'autenticazione per un'analisi

L'unica autenticazione supportata per un'origine Oracle è **l'autenticazione di base**.

## <a name="register-an-oracle-source"></a>Registrare un'origine Oracle

Per registrare una nuova origine Oracle nel catalogo dati, eseguire le operazioni seguenti:

1.  Passare all'account Purview.
2.  Selezionare **Origini** nel riquadro di spostamento a sinistra.
3.  Selezionare **Registra**
4.  In Registra origini selezionare **Oracle**. Selezionare **Continua**.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="registrare le origini" border="true":::

Nella schermata **Registra origini (Oracle)** eseguire le operazioni seguenti:

1.  Immettere un **nome per** indicare che l'origine dati verrà elencata all'interno del catalogo.

2.  Immettere il **nome host** per connettersi a un'origine Oracle. Può essere:
    - Nome host utilizzato da JDBC per connettersi al server di database. Ad esempio, MyDatabaseServer.com o
    - Un indirizzo IP. Ad esempio, 192.169.1.2 o
    - Stringa di connessione JDBC completa. Ad esempio, \
        jdbc:oracle:thin:@(DESCRIPTION=(LOAD \_ BALANCE=on)(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver1)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver2)(PORT=1521))(ADDRESS=(PROTOCOL=TCP)(HOST=oracleserver3)(PORT=1521))(CONNECT \_ DATA=(SERVICE \_ NAME=orcl))

3.  Immettere il **numero di porta** usato da JDBC per connettersi al server di database (1521 per impostazione predefinita per Oracle).

4.  Immettere il **nome del servizio Oracle** usato da JDBC per connettersi al server di database.

5.  Selezionare una raccolta o crearne una nuova (facoltativo)

6.  Completare la registrazione dell'origine dati.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="opzioni di registrazione delle origini" border="true":::

## <a name="creating-and-running-a-scan"></a>Creazione ed esecuzione di un'analisi

Per creare ed eseguire una nuova analisi, procedere come segue:

1.  Nel centro di gestione fare clic su Runtime di integrazione. Assicurasi che sia configurato un runtime di integrazione self-hosted. Se non è configurato, usare i passaggi indicati [qui](./manage-integration-runtimes.md) per creare un runtime di integrazione self-hosted.

2.  Passare a **Origini.**

3.  Selezionare l'origine Oracle registrata.

4.  Selezionare **+ Nuova analisi.**

5.  Fornire i dettagli seguenti:

    a.  **Nome:** nome dell'analisi

    b.  **Connetti tramite runtime di integrazione:** selezionare il runtime di integrazione self-hosted configurato

    c.  **Credenziali:** selezionare le credenziali per connettersi all'origine dati. Verificare di:
    - Selezionare Autenticazione di base durante la creazione di una credenziale.        
    - Specificare il nome utente usato da JDBC per connettersi al server di database nel campo di input Nome utente        
    - Archiviare la password utente usata da JDBC per connettersi al server di database nella chiave privata.

    d.  **Schema:** elenco di subset di schemi da importare espressi come elenco delimitato da punto e virgola. Ad esempio, schema1; schema2. Tutti gli schemi utente vengono importati se l'elenco è vuoto. Tutti gli schemi di sistema (ad esempio SysAdmin) e gli oggetti di sistema vengono ignorati per impostazione predefinita. Quando l'elenco è vuoto, vengono importati tutti gli schemi disponibili.
        I modelli di nomi di schema accettabili che usano la sintassi di espressioni LIKE SQL includono ad esempio l'uso di %. A%; %B; %C%; D
       -   inizia con A o        
       -   termina con B o        
       -   contiene C o        
       -   è uguale a D

    L'utilizzo di NOT e caratteri speciali non è accettabile.

6.  **Percorso driver:** specificare il percorso del driver JDBC nella macchina virtuale in cui è in esecuzione il runtime di integrazione self-host. Deve essere il percorso della cartella JAR valida.

7.  **Memoria massima disponibile:** memoria massima (in GB) disponibile nella macchina virtuale del cliente che deve essere usata dai processi di analisi. Ciò dipende dalle dimensioni dell'origine SAP S/4HANA da analizzare.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="oracle di analisi" border="true":::

8.  Fare clic su **Continua**.

9.  Scegliere il **trigger di analisi.** Si può configurare una pianificazione oppure eseguire l'analisi una sola volta.

10.  Esaminare l'analisi e fare clic **su Salva ed esegui**.

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