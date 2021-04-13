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
ms.openlocfilehash: 92d1534b5dd0233ce88b81d605c6502b67a121df
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307895"
---
# <a name="explore-the-synapse-knowledge-center"></a>Esplorare il Knowledge Center di Synapse

Questa esercitazione descrive come usare il Knowledge Center di Synapse Studio.

## <a name="introduction-to-the-knowledge-center"></a>Introduzione al Knowledge Center

È possibile trovare il Knowledge Center in Synapse Studio in due modi:

  1. Nell'hub Home, nell'angolo in alto a destra della pagina, fare clic su **Learn**.
  2. Nella barra dei menu in alto fare clic su **Guida** e quindi **Knowledge Center**.

Scegliere uno dei due metodi e aprire il **Knowledge Center**.

Una volta visibile, si noterà che il **centro informazioni** consente di eseguire tre operazioni:
* **Use samples immediately** (Usa immediatamente gli esempi). Per un esempio rapido del funzionamento di Synapse, scegliere questa opzione.
* **Esplora raccolta**. Questa opzione consente di collegare i set di dati di esempio e di aggiungere il codice di esempio sotto forma di script SQL, notebook e pipeline.
* **Tour sinapsi Studio**. Questa opzione consente di esplorare rapidamente gli elementi di base di Synapse Studio. È utile se non si è mai usato Synapse Studio prima.

## <a name="exploring-use-samples-immediately"></a>Esplorazione: usare immediatamente gli esempi

Questa sezione contiene tre elementi:
* Esplorare i dati di esempio con Spark
* Eseguire query sui dati con SQL
* Creare una tabella esterna con SQL

1. Nel **centro informazioni** fare clic su **usa immediatamente gli esempi**.
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

> [!div class="nextstepaction"]
> [Aggiungere un amministratore](get-started-add-admin.md)

