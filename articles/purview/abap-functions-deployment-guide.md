---
title: Modulo della funzione ABAP di estrazione dei metadati in SAP R3-Azure-competenza
description: Questo articolo illustra i passaggi per distribuire il modulo della funzione ABAP nel server SAP
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 9bd3c315fcc15317a9fa483289fdc326ca6aa47f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614361"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>Distribuire il modulo di funzione ABAP di estrazione dei metadati per la famiglia di Bridge SAP R3

Questo articolo illustra i passaggi per distribuire il modulo della funzione ABAP nel server SAP.

## <a name="overview"></a>Panoramica

SAP Business Suite 4 HANA (S/4HANA), ECC e R/3 ERP Bridge possono essere usati per estrarre i metadati dal server SAP. Questa operazione viene eseguita inserendo il modulo della funzione ABAP nel server SAP. Questo modulo della funzione è accessibile in remoto dal Bridge per eseguire query e scaricare (come file di testo) i metadati che contengono nel server SAP.

Quando viene eseguito, il Bridge quindi:

1. Importa i metadati da un file esistente già scaricato localmente da un'esecuzione precedente del Bridge.

2. Richiama l'API del modulo ABAP, attende il download e quindi importa i metadati da tale file.

Questo documento illustra i passaggi necessari per distribuire il modulo.

> [!Note]
> Le istruzioni seguenti sono state compilate in base a SAP GUI v. 7.2

## <a name="deployment-of-the-module"></a>Distribuzione del modulo

### <a name="create-a-package"></a>Creare un pacchetto

Questo passaggio è facoltativo ed è possibile usare un pacchetto esistente.

1. Accedere al server SAP S/4HANA o SAP ECC e aprire **Object Navigator** (Transazione SE80).

2. Selezionare **pacchetto** di opzioni nell'elenco e immettere un nome per il nuovo pacchetto (ad esempio, Z \_ miti) e quindi premere il pulsante **Visualizza**.

3. Selezionare **Sì** nella finestra **Crea pacchetto** . Di conseguenza, viene aperto un **Generatore di pacchetti finestra: Create Package** . Immettere il valore nel campo **breve descrizione** e selezionare l'icona **continua** .

4. Selezionare **le richieste personali** nella finestra di **richiesta Richiedi il Workbench locale** . Selezionare la richiesta di **sviluppo** .

### <a name="create-a-function-group"></a>Creazione di un gruppo di funzioni

In strumento di navigazione oggetti selezionare **gruppo di funzioni** dall'elenco e digitarne il nome nel campo di input seguente (ad esempio, Z \_ miti \_ FGROUP). Selezionare l'icona di **visualizzazione** .

1. Nella finestra **Crea oggetto** selezionare **Sì** per creare un nuovo gruppo di funzioni.

2. Specificare una descrizione appropriata nel campo di **testo breve** e premere il pulsante **Salva**.

3. Scegliere un pacchetto preparato nel passaggio precedente **creare un pacchetto** e selezionare **Salva**.

4. Confermare una richiesta premendo l'icona **continua**.

5. Attivare il gruppo di funzioni.

### <a name="create-the-abap-function-module"></a>Creare il modulo della funzione ABAP

1. Una volta creato il gruppo di funzioni, selezionarlo.

2. Fare clic con il pulsante destro del mouse sul nome del gruppo di funzioni in repository browser e selezionare **Crea**, quindi **modulo funzione**.

3. Nel campo **function module (modulo funzione** ) immettere `Z_MITI_DOWNLOAD` . Popola l'input di **testo breve** con una descrizione appropriata.

Quando il modulo è stato creato, specificare le informazioni seguenti:

1. Passare alla scheda **attributi** .

2. Selezionare **tipo di elaborazione** come **modulo della funzione abilitata per la modalità remota**.

   :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="Opzione Register Sources-Remote-Enabled function module" border="true":::

3. Passare alla scheda **codice sorgente** . Esistono due modi per distribuire il codice per la funzione:

   a. Dal menu principale caricare il file di testo [Z \_ miti \_ download](https://github.com/Azure/Purview-Samples/tree/master/connectors/sap) file. A tale scopo, selezionare **utilità**, **altre utilità**, quindi **caricare/scaricare**, quindi **caricare**.

   b. In alternativa, aprire il file, copiarne il contenuto e incollarlo nell'area del **codice sorgente** .

4. Passare alla scheda **Importa** e creare i parametri seguenti:

   a.  \_Tipo di area P DD02L-TABNAME (facoltativo = true)

   b.  *P \_ \_Stringa di tipo percorso locale* (facoltativo = true)

   c.  *P \_ tipo di linguaggio L001TAB-dati predefiniti \' E\'*

   d.  *TIPO ROWSKIPS in modo che \_ int predefinito 0*

   e.  *TIPO ROWCOUNT, quindi \_ int valore predefinito 0*

   > [!Note]
   > Scegliere il **valore pass** per tutti gli elementi

   :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="Opzione Register Sources-importa parametri" border="true":::

5. Passare alla scheda "tabelle" e definire quanto segue:

   `EXPORT_TABLE LIKE TAB512`

   :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="Opzioni per la registrazione delle origini-scheda tabelle" border="true":::

6. Passare alla scheda **eccezioni** e definire l'eccezione seguente: `E_EXP_GUI_DOWNLOADFAILED`

   :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="Opzioni per la registrazione delle origini-scheda eccezioni" border="true":::

7. Salvare la funzione (premere CTRL + S o scegliere **modulo funzione**, quindi **Salva** nel menu principale).

8. Fare clic sull'icona **attiva** sulla barra degli strumenti (CTRL + F3) e scegliere  **continua** pulsante nella finestra di dialogo. Se richiesto, è necessario selezionare le inclusioni generate da attivare insieme al modulo della funzione principale.

### <a name="testing-the-function"></a>Test della funzione

Al termine di tutti i passaggi precedenti, attenersi ai passaggi seguenti per testare la funzione:

1. Aprire il \_ modulo della funzione di download Z miti \_ .

2. Scegliere **modulo funzione**, **test**, quindi **modulo della funzione test** dal menu principale (o premere F8).

3. Immettere il percorso della cartella nella file system locale nel percorso locale del parametro \_ P \_ e premere l'icona **Esegui** sulla barra degli strumenti (o premere F8).

4. Inserire il nome dell'area di interesse nel \_ campo area P se è necessario scaricare o aggiornare un file con metadati. Al termine del funzionamento della funzione, la cartella indicata nel \_ parametro percorso locale P \_ deve contenere diversi file con metadati all'interno di. I nomi dei file possono essere specificati nel \_ campo area P.

La funzione completerà l'esecuzione e i metadati verranno scaricati molto più velocemente in caso di avvio nel computer che dispone di una connessione di rete ad alta velocità con SAP S/4HANA o ECC server.

## <a name="next-steps"></a>Passaggi successivi

- [Registrare e analizzare l'origine SAP ECC](register-scan-sapecc-source.md)
- [Registrare e analizzare l'origine SAP S/4HANA](register-scan-saps4hana-source.md)
