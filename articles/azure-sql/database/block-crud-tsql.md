---
title: Bloccare i comandi T-SQL per creare o modificare le risorse di Azure SQL
titleSuffix: Block T-SQL commands to create or modify Azure SQL resources
description: Questo articolo descrive in dettaglio una funzionalità che consente agli amministratori di Azure di bloccare i comandi T-SQL per creare o modificare le risorse SQL di Azure
ms.service: sql-database
ms.subservice: security
ms.custom: ''
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.topic: article
ms.date: 03/31/2021
ms.reviewer: ''
ROBOTS: NOINDEX
ms.openlocfilehash: 4ecaa8a3ee1d11ea13563ae5c74835b8d62fd960
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556137"
---
# <a name="what-is-block-t-sql-crud-feature"></a>Che cos'è la funzionalità di blocco T-SQL CRUD?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb-sqlmi.md)]


Questa funzionalità consente agli amministratori di Azure di bloccare la creazione o la modifica delle risorse SQL di Azure tramite T-SQL. Questa operazione viene applicata a livello di sottoscrizione per impedire ai comandi T-SQL di influenzare le risorse SQL in qualsiasi database SQL di Azure o istanza gestita.

## <a name="overview"></a>Panoramica

Per bloccare la creazione o la modifica delle risorse tramite T-SQL e applicare la gestione delle risorse tramite un modello di Azure Resource Manager (modello ARM) per una determinata sottoscrizione, è possibile usare le funzionalità di anteprima a livello di sottoscrizione di portale di Azure. Questa operazione è particolarmente utile quando si usano i [criteri di Azure](/azure/governance/policy/overview) per applicare gli standard aziendali tramite i modelli ARM. Poiché T-SQL non rispetta i criteri di Azure, è possibile applicare un blocco sulle operazioni di creazione o modifica di T-SQL. La sintassi bloccata include le istruzioni CRUD (create, Update, Delete) per i database in Azure SQL, in particolare le `CREATE DATABASE` `ALTER DATABASE` istruzioni, e `DROP DATABASE` . 

Le operazioni CRUD T-SQL possono essere bloccate tramite portale di Azure, [PowerShell](/powershell/module/az.resources/register-azproviderfeature)o l'interfaccia della riga di comando di [Azure](/cli/azure/feature#az_feature_register).

## <a name="permissions"></a>Autorizzazioni

Per registrare o rimuovere questa funzionalità, l'utente di Azure deve essere un membro del ruolo proprietario o collaboratore della sottoscrizione.

## <a name="examples"></a>Esempio

Nella sezione seguente viene descritto come è possibile registrare o annullare la registrazione di una funzionalità di anteprima con il provider di risorse Microsoft. SQL in portale di Azure: 

### <a name="register-block-t-sql-crud"></a>Register Block T-SQL CRUD

1. Passare alla sottoscrizione in portale di Azure.
2. Selezionare la scheda **funzionalità Anteprima** . 
3. Selezionare **blocca T-SQL CRUD**.
4. Dopo aver selezionato on **Block T-SQL CRUD**, verrà aperta una nuova finestra, Select **Register**, per registrare questo blocco con il provider di risorse Microsoft. SQL.

![Selezionare "blocca T-SQL CRUD" nell'elenco delle funzionalità di anteprima](./media/block-tsql-crud/block-tsql-crud.png)

![Con "blocco T-SQL CRUD" selezionato, selezionare Register (registra).](./media/block-tsql-crud/block-tsql-crud-register.png)

  
### <a name="re-register-microsoftsql-resource-provider"></a>Ripetere la registrazione del provider di risorse Microsoft. SQL 
Dopo aver registrato il blocco di T-SQL CRUD con il provider di risorse Microsoft. SQL, è necessario registrare di nuovo il provider di risorse Microsoft. SQL per rendere effettive le modifiche. Per registrare di nuovo il provider di risorse Microsoft. SQL:

1. Passare alla sottoscrizione in portale di Azure.
2. Selezionare nella scheda **provider di risorse** .
3. Cercare e selezionare provider di risorse **Microsoft. SQL** .
4. Selezionare **Re-Register**. 

> [!NOTE]
> Il passaggio di ripetizione della registrazione è obbligatorio per l'applicazione del blocco T-SQL alla sottoscrizione. 

![Registrare di nuovo il provider di risorse Microsoft. SQL](./media/block-tsql-crud/block-tsql-crud-re-register.png)

### <a name="removing-block-t-sql-crud"></a>Rimozione di un blocco T-SQL CRUD
Per rimuovere il blocco per le operazioni di creazione o modifica di T-SQL dalla sottoscrizione, annullare prima la registrazione del blocco T-SQL registrato in precedenza. Quindi, registrare di nuovo il provider di risorse Microsoft. SQL, come illustrato in precedenza, in modo da rendere effettiva la rimozione del blocco T-SQL. 


## <a name="next-steps"></a>Passaggi successivi

- [Panoramica delle funzionalità di sicurezza del database SQL di Azure](security-overview.md)
- [Procedure consigliate per la sicurezza del database SQL di Azure](security-best-practice.md)
