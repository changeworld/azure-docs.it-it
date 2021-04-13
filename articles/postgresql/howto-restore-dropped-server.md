---
title: Ripristinare un server di Database di Azure per PostgreSQL eliminato
description: Questo articolo descrive come ripristinare un server eliminato in Database di Azure per PostgreSQL usando il portale di Azure.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 5b5bb9fd6e3d34fc4a6b0ae90a2cd76fc84e9ce1
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366522"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Ripristinare un server di Database di Azure per PostgreSQL eliminato

Quando un server viene eliminato, il backup del server di database può essere conservato fino a cinque giorni nel servizio. Il backup del database è accessibile e ripristinato solo dalla sottoscrizione di Azure in cui risiedeva originariamente il server. È possibile seguire i passaggi consigliati seguenti per ripristinare una risorsa server PostgreSQL eliminata entro 5 giorni dall'eliminazione del server. I passaggi consigliati funzioneranno solo se il backup per il server è ancora disponibile e non è stato eliminato dal sistema. 

## <a name="pre-requisites"></a>Prerequisiti
Per ripristinare un server di Database di Azure per PostgreSQL eliminato, è necessario quanto segue:
- Nome della sottoscrizione di Azure che ospita il server originale
- Percorso in cui è stato creato il server

## <a name="steps-to-restore"></a>Passaggi per il ripristino

1. Accedere al [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Selezionare il **Monitoraggio di Azure,** quindi selezionare **Log attività**.

2. Nel log attività fare clic su **Aggiungi filtro** come illustrato e impostare i filtri seguenti per gli elementi seguenti

    - **Sottoscrizione** = Sottoscrizione che ospita il server eliminato
    - **Tipo di** risorsa = Server di Database di Azure per PostgreSQL (Microsoft.DBforPostgreSQL/servers)
    - **Operation** = Delete PostgreSQL Server (Microsoft.DBforPostgreSQL/servers/delete)
 
    ![Log attività filtrato per l'operazione di eliminazione del server PostgreSQL](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Selezionare **l'evento Delete PostgreSQL Server (Elimina server PostgreSQL),** quindi selezionare **la scheda JSON**. Copiare `resourceId` gli attributi e `submissionTimestamp` nell'output JSON. Il valore resourceId è nel formato seguente: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Passare alla pagina [dell'API REST](/rest/api/PostgreSQL/servers/create) per la creazione del server PostgreSQL e selezionare la **scheda Prova** evidenziata in verde. Accedere con l'account Azure.

 5. Specificare **le proprietà resourceGroupName**, **serverName** (nome del server eliminato), **subscriptionId** in base al valore JSON dell'attributo resourceId acquisito nel passaggio 3 precedente. La proprietà api-version è pre-popolata e può essere lasciata così come è, come illustrato nell'immagine seguente.

    ![Creare un server usando l'API REST](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. Scorrere di seguito nella sezione Request Body (Corpo richiesta) e incollare quanto segue sostituendo "Dropped server Location"(ad esempio CentralUS, EastUS e così via), "submissionTimestamp" e "resourceId". Per "restorePointInTime", specificare il valore "submissionTimestamp" meno **15** minuti per assicurarsi che il comando non eserciti errori.
    
    ```json
    {
      "location": "Dropped Server Location",  
      "properties": 
      {
        "restorePointInTime": "submissionTimestamp - 15 minutes",
        "createMode": "PointInTimeRestore",
        "sourceServerId": "resourceId"
      }
    }
    ```

    Ad esempio, se l'ora corrente è 2020-11-02T23:59:59.0000000Z, è consigliabile almeno 15 minuti prima del punto di ripristino nel tempo 2020-11-02T23:44:59.0000000Z.

    > [!Important]
    > È previsto un limite di tempo di cinque giorni dopo l'applicazione del server. Dopo cinque giorni, è previsto un errore perché non è possibile trovare il file di backup.
    
7. Se viene visualizzato il codice di risposta 201 o 202, la richiesta di ripristino viene inviata correttamente. 

    La creazione del server può richiedere tempo a seconda delle dimensioni del database e delle risorse di calcolo di cui è stato effettuato il provisioning nel server originale. Lo stato di ripristino può essere monitorato dal log attività filtrando per 
   - **Sottoscrizione** = Sottoscrizione
   - **Tipo di risorsa** = Database di Azure per server PostgreSQL (Microsoft.DBforPostgreSQL/servers) 
   - **Operation** = Update PostgreSQL Server Create

## <a name="next-steps"></a>Passaggi successivi
- Se si sta tentando di ripristinare un server entro cinque giorni e si riceve comunque un errore dopo aver seguito in modo accurato i passaggi descritti in precedenza, aprire un evento imprevisto di supporto per assistenza. Se si sta tentando di ripristinare un server eliminato dopo cinque giorni, è previsto un errore perché non è possibile trovare il file di backup. Non aprire un ticket di supporto in questo scenario. Il team di supporto non può fornire assistenza se il backup viene eliminato dal sistema. 
- Per evitare l'eliminazione accidentale dei server, è consigliabile usare [Blocchi risorse](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222).
