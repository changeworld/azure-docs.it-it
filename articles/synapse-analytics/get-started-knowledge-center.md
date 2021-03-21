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
ms.date: 11/16/2020
ms.openlocfilehash: 59364a0a2c1d1e411f41dc35dee812c8e920b72d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98209203"
---
# <a name="explore-the-synapse-knowledge-center"></a>Esplorare il Knowledge Center di Synapse

Questa esercitazione descrive come usare il Knowledge Center di Synapse Studio.

## <a name="getting-to-the-knowledge-center"></a>Accesso al Knowledge Center

È possibile trovare il Knowledge Center in Synapse Studio in due modi:

  1. Nell'hub Home, nell'angolo in alto a destra della pagina, fare clic su **Learn**.
  2. Nella barra dei menu in alto fare clic su **Guida** e quindi **Knowledge Center**.

Scegliere uno dei due metodi e aprire il **Knowledge Center**.

## <a name="overview"></a>Panoramica

Il **Knowledge Center** consente di eseguire tre operazioni:
* **Use samples immediately** (Usa immediatamente gli esempi). Per un esempio rapido del funzionamento di Synapse, scegliere questa opzione.
* **Esplora raccolta**. Questa opzione consente di collegare i set di dati di esempio e di aggiungere il codice di esempio sotto forma di script SQL, notebook e pipeline.
* **Tour sinapsi Studio**. Questa opzione consente di esplorare rapidamente gli elementi di base di Synapse Studio. È utile se non si è mai usato Synapse Studio prima.

## <a name="exploring-blob-storage-with-serverless-sql-pool"></a>Esplorare l'archivio BLOB con il pool SQL serverless

1. Visitare il **centro informazioni**, fare clic su **usa immediatamente gli esempi**.
1. Selezionare **query data con SQL**.
1. Fare clic su **USA esempio**.
1. Viene aperto un nuovo script SQL di esempio.
1. Scorrere fino alla prima query (righe da 28 a 32) e selezionare il testo della query.
1. Fare clic su Esegui. Verrà eseguito solo il codice selezionato.

## <a name="loading-more-nyc-taxi-data"></a>Caricamento di altri dati di NYC Taxi
1. Visitare il **centro informazioni**, fare clic su **Esplora raccolta**.
1. Selezionare la scheda **script SQL** nella parte superiore.
1. Selezionare carica l'esempio di inserimento dati **del set di dati del taxi di New York** , fare clic su **continua**.
1. In **pool SQL** scegliere **selezionare un pool esistente** e selezionare **SQLPOOL1** e selezionare il database **SQLPOOL1** creato in precedenza.
1. Fare clic su **Apri script**.
1. Viene aperto un nuovo script SQL di esempio.
1. Fare clic su **Run**
1. Verranno create diverse tabelle per tutti i dati di NYC Taxi che verranno caricati usando il comando T-SQL COPY. Se queste tabelle sono state create nei passaggi di avvio rapido precedenti, selezionare ed eseguire solo il codice per creare e copiare le tabelle che non esistono.

    > [!NOTE] 
    > Se si usa la raccolta di esempi di script SQL con un pool SQL dedicato (in precedenza SQL Data Warehouse), sarà possibile usare solo un pool SQL dedicato (in precedenza SQL Data Warehouse) esistente.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Synapse Analytics](get-started.md)
* [Creare un'area di lavoro](quickstart-create-workspace.md)
* [Usare i pool SQL serverless](quickstart-sql-on-demand.md)
