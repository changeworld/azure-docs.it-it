---
title: Distribuire Monitoraggio di Azure soluzioni SAP con il portale di Azure
description: Distribuire Monitoraggio di Azure soluzioni SAP con il portale di Azure
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.date: 08/17/2020
ms.openlocfilehash: 096f0425a6893d68341b97c821481fa0adf2f95c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375271"
---
# <a name="deploy-azure-monitor-for-sap-solutions-with-azure-portal"></a>Distribuire Monitoraggio di Azure per soluzioni SAP con portale di Azure

Monitoraggio di Azure per soluzioni SAP risorse possono essere create tramite il [portale di Azure](https://azure.microsoft.com/features/azure-portal). Questo metodo fornisce un'interfaccia utente basata su browser per distribuire Monitoraggio di Azure per soluzioni SAP e configurare i provider.

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="create-monitoring-resource"></a>Creare una risorsa di monitoraggio

1. Selezionare **Monitoraggio di Azure per soluzioni SAP** da **Azure Marketplace**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="L'immagine mostra la selezione Monitoraggio di Azure'offerta di soluzioni SAP Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. Nella scheda **Informazioni di** base specificare i valori necessari. Se applicabile, è possibile usare un'area di lavoro Log Analytics esistente.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Visualizzazione delle opzioni portale di Azure di configurazione." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

3. Quando si seleziona una rete virtuale, assicurarsi che i sistemi da monitorare siano raggiungibili dall'interno di tale rete virtuale. 

   > [!IMPORTANT]
   > La **selezione di** Condividi per la condivisione dei dati con Microsoft consente ai team di supporto di offrire supporto aggiuntivo.

## <a name="configure-providers"></a>Configurare provider

### <a name="sap-hana-provider"></a>SAP HANA provider 

1. Selezionare la **scheda** Provider per aggiungere i provider da configurare. È possibile aggiungere più provider uno dopo l'altro o aggiungerli dopo aver distribuito la risorsa di monitoraggio. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Mostra la scheda del provider per aggiungere altri provider al Monitoraggio di Azure per soluzioni SAP." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

2. Selezionare **Aggiungi provider** e scegliere **SAP HANA** dall'elenco a discesa. 

   > [!IMPORTANT]
   > Assicurarsi che SAP HANA provider sia configurato per SAP HANA nodo "master".

3. Immettere l'indirizzo IP privato per il server HANA.

4. Immettere il nome del tenant del database che si vuole usare. È tuttavia possibile scegliere qualsiasi tenant, è consigliabile usare **SYSTEMDB** perché consente una gamma più ampia di aree di monitoraggio. 

5. Immettere il numero di porta SQL associato al database HANA. Il numero di porta deve essere nel formato **[3]**  +  **[istanza#]**  +  **[13]**. Ad esempio, 30013. 

6. Immettere il nome utente del database da usare. Assicurarsi che all'utente del database siano assegnati **i** ruoli di monitoraggio **e lettura** del catalogo. 

7. Al termine, selezionare **Aggiungi provider**. Continuare ad aggiungere altri provider in base alle esigenze o selezionare **Rivedi e crea** per completare la distribuzione.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Immagine delle opzioni di configurazione quando si aggiungono informazioni sul provider." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

### <a name="high-availability-cluster-pacemaker-provider"></a>Provider di cluster a disponibilità elevata (Pacemaker)

1. Selezionare **Cluster a disponibilità elevata (Pacemaker)** nell'elenco a discesa. 

   > [!IMPORTANT]
   > Per configurare il provider del cluster a disponibilità elevata (Pacemaker), assicurarsi che ha_cluster_provider sia installato in ogni nodo. Per altre informazioni, vedere [l'esportatore di cluster a lungo](https://github.com/ClusterLabs/ha_cluster_exporter#installation)

2. Immettere l'endpoint Prometheus sotto forma di http://IP:9664/metrics . 
 
3. Immettere l'ID di sistema (SID), il nome host e il nome del cluster.

4. Al termine, selezionare **Aggiungi provider**. Continuare ad aggiungere altri provider in base alle esigenze o selezionare **Rivedi e crea** per completare la distribuzione.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-5.png" alt-text="Immagine che mostra le opzioni correlate al provider Pacemaker del cluster a lungo andare." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-5.png":::


### <a name="os-linux-provider"></a>Provider del sistema operativo (Linux) 

1. Selezionare Sistema operativo (Linux) dall'elenco a discesa 

>[!IMPORTANT]
> Per configurare il provider del sistema operativo (Linux), assicurarsi che sia installata la versione più recente di Node_Exporter in ogni host (BareMetal o VM) che si vuole monitorare. Usare questo [collegamento] ( per https://prometheus.io/download/#node_exporter) trovare la versione più recente. Per altre informazioni, vedere [Node_Exporter](https://github.com/prometheus/node_exporter)

2. Immettere un nome, che sarà l'identificatore per l'istanza BareMetal.
3. Immettere l'endpoint dell'esportatore di nodi nel formato http://IP:9100/metrics .

>[!IMPORTANT]
> Usare l'indirizzo IP privato dell'host Linux. Assicurarsi che l'host e la risorsa AMS siano nella stessa rete virtuale. 

>[!Note]
> La porta del firewall "9100" deve essere aperta nell'host Linux.
>Se si usa firewall-cmd: firewall-cmd --permanent --add-port=9100/tcp firewall-cmd --reload Se si usa ufw: ufw allow 9100/tcp ufw reload

>[!Tip]
> Se l'host Linux è una macchina virtuale di Azure, assicurarsi che tutti i gruppi di sicurezza di rete applicabili consentano il traffico in ingresso sulla porta 9100 da "VirtualNetwork" come origine.
 
5. Al termine, selezionare **Aggiungi provider.** Continuare ad aggiungere altri provider in base alle esigenze o selezionare **Rivedi e crea**   per completare la distribuzione. 


### <a name="microsoft-sql-server-provider"></a>Provider Microsoft SQL Server

1. Prima di aggiungere il provider Microsoft SQL Server, è necessario eseguire lo script seguente in SQL Server Management Studio per creare un utente con le autorizzazioni appropriate necessarie per configurare il provider.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

2. Selezionare **Aggiungi provider** e **Microsoft SQL Server** dall'elenco a discesa. 

3. Compilare i campi usando le informazioni associate al Microsoft SQL Server. 

4. Al termine, selezionare **Aggiungi provider.** Continuare ad aggiungere altri provider in base alle esigenze o selezionare **Rivedi e crea** per completare la distribuzione.

     :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="L'immagine mostra le informazioni relative all'aggiunta Microsoft SQL Server provider." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::

### <a name="sap-netweaver-provider"></a>Provider SAP NetWeaver

#### <a name="pre-requisites-for-adding-netweaver-provider"></a>Prerequisiti per l'aggiunta del provider NetWeaver

Il "servizio di avvio SAP" offre una serie di servizi, tra cui il monitoraggio del sistema SAP. Si sta sfruttando "SAPControl", ovvero un'interfaccia del servizio Web SOAP che espone queste funzionalità. Questa interfaccia del servizio Web SAPControl distingue tra [metodi di](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv) servizio Web protetti e non protetti. Per poter recuperare metriche specifiche, è necessario rimuovere la protezione di alcuni metodi. Per rimuovere la protezione dei metodi necessari per la versione corrente, seguire la procedura seguente per ogni sistema SAP: -

1. Aprire una connessione GUI SAP al server SAP
2. Accedere con un account amministrativo
3. Eseguire la transazione RZ10
4. Selezionare il profilo appropriato (DEFAULT. PFL)
5. Selezionare "Manutenzione estesa" e fare clic su Cambia 
6. Modificare il valore del parametro interessato "service/protectedwebmethods" in "SDEFAULT -GetQueueStatistic –ABAPGetWPTable –EnqGetStatistic –GetProcessList" nell'impostazione consigliata e fare clic su Copia
7. Tornare indietro e selezionare Profile->Save
8. Riavviare il sistema per l'applicazione del parametro

#### <a name="installing-netweaver-provider-on-the-azure-portal"></a>Installazione del provider NetWeaver nel portale di Azure
1.  Assicurarsi che i passaggi necessari siano stati completati e che il server sia stato riavviato
2.  Nel portale di Azure, in AMS, selezionare Aggiungi provider e scegliere SAP NetWeaver dall'elenco a discesa
3.  Immettere il nome host del sistema SAP e del sottodominio (se applicabile)
4.  Immettere il numero di istanza corrispondente al nome host immesso 
5.  Immettere l'ID di sistema (SID)
6.  Al termine, selezionare Aggiungi provider
7.  Continuare ad aggiungere altri provider in base alle esigenze o selezionare Rivedi e crea per completare la distribuzione

![image](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [Monitoraggio di Azure per soluzioni SAP](azure-monitor-overview.md)
