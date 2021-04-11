---
title: Informazioni di riferimento sulle impostazioni di configurazione di Azure HDInsight
description: Introdurre la configurazione dell'estensione HDInsight di Azure.
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-python
ms.openlocfilehash: a9a444c2557ea17114123cbd5084cbbd9fe6dac6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259995"
---
# <a name="azure-hdinsight-configuration-settings-reference"></a>Informazioni di riferimento sulle impostazioni di configurazione di Azure HDInsight

L'estensione Spark & hive Tools per Visual Studio Code è altamente configurabile. Questa pagina descrive le impostazioni principali che è possibile usare.  

Per informazioni generali sull'uso delle impostazioni in VS Code, vedere [impostazioni dell'area](https://code.visualstudio.com/docs/getstarted/settings)di lavoro e dell'utente e le informazioni di [riferimento sulle variabili](https://code.visualstudio.com/docs/editor/variables-reference) per informazioni sul supporto delle variabili predefinite.

## <a name="open-the-azure-hdinsight-configuration"></a>Aprire la configurazione di Azure HDInsight

1. Aprire prima di tutto una cartella per creare le impostazioni dell'area di lavoro.
2. Premere **CTRL + MAIUSC + P** oppure **passare a Visualizza**  ->  **riquadro comandi...** per visualizzare tutti i comandi.
3. **Configurazione del set** di ricerca.
4. Espandere **estensioni** nella directory sinistra e selezionare **configurazione HDInsight**.

 ![immagine di configurazione HDI](./media/HDInsight-config-for-vscode/HDInsight-config-for-vscode.png)

## <a name="general-settings"></a>Impostazioni generali   

|  Proprietà   | Predefinito | Descrizione   |
| ----- | ----- |----- |
| HDInsight: ambiente di Azure | Azure | Ambiente Azure |
| HDInsight: disabilitare il collegamento al sondaggio aperto | Selezionata | Abilita/Disabilita l'apertura del sondaggio HDInsight |
| HDInsight: Abilita l'installazione di Skip Pyspark | Non selezionato | Abilita/Disabilita l'installazione di pyspark ignorata |
| HDInsight: Abilita suggerimenti per l'accesso | Non selezionato | Quando questa opzione è selezionata, verrà richiesto di eseguire l'accesso ad Azure |
| HDInsight: versione precedente dell'estensione | Visualizza il numero di versione dell'estensione corrente | Mostra la versione precedente dell'estensione|
| HDInsight: famiglia di caratteri risultati | -Apple-System, BlinkMacSystemFont, Segoe WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, Droid sans, sans-serif | Imposta la famiglia di caratteri per la griglia dei risultati; imposta su blank per usare il tipo di carattere dell'editor |
| HDInsight: dimensioni carattere risultati | 13 |Impostare la dimensione del carattere per i risultati griglia; imposta su blank per usare le dimensioni dell'editor |
| Cluster HDInsight: cluster collegato | -- | URL di cluster collegati. Può anche modificare il file JSON da impostare |
| Hive HDInsight: applica localizzazione | Non selezionato | Opzionale Opzioni di configurazione per la localizzazione nelle impostazioni locali configurate di VSCode (è necessario riavviare VSCode per rendere effettive le impostazioni)|
| HDInsight hive: copiare le intestazioni di inclusione | Non selezionato | Opzionale Opzione di configurazione per la copia dei risultati dalla visualizzazione dei risultati |
| HDInsight hive: copia Rimuovi nuova riga | Selezionata | Opzionale Opzioni di configurazione per la copia di risultati a più righe dalla visualizzazione dei risultati |
| HDInsight hive › Format: allinea le definizioni di colonna nelle colonne | Non selezionato | La definizione della colonna deve essere allineata |
| HDInsight hive › Format: tipo di dati maiuscole | Nessuno | I tipi di dati devono essere formattati in MAIUSCOLo, minuscolo o nessuno (non formattato) |
| HDInsight hive › formato: combinazione di maiuscole/minuscole | Nessuno | Le parole chiave devono essere formattate in MAIUSCOLo, minuscolo o nessuno (non formattato) |
| HDInsight hive › Format: posizionare le virgole prima della successiva istruzione | Non selezionato | Le virgole devono essere posizionate all'inizio di ogni istruzione in un elenco, ad esempio ', mycolumn2' anziché alla fine ' mycolumn1'|
| HDInsight hive › Format: Inserisci riferimenti a istruzioni SELECT in una nuova riga | Non selezionato | I riferimenti a oggetti in un'istruzione SELECT devono essere suddivisi in righe separate? Per ' SELECT C1, C2 FROM T1', ad esempio, sia C1 che C2 si troveranno su righe separate
| HDInsight hive: informazioni di debug del log | Non selezionato | Opzionale Registra l'output di debug nella console di VS Code (Help-> attiva/Nascondi Strumenti di sviluppo) 
| HDInsight hive: messaggi aperti predefiniti | Selezionata | True perché il riquadro messaggi sia aperto per impostazione predefinita; false per chiuso|
| HDInsight hive: gruppo di caratteri risultati | -Apple-System, BlinkMacSystemFont, Segoe WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, Droid sans, sans-serif | Imposta la famiglia di caratteri per la griglia dei risultati; imposta su blank per usare il tipo di carattere dell'editor |
| HDInsight hive: dimensioni dei tipi di carattere | 13 | Impostare la dimensione del carattere per la griglia dei risultati; imposta su blank per usare le dimensioni dell'editor |
| HDInsight hive › Salva come CSV: Includi intestazioni | Selezionata | Opzionale Se true, le intestazioni di colonna vengono incluse quando si salvano i risultati in formato CSV |
| HDInsight hive: collegamenti | -- | Collegamenti correlati alla finestra risultati |
| HDInsight hive: Mostra ora batch| Non selezionato | Opzionale Il tempo di esecuzione deve essere visualizzato per i singoli batch |
| HDInsight hive: Selezione riquadro Dividi | Avanti | Opzionale Opzioni di configurazione per le quali devono essere aperti i riquadri dei risultati nuovi nei |
| Invio del processo HDInsight: conf del cluster | -- | Configurazione cluster |
| Invio del processo HDInsight: Livio conf | -- | Configurazione di Tito. POST/batch |
| HDInsight Jupyter: Accodamento risultati| Selezionata | Indica se accodare i risultati alla finestra risultati, altrimenti deselezionare e visualizzare. |
| HDInsight Jupyter: lingue | -- | Impostazioni predefinite per lingua. |
| HDInsight Jupyter › log: Verbose | Non selezionato | Se Abilita registrazione dettagliata |
| HDInsight Jupyter › notebook: argomenti di avvio | È possibile aggiungere un elemento | argomenti della riga di comando ' jupyter notebook '. Ogni argomento è un elemento separato nella matrice. Per un elenco completo, digitare "jupyter notebook--Help" in una finestra del terminale. |
| HDInsight Jupyter › notebook: cartella di avvio | $ {workspaceRoot} |-- |
| HDInsight Jupyter: estensione Python abilitata | Selezionata | Usare Python-Interactive-Window dell'estensione MS-Python quando si inviano processi interattivi pySpark. In caso contrario, usare la finestra jupyter personalizzata |
| HDInsight Spark.NET: 7z | C:\Programmi\Microsoft Files\7-Zip | Percorso <7z.exe> |
| HDInsight Spark.NET: HADOOP_HOME | D:\winutils | <percorso bin\winutils.exe solo> sistema operativo Windows |
| HDInsight Spark.NET: JAVA_HOME | C:\Programmi\Microsoft Files\Java\ jdk1.8.0_201 \ | Percorso della Home page di Java|
| HDInsight Spark.NET: SCALA_HOME | C:\Programmi (x86) \scala\ | Percorso della Home page di scala |
| HDInsight Spark.NET: SPARK_HOME | D:\spark-2.3.3-bin-hadoop2.7\ | Percorso della Home page di Spark |
| Hive: Mantieni le schede risultati query | Non selezionato | PersistQueryResultTabs hive |
| Hive: Selezione riquadro Dividi | Avanti | Opzionale Opzioni di configurazione per le quali devono essere aperti i riquadri dei risultati nuovi nei |
| Hive Interactive: copia cartella eseguibile | Non selezionato | Se copiare la cartella di runtime del servizio interattivo hive nella cartella tmp dell'utente |
| Server interattivo HQL: porta wrapper | 13424 | Porta del servizio interattivo hive |
| Server di linguaggio HQL: porta wrapper del linguaggio | 12342 | Server di porta del servizio di linguaggio hive in ascolto. |
| Server di linguaggio HQL: numero massimo di problemi | 100 | Controlla il numero massimo di problemi prodotti dal server. |
| Calcolo delle sinapsi Spark: ambiente di calcolo di Azure con sinapsi Spark | vuoto | ambiente di calcolo di Azure con sinapsi Spark |
| Invio del processo del pool di Spark di sinapsi: Livio conf | -- | Configurazione di Tito. POST/batch
| Invio del processo del pool di Spark di sinapsi: configurazione del cluster del pool di Spark sinapsi | -- | Configurazione del pool di Spark di sinapsi |


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su Azure HDInsight per VSCode, vedere [Spark & hive per Visual Studio Code Tools](https://docs.microsoft.com/sql/big-data-cluster/spark-hive-tools-vscode).
- Per un video che illustra l'uso di Spark & hive per Visual Studio Code, vedere [spark & hive per Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).