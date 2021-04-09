---
title: Gestire server-portale di Azure-database di Azure per MySQL
description: Informazioni su come gestire un database di Azure per il server MySQL dalla portale di Azure.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/26/2021
ms.openlocfilehash: 83876f77e0d7ffc0ae20bc5a545c1f18f53f4a8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897986"
---
# <a name="manage-an-azure-database-for-mysql-server-using-the-azure-portal"></a>Gestire un database di Azure per il server MySQL usando il portale di Azure

Questo articolo illustra come gestire il database di Azure per i server MySQL. Le attività di gestione includono il ridimensionamento di calcolo e archiviazione, la reimpostazione della password amministratore e la visualizzazione dei dettagli del server.

> [!NOTE]
> Questo articolo contiene riferimenti al termine _slave_, un termine che Microsoft non usa più. Quando il termine verrà rimosso dal software, verrà rimosso anche dall'articolo.
>

## <a name="sign-in"></a>Accesso

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="create-a-server"></a>Creare un server

Visitare la [Guida introduttiva](quickstart-create-mysql-server-database-using-azure-portal.md) per informazioni su come creare e iniziare a usare un database di Azure per il server MySQL.

## <a name="scale-compute-and-storage"></a>Ridimensionare le risorse di calcolo e archiviazione

Dopo la creazione del server è possibile scalare tra i livelli per utilizzo generico e con ottimizzazione per la memoria in base alle esigenze. È anche possibile ridimensionare il calcolo e la memoria aumentando o diminuendo vcore. L'archiviazione può essere aumentata (Tuttavia, non è possibile ridimensionare lo spazio di archiviazione).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Scalabilità tra per utilizzo generico e livelli con ottimizzazione per la memoria

È possibile eseguire la scalabilità da per utilizzo generico a con ottimizzazione per la memoria e viceversa. La modifica da e verso il livello Basic dopo la creazione del server non è supportata.

1. Selezionare il server nella portale di Azure. Selezionare piano **tariffario**, che si trova nella sezione **Impostazioni** .

2. Selezionare **per utilizzo generico** o con ottimizzazione per la **memoria**, a seconda di ciò che si sta ridimensionando.

   :::image type="content" source="./media/howto-create-manage-server-portal/change-pricing-tier.png" alt-text="Screenshot dei portale di Azure per scegliere il livello di base, per utilizzo generico o con ottimizzazione per la memoria nel database di Azure per MySQL":::

   > [!NOTE]
   > La modifica di livelli comporta il riavvio del server.

3. Selezionare **OK** per salvare le modifiche.

### <a name="scale-vcores-up-or-down"></a>Ridimensionare vcore

1. Selezionare il server nella portale di Azure. Selezionare piano **tariffario**, che si trova nella sezione **Impostazioni** .

2. Modificare l'impostazione di **vCore** spostando il dispositivo di scorrimento sul valore desiderato.

    :::image type="content" source="./media/howto-create-manage-server-portal/scaling-compute.png" alt-text="Screenshot del portale di Azure scegliere l'opzione vCore nel database di Azure per MySQL":::

    > [!NOTE]
    > Il ridimensionamento di Vcore causa un riavvio del server.

3. Selezionare **OK** per salvare le modifiche.

### <a name="scale-storage-up"></a>Ridimensionare l'archiviazione

1. Selezionare il server nella portale di Azure. Selezionare piano **tariffario**, che si trova nella sezione **Impostazioni** .

2. Modificare l'impostazione di **archiviazione** spostando il dispositivo di scorrimento fino al valore desiderato.

   :::image type="content" source="./media/howto-create-manage-server-portal/scaling-storage.png" alt-text="Screenshot di portale di Azure per scegliere la scalabilità di archiviazione nel database di Azure per MySQL":::

   > [!NOTE]
   > Non è possibile ridimensionare l'archiviazione.

3. Selezionare **OK** per salvare le modifiche.

## <a name="update-admin-password"></a>Aggiorna password amministratore

È possibile modificare la password del ruolo amministratore utilizzando la portale di Azure.

1. Selezionare il server nella portale di Azure. Nella finestra **Panoramica** selezionare **Reimposta password**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-reset-password.png" alt-text="Screenshot della portale di Azure per reimpostare la password nel database di Azure per MySQL":::

2. Immettere una nuova password e confermarla. Nella casella di testo vengono visualizzati i requisiti di complessità delle password.

   :::image type="content" source="./media/howto-create-manage-server-portal/reset-password.png" alt-text="Screenshot della portale di Azure per reimpostare la password e salvarla nel database di Azure per MySQL":::

3. Selezionare **OK** per salvare la nuova password.
 

> [!IMPORTANT]
> Il ripristino della password di amministratore del server reimposterà automaticamente i privilegi di amministratore del server predefiniti. Provare a reimpostare la password amministratore del server se sono stati revocati accidentalmente uno o più privilegi di amministratore del server.
   
> [!NOTE]
> Per impostazione predefinita, l'utente amministratore del server dispone dei privilegi seguenti: SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, reload, PROCESS, REFERENCEs, INDEX, ALTER, SHOW databases, CREATE TEMPORARY Tables, LOCK TABLES, EXECUTE, REPLICATION SLAVE, REPLICATION CLIENT, CREATE VIEW, SHOW VIEW, CREATE ROUTINE, ALTER ROUTINE, CREATE USER, EVENT, TRIGGER

## <a name="delete-a-server"></a>Eliminazione di un server

Se non è più necessario, è possibile eliminare il server.

1. Selezionare il server nella portale di Azure. Nella finestra **Panoramica** selezionare **Elimina**.

   :::image type="content" source="./media/howto-create-manage-server-portal/overview-delete.png" alt-text="Screenshot della portale di Azure per eliminare il server nel database di Azure per MySQL":::

2. Digitare il nome del server nella casella di input per confermare che si tratta del server che si desidera eliminare.

   :::image type="content" source="./media/howto-create-manage-server-portal/confirm-delete.png" alt-text="Screenshot dei portale di Azure per confermare l'eliminazione del server nel database di Azure per MySQL":::

   > [!NOTE]
   > L'eliminazione di un server è irreversibile.

3. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sui [backup e sul ripristino del server](howto-restore-server-portal.md)
- Informazioni sulle [Opzioni di ottimizzazione e monitoraggio in database di Azure per MySQL](concepts-monitoring.md)
