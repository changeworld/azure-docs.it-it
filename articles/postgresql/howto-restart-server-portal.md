---
title: Riavviare il server-portale di Azure-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come riavviare un database di Azure per PostgreSQL-singolo server usando il portale di Azure.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 12/20/2020
ms.openlocfilehash: d3dc01ee163b941f450eeac98adfdf1ea0e91190
ms.sourcegitcommit: 0830e02635d2f240aae2667b947487db01f5fdef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2020
ms.locfileid: "97706899"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Riavviare database di Azure per PostgreSQL: singolo server usando il portale di Azure
In questo argomento viene descritta la procedura per riavviare un server di Database di Azure per PostgreSQL. Potrebbe essere necessario riavviare il server per motivi di manutenzione, causando così una breve interruzione del servizio mentre il server esegue l'operazione.

Il riavvio del server verrà bloccato se il servizio è occupato. È ad esempio possibile che il servizio stia elaborando un'operazione richiesta in precedenza, come il ridimensionamento di vCore.
 
> [!NOTE] 
> Il tempo necessario per completare un riavvio dipende dal processo di ripristino di PostgreSQL. Per ridurre i tempi di riavvio, è consigliabile ridurre al minimo l'attività in corso nel server prima del riavvio. Potrebbe inoltre essere necessario aumentare la frequenza di checkpoint. È anche possibile ottimizzare i valori dei parametri correlati ai checkpoint, tra cui `max_wal_size` . È inoltre consigliabile eseguire `CHECKPOINT` il comando prima di riavviare il server.

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [database di Azure per il server PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Eseguire il riavvio del server

La procedura seguente consente di riavviare il server PostgreSQL:

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per il server PostgreSQL.

2. Nella barra degli strumenti della pagina **Panoramica** del server fare clic su **Riavvia**.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Database di Azure per PostgreSQL - Panoramica - Pulsante Riavvia":::

3. Fare clic su **Sì** per confermare il riavvio del server.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Database di Azure per PostgreSQL - Conferma di riavvio":::

4. Si noti che lo stato del server passa a "Riavvio in corso".

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Database di Azure per PostgreSQL - Stato di riavvio":::

5. La conferma del riavvio del server ha esito positivo.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Database di Azure per PostgreSQL - Riavvio riuscito":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come impostare i parametri nel database di Azure per PostgreSQL](howto-configure-server-parameters-using-portal.md)