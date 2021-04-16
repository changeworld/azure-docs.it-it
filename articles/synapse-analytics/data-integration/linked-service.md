---
title: Proteggere un servizio collegato
description: Informazioni su come effettuare il provisioning e proteggere un servizio collegato con una rete virtuale gestita
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 6be76878a9a07c5f4a1e2a9348bb7b09cb1b10eb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567588"
---
# <a name="secure-a-linked-service-with-private-links"></a>Proteggere un servizio collegato con collegamenti privati

Questo articolo descrive come proteggere un servizio collegato in Synapse con un endpoint privato.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**: usare Azure Data Lake Gen 2 come archivio dati di *origine*. Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../../storage/common/storage-account-create.md) per informazioni su come crearne uno. Assicurarsi che l'account di archiviazione abbia il Synapse Studio IP per  accedervi e che sia consentito solo alle reti selezionate di accedere all'account di archiviazione. L'impostazione nel pannello **Firewall e reti virtuali** dovrebbe essere simile all'immagine seguente.

![Account di archiviazione protetto](./media/secure-storage-account.png)

## <a name="create-a-linked-service-with-private-links"></a>Creare un servizio collegato con collegamenti privati

In Azure Synapse Analytics si usano i servizi collegati per definire le informazioni di connessione ad altri servizi. In questa sezione Azure Synapse Analytics e Azure Data Lake Gen 2 verranno aggiunti come servizi collegati.

1. Aprire il Azure Synapse Studio e passare alla **scheda** Gestisci.
1. In **Connessioni esterne** selezionare **Servizi collegati**.
1. Per aggiungere un servizio collegato, selezionare **Nuovo**.
1. Selezionare il Azure Data Lake Storage Gen2 riquadro dall'elenco e selezionare **Continua.**
1. Assicurarsi di abilitare **Interactive Authoring** (Creazione interattiva). L'abilitazione potrebbe richiedere circa 1 minuto. 
1. Immettere le credenziali di autenticazione. I tipi di autenticazione attualmente supportati sono la chiave dell'account, l'entità servizio e l'identità gestita. Selezionare test connessione per verificare che le credenziali siano corrette.
1. Selezionare **Test connessione**. L'errore dovrebbe verificarsi perché l'account di archiviazione non consente l'accesso senza la creazione e l'approvazione di un endpoint privato. Nel messaggio di errore dovrebbe essere visualizzato un collegamento per creare un **endpoint** privato che è possibile seguire per passare alla parte successiva. Se si segue questo collegamento, ignorare la parte successiva.
1. Selezionare **Create** (Crea) al termine.

## <a name="create-a-managed-private-endpoint"></a>Creare un endpoint privato gestito

Se non è stato selezionato il collegamento ipertestuale durante il test della connessione precedente, seguire il percorso seguente. Creare un endpoint privato gestito che verrà connesso al servizio collegato creato in precedenza.

1. Passare alla scheda **Gestisci**.
1. Passare alla **sezione Reti virtuali** gestite.
1. Selezionare **+ Nuovo** in Endpoint privato gestito.
1. Selezionare il Azure Data Lake Storage Gen2 riquadro dall'elenco e selezionare **Continua.**
1. Immettere il nome dell'account di archiviazione creato in precedenza.
1. Selezionare **Crea**
1. Dopo aver atteso alcuni secondi, si noterà che il collegamento privato creato necessita di un'approvazione.

## <a name="private-link-approval"></a>Approvazione del collegamento privato
1. Selezionare l'endpoint privato creato in precedenza. È possibile visualizzare un collegamento ipertestuale che consente di approvare l'endpoint privato a livello di account di archiviazione. *In alternativa, passare direttamente all'account portale di Azure di archiviazione e passare al **pannello Connessioni endpoint** privato.*
1. Selezionare l'endpoint privato creato in Studio e selezionare **Approva.**
1. Aggiungere una descrizione e selezionare **Sì**
1. Tornare al Synapse Studio nella sezione **Reti virtuali** gestite della **scheda** Gestisci.
1. L'approvazione per l'endpoint privato dovrebbe richiedere circa 1 minuto.

## <a name="check-the-connection-works"></a>Verificare il funzionamento della connessione
1. Passare alla **scheda Gestisci** e selezionare il servizio collegato creato.
1. Assicurarsi che la **creazione interattiva** sia attiva.
1. Selezionare **Test connessione**. La connessione dovrebbe avere esito positivo.

È stata stabilita una connessione sicura e privata tra Synapse e il servizio collegato.

## <a name="next-steps"></a>Passaggi successivi


Per altre informazioni sull'endpoint privato gestito in Azure Synapse Analytics, vedere [Endpoint privati gestiti.](../security/synapse-workspace-managed-private-endpoints.md)


Per altre informazioni sull'integrazione dei dati Azure Synapse Analytics, vedere [l'articolo Inserimento di dati in un data](data-integration-data-lake.md) lake.