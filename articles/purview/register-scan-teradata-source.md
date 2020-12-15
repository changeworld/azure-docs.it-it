---
title: Registrare un'origine Teradata e configurare le analisi (anteprima)
description: Questo articolo illustra come registrare un'origine Teradata in Azure Purview e configurare un'analisi.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841653"
---
# <a name="register-and-scan-teradata-source-preview"></a>Registrare e analizzare un'origine Teradata (anteprima)

Questo articolo illustra come registrare un'origine Teradata in Purview e configurare un'analisi.

> [!IMPORTANT]
> Questa origine dati è attualmente in anteprima. È possibile provarlo e inviare commenti e suggerimenti.

## <a name="supported-capabilities"></a>Funzionalità supportate

L'origine Teradata supporta l'**analisi completa** per estrarre i metadati da un database Teradata e recuperare la **derivazione** tra gli asset di dati.

## <a name="prerequisites"></a>Prerequisiti

- Il connettore supporta solo l'archivio dati che si trova all'interno di una rete locale, una rete virtuale di Azure o Amazon Virtual Private Cloud. Occorre pertanto configurare un [runtime di integrazione self-hosted](manage-integration-runtimes.md) per connettersi a questo connettore.

- Verificare che Java Runtime Environment (JRE) sia installato nella macchina virtuale in cui è installato il runtime di integrazione self-hosted.
 
- Verificare che "Visual C++ Redistributable 2012 Update 4" sia installato nella macchina virtuale in cui è installato il runtime di integrazione self-hosted. Se non è ancora installato, scaricarlo da [qui](https://www.microsoft.com/download/details.aspx?id=30679).

- Sarà necessario installare manualmente un driver denominato Teradata JDBC Driver nella macchina virtuale locale. Il file JAR eseguibile può essere scaricato dal [sito Web Teradata](https://downloads.teradata.com/).

    > [!Note] 
    > Il driver deve essere accessibile a tutti gli account nella VM. Non installarlo in un account utente.

- Sono supportate le versioni del database Teradata **dalla 12.x alla 16.x**. Assicurarsi di vere accesso in lettura all'origine Teradata da analizzare.

### <a name="feature-flag"></a>Flag di funzionalità

È possibile registrare e analizzare un'origine Teradata mediante un flag di funzionalità. Aggiungere il testo seguente alla fine dell'URL: *?feature.ext.datasource=%7b"teradata":"true"%7d* 

Ad esempio, URL completo [https://web.purview.azure.com/?feature.ext.datasource=%7b"metadata":"true"%7d](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)

## <a name="setting-up-authentication-for-a-scan"></a>Configurazione dell'autenticazione per un'analisi
L'unica modalità di autenticazione supportata per un'origine Teradata è l'autenticazione del **database di base**.

## <a name="register-a-teradata-source"></a>Registrare un'origine Teradata

Per registrare una nuova origine Teradata nel catalogo dati, seguire questa procedura:

1. Passare all'account Purview
2. Selezionare **Origini** nel riquadro di spostamento di sinistra
3. Selezionare **Registra**
4. In **Register sources** (Registra origini) selezionare **Teradata**
5. Selezionare **Continua**

Nella schermata **Register sources (Teradata)** (Registra origini - Teradata) seguire questa procedura:

1. Immettere un **Nome** con il quale l'origine dati sarà elencata nel catalogo.
2. Immettere il nome dell'**host** per la connessione a un'origine Teradata. Può essere anche un indirizzo IP o una stringa di connessione completa al server.
3. Selezionare una raccolta o crearne una nuova (facoltativo)
4. **Completare** la registrazione dell'origine dati.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="opzioni di registrazione delle origini" border="true":::

## <a name="creating-and-running-a-scan"></a>Creazione ed esecuzione di un'analisi

Per creare ed eseguire una nuova analisi, procedere come segue:
1. Nel centro di gestione fare clic su *Runtime di integrazione*. Assicurasi che sia configurato un runtime di integrazione self-hosted. Se non è configurato, usare la procedura descritta [qui](manage-integration-runtimes.md) per creare un runtime di integrazione self-hosted per l'analisi in una VM locale o di Azure che ha accesso alla rete locale.

2. Passare quindi a **Origini**

3. Selezionare l'origine Teradata registrata.

4. Selezionare + Nuova analisi
 
5. Fornire i dettagli seguenti:

    - Nome: nome dell'analisi

    - Connetti tramite runtime di integrazione: selezionare il runtime di integrazione self-hosted configurato

    - Metodo di autenticazione: Autenticazione database è l'unica opzione attualmente supportata. Sarà selezionata per impostazione predefinita

    - Nome utente: nome utente per la connessione al server di database. Deve avere accesso in lettura al server

    - Password: password dell'utente usata per la connessione al server di database

    - Schema: subset dell'elenco di schemi da importare espressamente come elenco delimitato da punti e virgola. ad esempio schema1; schema2. Se l'elenco è vuoto vengono importati tutti gli schemi utente. Tutti gli schemi di sistema (ad esempio SysAdmin) e gli oggetti di sistema vengono ignorati per impostazione predefinita.

        I modelli di nomi di schema accettabili con la sintassi delle espressioni LIKE SQL includono %, ad esempio A%; %B; %C%; D
        - inizia con A o    
        - termina con B o    
        - contiene C o    
        - è uguale a D

        L'uso di NOT e di caratteri speciali non è consentito

    - Driver location (Percorso driver): percorso completo del driver Teradata nella VM del cliente. Il nome del driver JDBC Teradata deve essere com.teradata.jdbc.TeraDriver

    - Maximum memory available (Memoria massima disponibile): quantità massima di memoria (in GB) disponibile nella VM del cliente che può essere usata dai processi di analisi. Questa quantità dipende dalle dimensioni dell'origine Teradata da analizzare.

    > [!Note] 
    > Come regola generale, fornire 2 GB di memoria ogni 1000 tabelle

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="configurazione dell'analisi" border="true":::

6. Fare clic su *Continua*.

7. Scegliere il trigger dell'analisi. Si può configurare una pianificazione oppure eseguire l'analisi una sola volta.

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="attivare l'analisi" border="true":::

8. Esaminare l'analisi e fare clic su *Salva ed esegui*.

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
