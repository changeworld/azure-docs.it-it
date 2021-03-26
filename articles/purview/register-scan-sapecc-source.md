---
title: Registrare le analisi di origine e configurazione di SAP ECC (anteprima) in Azure
description: Questo articolo illustra come registrare l'origine SAP ECC in Azure e come configurare un'analisi.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: af3f54778882df9aaa06297f291c12a0f4b1577c
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046046"
---
# <a name="register-and-scan-sap-ecc-source-preview"></a>Registrare e analizzare l'origine SAP ECC (anteprima)

Questo articolo illustra come registrare un'origine SAP ECC in ambito di competenza e come configurare un'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

L'origine SAP ECC supporta l' **analisi completa** per estrarre i metadati da un'istanza di SAP ecc e recuperare la derivazione **tra gli** asset di dati.

## <a name="prerequisites"></a>Prerequisiti

1.  Configurare il runtime di [integrazione self-hosted](https://www.microsoft.com/download/details.aspx?id=39717)più recente.
    Per altre informazioni, vedere [creare e configurare un runtime di integrazione self-hosted](../data-factory/create-self-hosted-integration-runtime.md).

2.  Assicurarsi che [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) sia installato nella macchina virtuale in cui è installato il runtime di integrazione self-hosted.

3.  Assicurarsi che \" Visual C++ Redistributable 2012 Update 4 \" sia installato nel computer del runtime di integrazione self-hosted. Se non è \' ancora installato, scaricarlo da [qui](https://www.microsoft.com/download/details.aspx?id=30679).

4.  Scaricare il [connettore SAP a 64 bit per Microsoft .NET 3,0](https://support.sap.com/en/product/connectors/msnet.html) dal \' sito Web SAP s e installarlo nel computer del runtime di integrazione self-hosted. Durante l'installazione, assicurarsi di selezionare l'opzione **Installa assembly per GAC** nella finestra **passaggi di installazione facoltativi** .

    :::image type="content" source="media/register-scan-sapecc-source/requirement.png" alt-text="Prerequisiti" border="true":::

5.  Il connettore legge i metadati da SAP usando l'API del connettore Java (JCo) 3,0. Verificare quindi che il connettore Java sia disponibile nella macchina virtuale in cui è installato il runtime di integrazione self-hosted.
    Assicurarsi di usare la distribuzione JCo corretta per l'ambiente. Ad esempio, in un computer Microsoft Windows, assicurarsi che i file sapjco3. jar e sapjco3.dll siano disponibili.

    > [!Note] 
    > Il driver deve essere accessibile a tutti gli account nella VM. Non installarlo in un account utente.

6.  Distribuire il modulo di funzione ABAP di estrazione dei metadati nel server SAP attenendosi alla procedura descritta nella [Guida alla distribuzione di funzioni ABAP](abap-functions-deployment-guide.md). Per creare il modulo della funzione RFC nel server SAP, è necessario un account per sviluppatore ABAP. L'account utente richiede autorizzazioni sufficienti per connettersi al server SAP ed eseguire i moduli della funzione RFC seguenti:
    -   STFC_CONNECTION (verifica connettività)
    -   RFC_SYSTEM_INFO (verificare le informazioni di sistema)


## <a name="setting-up-authentication-for-a-scan"></a>Configurazione dell'autenticazione per un'analisi

L'unica autenticazione supportata per SAP ECC source è l' **autenticazione di base**.

## <a name="register-sap-ecc-source"></a>Registrare l'origine SAP ECC

Per registrare una nuova origine SAP ECC nel Catalogo dati, seguire questa procedura:

1.  Passare all'account di competenza.
2.  Selezionare **origini** nel percorso di spostamento a sinistra.
3.  Selezionare **Registra**
4.  In registra origini selezionare **SAP ecc**. Selezionare **continua.**

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc.png" alt-text="Registra opzioni di SAPECC" border="true":::

Nella schermata **Register Sources (SAP ecc)** eseguire le operazioni seguenti:

1.  Immettere un **nome** che l'origine dati sarà elencata nel catalogo.

2.  Immettere il nome del **server applicazioni** per connettersi all'origine SAP ecc.
    Può anche essere un indirizzo IP dell'host del server applicazioni SAP.

3.  Immettere il **numero del sistema** SAP. Si tratta di un numero intero a due cifre compreso tra 00 e 99.

4.  Selezionare una raccolta o crearne una nuova (facoltativo)

5.  Completare la registrazione dell'origine dati.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc-2.png" alt-text="Registra SAPECC" border="true":::

## <a name="creating-and-running-a-scan"></a>Creazione ed esecuzione di un'analisi

Per creare ed eseguire una nuova analisi, procedere come segue:

1.  Nel centro di gestione fare clic su Runtime di integrazione. Assicurasi che sia configurato un runtime di integrazione self-hosted. Se non è configurato, attenersi alla procedura descritta di [seguito](./manage-integration-runtimes.md) per creare un runtime di integrazione self-hosted.

2.  Passa a **origini**

3.  Selezionare l'origine SAP ECC registrata.

4.  Selezionare **+ Nuova analisi**

5.  Fornire i dettagli seguenti:

    a.  **Nome**: il nome dell'analisi

    b.  **Connetti tramite Integration Runtime**: selezionare il runtime di integrazione self-hosted configurato

    c.  **Credenziale**: selezionare le credenziali per la connessione all'origine dati. Verificare di:

    -   Selezionare autenticazione di base durante la creazione di una credenziale.
    -   Fornire un ID utente per la connessione al server SAP nel campo di input del nome utente.
    -   Archiviare la password utente usata per connettersi al server SAP nella chiave privata.

    d.  **ID client**: immettere l'ID client SAP. Si tratta di un numero numerico a tre cifre compreso tra 000 e 999.

    e.  **Percorso della libreria JCO**: percorso della directory in cui si trovano le librerie JCo

    f.  **Memoria massima disponibile:** Memoria massima (in GB) disponibile nella macchina virtuale del cliente da usare per l'analisi dei processi. Dipende dalle dimensioni dell'origine SAP ECC da analizzare.

    :::image type="content" source="media/register-scan-sapecc-source/scan-sapecc.png" alt-text="analizza SAPECC" border="true":::

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