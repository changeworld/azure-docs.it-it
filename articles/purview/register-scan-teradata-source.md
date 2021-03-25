---
title: Registrare un'origine Teradata e le analisi del programma di installazione (anteprima) in Azure competenza
description: Questo articolo illustra come registrare un'origine Teradata in Azure Purview e configurare un'analisi.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 8f300f214ed36b7a5257b7276364027b91edc746
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048103"
---
# <a name="register-and-scan-teradata-source-preview"></a>Registrare e analizzare un'origine Teradata (anteprima)

Questo articolo illustra come registrare un'origine Teradata in Purview e configurare un'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

L'origine Teradata supporta l'**analisi completa** per estrarre i metadati da un database Teradata e recuperare la **derivazione** tra gli asset di dati.

## <a name="prerequisites"></a>Prerequisiti

1.  Configurare il runtime di [integrazione self-hosted](https://www.microsoft.com/download/details.aspx?id=39717)più recente.
    Per altre informazioni, vedere [creare e configurare un runtime di integrazione self-hosted](../data-factory/create-self-hosted-integration-runtime.md).

2.  Assicurarsi che [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) sia installato nella macchina virtuale in cui è installato il runtime di integrazione self-hosted.

3.  Assicurarsi che \" Visual C++ Redistributable 2012 Update 4 \" sia installato nel computer del runtime di integrazione self-hosted. Se non è \' ancora installato, scaricarlo da [qui](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Sarà necessario scaricare manualmente il driver JDBC di Teradata nella macchina virtuale in cui è in esecuzione il runtime di integrazione self-hosted.
    Il file JAR eseguibile può essere scaricato dal [sito Web](https://downloads.teradata.com/)di Teradata.

    > [!Note]
    > Il driver deve essere accessibile a tutti gli account nella VM. Non installarlo in un account utente.

5.  Sono supportate le versioni del database Teradata dalla 12.x alla 16.x. Assicurarsi di vere accesso in lettura all'origine Teradata da analizzare.

## <a name="setting-up-authentication-for-a-scan"></a>Configurazione dell'autenticazione per un'analisi

L'unica autenticazione supportata per un'origine Teradata è l' **autenticazione di base**.

## <a name="register-a-teradata-source"></a>Registrare un'origine Teradata

Per registrare una nuova origine Teradata nel catalogo dati, seguire questa procedura:

1.  Passare all'account di competenza.
2.  Selezionare **origini** nel percorso di spostamento a sinistra.
3.  Selezionare **Registra**
4.  In registra origini selezionare **Teradata**. Selezionare **Continua**

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Registra opzioni Teradata" border="true":::

Nella schermata **Register sources (Teradata)** (Registra origini - Teradata) seguire questa procedura:

1.  Immettere un **Nome** con il quale l'origine dati sarà elencata nel catalogo.

2.  Immettere il nome dell'**host** per la connessione a un'origine Teradata. Può essere anche un indirizzo IP o una stringa di connessione completa al server.

3.  Selezionare una raccolta o crearne una nuova (facoltativo)

4.  Completare la registrazione dell'origine dati.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="Registra Teradata" border="true":::

## <a name="creating-and-running-a-scan"></a>Creazione ed esecuzione di un'analisi

Per creare ed eseguire una nuova analisi, procedere come segue:

1.  Nel centro di gestione fare clic su **Runtime di integrazione**. Assicurasi che sia configurato un runtime di integrazione self-hosted. Se non è configurato, attenersi alla procedura descritta [qui](./manage-integration-runtimes.md) per configurare un runtime di integrazione self-hosted

2.  Passare a **Origini**

3.  Selezionare l'origine Teradata registrata.

4.  Selezionare **+ Nuova analisi**

5.  Fornire i dettagli seguenti:

    a.  **Nome**: il nome dell'analisi

    b.  **Connetti tramite Integration Runtime**: selezionare il runtime di integrazione self-hosted configurato.

    c.  **Credenziale**: selezionare le credenziali per la connessione all'origine dati. Verificare di:

    -   Selezionare autenticazione di base durante la creazione di una credenziale.
    -   Specificare un nome utente per la connessione al server di database nel campo di input del nome utente
    -   Archiviare la password del server di database nella chiave privata.

        Per ulteriori informazioni sulle credenziali, vedere il collegamento [qui](./manage-credentials.md)

6.  **Schema**: elenco subset di schemi da importare espressi come un elenco separato da punti e virgola. ad esempio, Schema1; Schema2. Se l'elenco è vuoto, verranno importati tutti gli schemi utente. Tutti gli schemi di sistema (ad esempio SysAdmin) e gli oggetti di sistema vengono ignorati per impostazione predefinita. Quando l'elenco è vuoto, vengono importati tutti gli schemi disponibili.

    I modelli di nomi di schema accettabili con la sintassi delle espressioni LIKE SQL includono %, ad esempio A%; %B; %C%; D
    - inizia con A o    
    - termina con B o    
    - contiene C o    
    - è uguale a D

    L'uso di NOT e di caratteri speciali non è consentito

7.  **Percorso driver**: specificare il percorso della posizione del driver JDBC nella macchina virtuale in cui è in esecuzione il runtime di integrazione self-hosted. Deve corrispondere al percorso della cartella JAR valida.

8.  **Memoria massima disponibile:** Memoria massima (in GB) disponibile nella macchina virtuale del cliente da usare per l'analisi dei processi. Questa quantità dipende dalle dimensioni dell'origine Teradata da analizzare.

    > [!Note] 
    > Come regola generale, fornire 2 GB di memoria ogni 1000 tabelle

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="configurazione dell'analisi" border="true":::

6.  Fare clic su **continua**.

7.  Scegliere il **trigger di analisi**. Si può configurare una pianificazione oppure eseguire l'analisi una sola volta.

8.  Esaminare l'analisi e fare clic su **Salva ed Esegui**.

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