---
title: 'Esercitazione: Iniziare a esplorare il Knowledge Center di Synapse'
description: Questa esercitazione descrive come usare il Knowledge Center di Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/04/2021
ms.openlocfilehash: f4cc631bd3ff05dc63566677ec96ef0360d362c9
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517352"
---
# <a name="explore-the-synapse-knowledge-center"></a>Esplorare il Knowledge Center di Synapse

Questa esercitazione descrive come usare il Knowledge Center di Synapse Studio.

## <a name="finding-to-the-knowledge-center"></a>Ricerca nel Centro informazioni

È possibile trovare il Knowledge Center in Synapse Studio in due modi:

  1. Nell'hub Home, nell'angolo in alto a destra della pagina, fare clic su **Learn**.
  2. Nella barra dei menu in alto fare clic su **Guida** e quindi **Knowledge Center**.

Scegliere uno dei due metodi e aprire il **Knowledge Center**.

## <a name="exploring-the-knowledge-center"></a>Esplorazione del Centro informazioni

Quando è visibile, si può vedere che il **Centro informazioni** consente di eseguire tre operazioni:
* **Use samples immediately** (Usa immediatamente gli esempi). Per un esempio rapido del funzionamento di Synapse, scegliere questa opzione.
* **Esplora raccolta**. Questa opzione consente di collegare i set di dati di esempio e di aggiungere il codice di esempio sotto forma di script SQL, notebook e pipeline.
* **Presentazione Synapse Studio**. Questa opzione consente di esplorare rapidamente gli elementi di base di Synapse Studio. È utile se non si è mai usato Synapse Studio prima.

## <a name="use-samples-immediately-three-samples-to-help-you-get-started-fast"></a>Usare immediatamente gli esempi: tre esempi per iniziare rapidamente

Questa sezione contiene tre elementi:
* Esplorare i dati di esempio con Spark
* Eseguire query sui dati con SQL
* Creare una tabella esterna con SQL

1. Nella finestra **Centro informazioni** fare clic su **Usa esempi immediatamente**.
1. Selezionare **Query data with SQL (Eseguire query sui dati con SQL).**
1. Fare clic **su Usa esempio.**
1. Verrà aperto un nuovo script SQL di esempio.
1. Scorrere fino alla prima query (righe da 28 a 32) e selezionare il testo della query.
1. Fare clic su Esegui. Verrà eseguito solo il codice selezionato.

## <a name="gallery-a-collectiopn-of-sample-data-sets-and-sample-code"></a>Raccolta: raccolta di set di dati di esempio e codice di esempio

1. Passare al **Knowledge Center e** fare clic su Esplora **raccolta.**
1. Selezionare la **scheda Script SQL** nella parte superiore.
1. Selezionare **Load the New York Taxicab dataset** Data ingestion sample (Carica il set di dati taxicab di New York Esempio di inserimento dati) e fare clic su Continue **(Continua).**
1. In **Pool SQL** scegliere Selezionare un pool **esistente** e selezionare **SQLPOOL1** e selezionare il database **SQLPOOL1** creato in precedenza.
1. Fare clic **su Apri script**.
1. Verrà aperto un nuovo script SQL di esempio.
1. Fare clic su **Run**
1. Verranno create diverse tabelle per tutti i dati di NYC Taxi che verranno caricati usando il comando T-SQL COPY. Se queste tabelle sono state create nei passaggi di avvio rapido precedenti, selezionare ed eseguire solo il codice in CREATE e COPY per le tabelle che non esistono.

    > [!NOTE] 
    > Se si usa la raccolta di esempi di script SQL con un pool SQL dedicato (in precedenza SQL Data Warehouse), sarà possibile usare solo un pool SQL dedicato (in precedenza SQL Data Warehouse) esistente.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere un amministratore](get-started-add-admin.md)

