---
title: Configurare il runtime di integrazione SSIS di Azure per la continuità aziendale e il ripristino di emergenza (BCDR)
description: Questo articolo descrive come configurare il runtime di integrazione SSIS di Azure in Azure Data Factory con il database SQL di Azure/Istanza gestita gruppo di failover per la continuità aziendale e il ripristino di emergenza (BCDR).
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/25/2021
ms.openlocfilehash: 73c27204ee8730c95d1cbeecf8777767173e73d9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710252"
---
# <a name="configure-azure-ssis-integration-runtime-for-business-continuity-and-disaster-recovery-bcdr"></a>Configurare il runtime di integrazione SSIS di Azure per la continuità aziendale e il ripristino di emergenza (BCDR) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Il database SQL di Azure/Istanza gestita e SQL Server Integration Services (SSIS) in Azure Data Factory (ADF) può essere combinato come soluzione PaaS (all-Platform as a Service) consigliata per la migrazione SQL Server. È possibile distribuire i progetti SSIS nel database del catalogo SSIS (SSISDB) ospitato da database SQL di Azure/Istanza gestita ed eseguire i pacchetti SSIS in Azure SSIS Integration Runtime (IR) in ADF.

Per la continuità aziendale e il ripristino di emergenza (BCDR), il database SQL di Azure/Istanza gestita può essere configurato con un [gruppo di failover/replica geografica](https://docs.microsoft.com/azure/azure-sql/database/auto-failover-group-overview), in cui SSISDB in un'area di Azure primaria con accesso in lettura/scrittura (ruolo primario) verrà replicato continuamente in un'area secondaria con accesso in sola lettura (ruolo secondario). Quando si verifica un'emergenza nell'area primaria, viene attivato un failover, in cui il SSISDBs primario e secondario effettuerà lo swapping dei ruoli.

Per BCDR, è anche possibile configurare una coppia di runtime di integrazione SSIS di Azure con due standby che funziona in sincronia con il database SQL di Azure/Istanza gestita gruppo di failover. Ciò consente di avere una coppia di esecuzione di Azure-SSIS IRs che in qualsiasi momento, solo una può accedere al database SSISDB primario per recuperare ed eseguire i pacchetti, nonché scrivere i log di esecuzione del pacchetto (ruolo primario), mentre l'altro può eseguire la stessa operazione solo per i pacchetti distribuiti altrove, ad esempio in File di Azure (ruolo secondario). Quando si verifica il failover di SSISDB, il fisco di Azure-SSIS primario e secondario eseguirà anche lo swapping dei ruoli e, se entrambi sono in esecuzione, si verificherà un tempo di inattività quasi nullo.

Questo articolo descrive come configurare Azure-SSIS IR con il database SQL di Azure/Istanza gestita il gruppo di failover per BCDR.

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-database-failover-group"></a>Configurare una coppia di Azure-SSIS IR a doppio standby con il gruppo di failover del database SQL di Azure

Per configurare una coppia di Azure-SSIS IR a doppio standby sincronizzata con il gruppo di failover del database SQL di Azure, completare i passaggi seguenti.

1. Con l'interfaccia utente di portale di Azure/ADF è possibile creare una nuova Azure-SSIS IR con il server di database SQL di Azure primario per ospitare SSISDB nell'area primaria. Se si dispone di un Azure-SSIS IR già collegato a SSISDB ospitato dal server di database SQL di Azure primario ed è ancora in esecuzione, è necessario arrestarlo prima per riconfigurarlo. Si tratta del Azure-SSIS IR primario. Quando [si sceglie di usare SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) nella pagina **impostazioni di distribuzione** del riquadro di installazione di **Integration Runtime** , selezionare anche la casella di controllo Usa la coppia di Azure-SSIS Integration Runtime a **doppio standby con failover SSISDB** . Per **nome coppia doppio standby** immettere un nome per identificare la coppia di Azure-SSIS primaria e secondaria. Quando si completa la creazione del Azure-SSIS IR primario, quest'ultima verrà avviata e collegata a un SSISDB primario che verrà creato per conto dell'utente con accesso in lettura/scrittura. Se è stato appena riconfigurato, è necessario riavviarlo.

1. Con portale di Azure è possibile verificare se il database SSISDB primario è stato creato nella pagina **Panoramica** del server di database SQL di Azure primario. Una volta creato, è possibile [creare un gruppo di failover per i server del database SQL di Azure primario e secondario e aggiungere SSISDB alla](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#2---create-the-failover-group) pagina dei **gruppi di failover** . Una volta creato il gruppo di failover, è possibile verificare se il database SSISDB primario è stato replicato in uno secondario con accesso in sola lettura nella pagina **Panoramica** del server di database SQL di Azure secondario.

1. Con l'interfaccia utente di portale di Azure/ADF è possibile creare un'altra Azure-SSIS IR con il server di database SQL di Azure secondario per ospitare SSISDB nell'area secondaria. Si tratta del Azure-SSIS IR secondario. Per BCDR completi, assicurarsi che tutte le risorse da cui dipende siano create anche nell'area secondaria, ad esempio archiviazione di Azure per archiviare script/file di installazione personalizzati, ADF per l'orchestrazione/pianificazione delle esecuzioni dei pacchetti e così via. Quando [si sceglie di usare SSISDB](./tutorial-deploy-ssis-packages-azure.md#creating-ssisdb) nella pagina **impostazioni di distribuzione** del riquadro di installazione di **Integration Runtime** , selezionare anche la casella di controllo Usa la coppia di Azure-SSIS Integration Runtime a **doppio standby con failover SSISDB** . Per **nome coppia doppio standby** immettere lo stesso nome per identificare la coppia di Azure-SSIS primaria e secondaria di Azure-SSIS. Al termine della creazione del Azure-SSIS IR secondario, il database verrà avviato e collegato al database SSISDB secondario.

1. Se si vuole avere un tempo di inattività quasi nullo quando si verifica il failover di SSISDB, lasciare che sia in esecuzione l'IRs Azure-SSIS. Solo il Azure-SSIS IR primario può accedere al database SSISDB primario per recuperare ed eseguire i pacchetti, nonché scrivere i log di esecuzione del pacchetto, mentre i Azure-SSIS IR secondari possono eseguire la stessa operazione solo per i pacchetti distribuiti altrove, ad esempio in File di Azure. Se si vuole ridurre al minimo il costo di esecuzione, è possibile arrestare la Azure-SSIS IR secondaria dopo che è stata creata. Quando si verifica il failover di SSISDB, l'IRs Azure-SSIS primaria e secondaria esegue lo swapping dei ruoli. Se il Azure-SSIS IR primario viene arrestato, è necessario riavviarlo. A seconda del fatto che venga inserito in una rete virtuale e che venga usato il metodo di inserimento, l'esecuzione sarà necessaria entro 5 minuti o circa 20-30 minuti.

1. Se si [USA ADF per l'orchestrazione o la pianificazione delle esecuzioni dei pacchetti](./how-to-invoke-ssis-package-ssis-activity.md), assicurarsi che tutte le pipeline di ADF pertinenti con le attività Esegui pacchetto SSIS e i trigger associati vengano copiate nel file ADF secondario con i trigger inizialmente disabilitati. Quando si verifica il failover di SSISDB, è necessario abilitarli.

1. È possibile [eseguire il test del gruppo di failover del database SQL di Azure](https://docs.microsoft.com/azure/azure-sql/database/failover-group-add-single-database-tutorial?tabs=azure-portal#3---test-failover) e controllare [Azure-SSIS IR pagina di monitoraggio nel portale di](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) Azure Data Factory, se l'IRS Azure-SSIS primaria e secondaria hanno ruoli scambiati. 

## <a name="configure-a-dual-standby-azure-ssis-ir-pair-with-azure-sql-managed-instance-failover-group"></a>Configurare una coppia di Azure-SSIS IR a doppio standby con il gruppo di failover di SQL Istanza gestita di Azure

Per configurare una coppia di Azure-SSIS IR a doppio standby sincronizzata con il gruppo di failover di SQL Istanza gestita di Azure, completare i passaggi seguenti.

1. Con portale di Azure è possibile [creare un gruppo di failover per le istanze gestite di SQL Azure primarie e secondarie](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal) nella pagina **gruppi di failover** del istanza gestita SQL di Azure primario.

1. Con l'interfaccia utente di portale di Azure/ADF è possibile creare un nuovo Azure-SSIS IR con il Istanza gestita primario di Azure SQL per ospitare SSISDB nell'area primaria. Se si dispone di un Azure-SSIS IR già collegato a SSISDB ospitato dal Istanza gestita SQL di Azure primario ed è ancora in esecuzione, è necessario prima arrestarlo per riconfigurarlo. Si tratta del Azure-SSIS IR primario. Quando [si sceglie di usare SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb) nella pagina **impostazioni di distribuzione** del riquadro di installazione di **Integration Runtime** , selezionare anche la casella di controllo Usa la coppia di Azure-SSIS Integration Runtime a **doppio standby con failover SSISDB** . Per **nome coppia doppio standby** immettere un nome per identificare la coppia di Azure-SSIS primaria e secondaria. Quando si completa la creazione del Azure-SSIS IR primario, quest'ultima verrà avviata e collegata a un SSISDB primario che verrà creato per conto dell'utente con accesso in lettura/scrittura. Se è stato appena riconfigurato, è necessario riavviarlo. È anche possibile verificare se il database SSISDB primario è stato replicato in uno secondario con accesso in sola lettura nella pagina **Panoramica** del istanza gestita SQL di Azure secondario.

1. Con l'interfaccia utente di portale di Azure/ADF è possibile creare un'altra Azure-SSIS IR con il Istanza gestita SQL di Azure secondario per ospitare SSISDB nell'area secondaria. Si tratta del Azure-SSIS IR secondario. Per BCDR completi, assicurarsi che tutte le risorse da cui dipende siano create anche nell'area secondaria, ad esempio archiviazione di Azure per archiviare script/file di installazione personalizzati, ADF per l'orchestrazione/pianificazione delle esecuzioni dei pacchetti e così via. Quando [si sceglie di usare SSISDB](./create-azure-ssis-integration-runtime.md#creating-ssisdb) nella pagina **impostazioni di distribuzione** del riquadro di installazione di **Integration Runtime** , selezionare anche la casella di controllo Usa la coppia di Azure-SSIS Integration Runtime a **doppio standby con failover SSISDB** . Per **nome coppia doppio standby** immettere lo stesso nome per identificare la coppia di Azure-SSIS primaria e secondaria di Azure-SSIS. Al termine della creazione del Azure-SSIS IR secondario, il database verrà avviato e collegato al database SSISDB secondario.

1. Istanza gestita SQL di Azure consente di proteggere i dati sensibili nei database, ad esempio SSISDB, mediante la crittografia tramite la chiave master del database (DMK). La DMK stessa viene a sua volta crittografata con la chiave master del servizio (SMK) per impostazione predefinita. Al momento della stesura del documento, il gruppo di failover di SQL Istanza gestita di Azure non esegue la replica di SMK dal Istanza gestita primario di Azure SQL, quindi DMK e, a sua volta, non possono essere decrittografati nel Istanza gestita SQL di Azure secondario dopo il failover. Per ovviare a questo problema, è possibile aggiungere una crittografia delle password per la DMK da decrittografare nel Istanza gestita SQL secondario di Azure. Usare SSMS per completare i passaggi seguenti.

   1. Eseguire il comando seguente per SSISDB nel Istanza gestita SQL di Azure primario per aggiungere una password per la crittografia DMK.

      ```sql
      ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'YourPassword'
      ```
   
   1. Eseguire il comando seguente per SSISDB in entrambe le istanze gestite di SQL Azure primarie e secondarie per aggiungere la nuova password per la decrittografia di DMK.

      ```sql
      EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB', @password = N'YourPassword', @action = N'add'
      ```

1. Se si vuole avere un tempo di inattività quasi nullo quando si verifica il failover di SSISDB, lasciare che sia in esecuzione l'IRs Azure-SSIS. Solo il Azure-SSIS IR primario può accedere al database SSISDB primario per recuperare ed eseguire i pacchetti, nonché scrivere i log di esecuzione del pacchetto, mentre i Azure-SSIS IR secondari possono eseguire la stessa operazione solo per i pacchetti distribuiti altrove, ad esempio in File di Azure. Se si vuole ridurre al minimo il costo di esecuzione, è possibile arrestare la Azure-SSIS IR secondaria dopo che è stata creata. Quando si verifica il failover di SSISDB, l'IRs Azure-SSIS primaria e secondaria esegue lo swapping dei ruoli. Se il Azure-SSIS IR primario viene arrestato, è necessario riavviarlo. A seconda del fatto che venga inserito in una rete virtuale e che venga usato il metodo di inserimento, l'esecuzione sarà necessaria entro 5 minuti o circa 20-30 minuti.

1. Se si [Usa l'agente di istanza gestita SQL di Azure per l'orchestrazione e la pianificazione delle esecuzioni dei pacchetti](./how-to-invoke-ssis-package-managed-instance-agent.md), assicurarsi che tutti i processi SSIS pertinenti con i passaggi del processo e le pianificazioni associate vengano copiati nel istanza gestita SQL di Azure secondario con le pianificazioni inizialmente disabilitate. Usare SSMS per completare i passaggi seguenti.

   1. Per ogni processo SSIS, fare clic con il pulsante destro del mouse e selezionare il **processo script come**, **Crea in** e nuovo menu a discesa **finestra Editor di query** per generare lo script.

      ![Genera script processo SSIS](media/configure-bcdr-azure-ssis-integration-runtime/generate-ssis-job-script.png)

   1. Per ogni script del processo SSIS generato, trovare il comando da eseguire `sp_add_job` stored procedure e modificare/rimuovere l'assegnazione di valore all' `@owner_login_name` argomento, se necessario.

   1. Per ogni script di processo SSIS aggiornato, eseguirlo sul Istanza gestita SQL di Azure secondario per copiare il processo con i passaggi del processo e le pianificazioni associate.

   1. Usando lo script seguente, creare un nuovo processo T-SQL per abilitare o disabilitare le pianificazioni dei processi SSIS in base al ruolo SSISDB primario/secondario, rispettivamente, nelle istanze gestite di SQL Azure primarie e secondarie ed eseguirle regolarmente. Quando si verifica il failover di SSISDB, le pianificazioni di processo SSIS disabilitate verranno abilitate e viceversa.

      ```sql
      IF (SELECT Top 1 role_desc FROM SSISDB.sys.dm_geo_replication_link_status WHERE partner_database = 'SSISDB') = 'PRIMARY'
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 0
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 1
         END
      ELSE
         BEGIN
            IF (SELECT enabled FROM msdb.dbo.sysschedules WHERE schedule_id = <ScheduleID>) = 1
               EXEC msdb.dbo.sp_update_schedule @schedule_id = <ScheduleID >, @enabled = 0
         END
      ```

1. Se si [USA ADF per l'orchestrazione o la pianificazione delle esecuzioni dei pacchetti](./how-to-invoke-ssis-package-ssis-activity.md), assicurarsi che tutte le pipeline di ADF pertinenti con le attività Esegui pacchetto SSIS e i trigger associati vengano copiate nel file ADF secondario con i trigger inizialmente disabilitati. Quando si verifica il failover di SSISDB, è necessario abilitarli.

1. È possibile [testare il gruppo di failover di SQL istanza gestita di Azure](https://docs.microsoft.com/azure/azure-sql/managed-instance/failover-group-add-instance-tutorial?tabs=azure-portal#test-failover) e controllare [Azure-SSIS IR pagina di monitoraggio nel portale di](./monitor-integration-runtime.md#monitor-the-azure-ssis-integration-runtime-in-azure-portal) Azure Data Factory, se l'IRS Azure-SSIS primaria e secondaria hanno ruoli scambiati. 

## <a name="attach-a-new-azure-ssis-ir-to-existing-ssisdb-hosted-by-azure-sql-databasemanaged-instance"></a>Alleghi un nuovo Azure-SSIS IR a SSISDB esistente ospitato dal database SQL di Azure/Istanza gestita

Se si verifica un'emergenza e si influisca sul Azure-SSIS IR esistente ma non sul database SQL di Azure/Istanza gestita nella stessa area, è possibile sostituirlo con uno nuovo in un'altra area. Per aggiungere il database SSISDB esistente ospitato dal database SQL di Azure/Istanza gestita a una nuova Azure-SSIS IR, completare i passaggi seguenti.

1. Se il Azure-SSIS IR esistente è ancora in esecuzione, è necessario arrestarlo prima usando l'interfaccia utente portale di Azure/ADF o Azure PowerShell. Se l'emergenza influisca anche sull'ADF nella stessa area, è possibile ignorare questo passaggio.

1. Con SSMS, eseguire il comando seguente per SSISDB nel database SQL di Azure/Istanza gestita per aggiornare i metadati che consentiranno le connessioni dal nuovo ADF/Azure-SSIS IR.

   ```sql
   EXEC [catalog].[failover_integration_runtime] @data_factory_name = 'YourNewADF', @integration_runtime_name = 'YourNewAzureSSISIR'
   ```

1. Usando l' [interfaccia utente di portale di Azure/ADF](./create-azure-ssis-integration-runtime.md#use-the-azure-portal-to-create-an-integration-runtime) o [Azure PowerShell](./create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime), creare rispettivamente la nuova ADF/Azure-SSIS IR denominata *YourNewADF* / *YourNewAzureSSISIR* in un'altra area. Se si usa l'interfaccia utente portale di Azure/ADF, è possibile ignorare l'errore di connessione test nella pagina **impostazioni di distribuzione** del riquadro di **installazione di Integration Runtime** .

## <a name="next-steps"></a>Passaggi successivi

È possibile prendere in considerazione queste altre opzioni di configurazione per il Azure-SSIS IR:

- [Configurare gli archivi pacchetti per il Azure-SSIS IR](./create-azure-ssis-integration-runtime.md#creating-azure-ssis-ir-package-stores)

- [Configurare le configurazioni personalizzate per la Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)

- [Configurare l'inserimento di reti virtuali per la Azure-SSIS IR](./join-azure-ssis-integration-runtime-virtual-network.md)

- [Configurare il runtime di integrazione self-hosted come proxy per la Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)
