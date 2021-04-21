---
title: Connettori gestiti per App per la logica di Azure
description: Usare trigger e azioni gestiti da Microsoft per creare flussi di lavoro automatizzati che integrano altre app, dati, servizi e sistemi usando App per la logica di Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: d50cf1009fb674f2d3d5914715dcbd9699565489
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796925"
---
# <a name="managed-connectors-for-logic-apps"></a>Connettori gestiti per App per la logica

[I connettori](apis-list.md) gestiti consentono di accedere ad altri servizi e sistemi in cui [trigger](built-in.md) e azioni predefiniti non sono disponibili. È possibile usare questi trigger e azioni per creare flussi di lavoro che integrano dati, app, servizi basati sul cloud e sistemi locali. Rispetto ai trigger e alle azioni predefiniti, questi connettori sono in genere associati a un servizio o a un sistema specifico, ad esempio server Archiviazione BLOB di Azure, Office 365, SQL, Salesforce o SFTP. Gestiti da Microsoft e ospitati in Azure, i connettori gestiti richiedono in genere prima di tutto di creare una connessione dal flusso di lavoro e autenticare l'identità. Sono disponibili trigger basati sulla ricorrenza e basati su webhook, quindi se si usa un trigger basato sulla ricorrenza, vedere panoramica del comportamento [di ricorrenza.](apis-list.md#recurrence-behavior)

Per alcuni servizi, sistemi e protocolli, ad esempio bus di servizio di Azure, Funzioni di Azure, SQL, AS2 e così via, App per la logica offre anche versioni predefinite. Il numero e l'intervallo variano a seconda che si crei un'app per la logica multi-tenant o un'app per la logica a tenant singolo. In alcuni casi sono disponibili sia una versione predefinita che una versione del connettore gestito. Nella maggior parte dei casi, la versione predefinita offre prestazioni, funzionalità, prezzi e così via migliori. Ad esempio, per scambiare messaggi B2B usando il protocollo [AS2,](../logic-apps/logic-apps-enterprise-integration-as2.md)selezionare la versione predefinita, a meno che non siano necessarie funzionalità di rilevamento, disponibili solo nella versione del connettore gestito (deprecata).

Alcuni connettori gestiti per App per la logica appartengono a più sottocategorie. Ad esempio, il connettore SAP è sia [un connettore aziendale](#enterprise-connectors) che [un connettore locale.](#on-premises-connectors)

* [I connettori standard](#standard-connectors) consentono l'accesso a servizi come Archiviazione BLOB di Azure, Office 365, SharePoint, Salesforce, Power BI, OneDrive e molti altri.
* [I connettori](#on-premises-connectors) locali consentono l'accesso a sistemi locali come SQL Server, SharePoint Server, SAP, Oracle DB, condivisioni file e altri.
* I connettori di [account](#integration-account-connectors) di integrazione consentono di trasformare e convalidare xml, codificare e decodificare file flat ed elaborare messaggi business-to-business (B2B) usando protocolli AS2, EDIFACT e X12. 

## <a name="standard-connectors"></a>Connettori Standard

App per la logica di Azure questi connettori Standard più diffusi per la creazione di flussi di lavoro automatizzati usando questi servizi e sistemi. Alcuni connettori Standard supportano [anche sistemi locali](#on-premises-connectors) o account di [integrazione](#integration-account-connectors).

Alcuni connettori Standard di App per la logica [supportano i sistemi locali](#on-premises-connectors) o gli [account di integrazione](#integration-account-connectors).

:::row:::
    :::column:::
        [![bus di servizio di Azure'icona del connettore gestito in App per la logica][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**bus di servizio di Azure**][azure-service-bus-doc]
        \
        \
        Gestire i messaggi asincroni, le sessioni e le sottoscrizioni agli argomenti con il connettore più utilizzato nelle app per la logica.
    :::column-end:::
    :::column:::
        [![SQL Server'icona del connettore gestito in App per la logica][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
        \
        \
        Connettersi al SQL Server locale o a un database SQL di Azure nel cloud in modo da poter gestire record, eseguire stored procedure o eseguire query.
    :::column-end:::
    :::column:::
        [![Icona del connettore gestito di Archiviazione blog di Azure in App per la logica][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Archiviazione blog di Azure**][azure-blob-storage-doc]
        \
        \
        Connettersi all'account Archiviazione di Azure per poter creare e gestire il contenuto BLOB.
    :::column-end:::
    :::column:::
        [![Icona del connettore gestito di Office 365 Outlook in App per la logica][office-365-outlook-icon]][office-365-outlook-doc]
        \
        \
        [**Office 365 Outlook**][office-365-outlook-doc]
        \
        \
        Connettersi all'account di posta elettronica aziendale o dell'istituto di istruzione per creare e gestire messaggi di posta elettronica, attività, eventi e riunioni del calendario, contatti, richieste e altro ancora.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icona del connettore gestito STFP-SSH in App per la logica][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**STFP-SSH**][sftp-ssh-doc]
        \
        \
        Connettersi ai server con FTP sicuro a cui è possibile accedere da Internet per usare SSH in modo da poter lavorare con file e cartelle.
    :::column-end:::
    :::column:::
        [![Icona del connettore gestito di SharePoint Online in App per la logica][sharepoint-online-icon]][sharepoint-online-doc]
        \
        \
        [**SharePoint Online**][sharepoint-online-doc]
        \
        \
        Connettersi a SharePoint Online in modo che sia possibile gestire file, allegati, cartelle e altro ancora.
    :::column-end:::
    :::column:::
        [![Icona del connettore gestito code di Azure in App per la logica][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Code di Azure**][azure-queues-doc]
        \
        \
        Connettersi all'account Archiviazione di Azure per poter creare e gestire code e messaggi.
    :::column-end:::
    :::column:::
        [![Icona del connettore gestito tramite FTP in App per la logica][ftp-icon]][ftp-doc]
        \
        \
        [**Ftp**][ftp-doc]
        \
        \
        Connettersi ai server FTP a cui è possibile accedere da Internet in modo da poter usare file e cartelle.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icona del connettore gestito dal file system in App per la logica][file-system-icon]][file-system-doc]
        \
        \
        [**File System**][file-system-doc]
        \
        \
        Connettersi alla condivisione file locale per poter creare e gestire i file.
    :::column-end:::
    :::column:::
        [![Hub eventi di Azure connettore gestito in App per la logica][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Hub eventi di Azure**][azure-event-hubs-doc]
        \
        \
        Utilizzare e pubblicare eventi tramite un hub eventi. È ad esempio possibile ottenere output dall'app per la logica con gli hub eventi e quindi inviare questo output a un provider di analisi in tempo reale.
    :::column-end:::
    :::column:::
        [![Griglia di eventi di Azure'icona del connettore gestito in App per la logica][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Griglia di eventi di Azure**][azure-event-grid-doc]
        \
        \
        Monitorare gli eventi pubblicati dalla griglia di eventi, ad esempio, quando le risorse di Azure o le risorse di terze parti vengono modificate.
    :::column-end:::
    :::column:::
        [![Icona del connettore gestito salesforce in App per la logica][salesforce-icon]][salesforce-doc]
        \
        \
        [**Salesforce**][salesforce-doc]
        \
        \
        Connettersi all'account Salesforce per creare e gestire elementi come record, processi, oggetti e altro ancora.
    :::column-end:::
:::row-end:::


## <a name="on-premises-connectors"></a>Connettori locali

Prima di creare una connessione a un sistema locale, è necessario innanzitutto [scaricare, installare e configurare un gateway dati locale][gateway-doc]. Questo gateway offre un canale di comunicazione sicuro senza dover configurare l'infrastruttura di rete necessaria. 

I connettori seguenti sono alcuni connettori [Standard](#standard-connectors) di uso comune forniti da App per la logica per l'accesso ai dati e alle risorse nei sistemi locali. Per l'elenco dei connettori locali, vedere [Origini dati supportate.](../logic-apps/logic-apps-gateway-connection.md#supported-connections)

:::row:::
    :::column:::
        [![Icona del connettore locale biztalk Server in App per la logica][biztalk-server-icon]][biztalk-server-doc]
        \
        \
        [**Biztalk Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore locale file system in App per la logica][file-system-icon]][file-system-doc]
        \
        \
        [**File System**][file-system-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore IBM Db2 locale in App per la logica][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM Db2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore IBM Informix locale in App per la logica][ibm-informix-icon]][ibm-informix-doc]
        \
        \
        [**IBM Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icona del connettore mySQL locale in App per la logica][mysql-icon]][mysql-doc]
        \
        \
        [**MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Oracle DB'icona del connettore locale in App per la logica][oracle-db-icon]][oracle-db-doc]
        \
        \
        [**Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore postgreSQL locale in App per la logica][postgre-sql-icon]][postgre-sql-doc]
        \
        \
        [**Postgresql**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore locale di SharePoint Server in App per la logica][sharepoint-server-icon]][sharepoint-server-doc]
        \
        \
        [**SharePoint Server**][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SQL Server'icona del connettore locale in App per la logica][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore Teradata locale in App per la logica][teradata-icon]][teradata-doc]
        \
        \
        [**Teradata**][teradata-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="integration-account-connectors"></a>Connettori dell'account di integrazione

I connettori di account di integrazione supportano in modo specifico scenari di comunicazione [business-to-business (B2B)](../logic-apps/logic-apps-enterprise-integration-overview.md) in App per la logica di Azure. Dopo aver creato un account di integrazione e aver definito gli artefatti B2B, ad esempio partner commerciali, contratti, mappe e schemi, è possibile usare i connettori [dell'account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) di integrazione per codificare e decodificare i messaggi, trasformare il contenuto e altro ancora.

Ad esempio, se si usa Microsoft BizTalk Server, è possibile creare una connessione dal flusso di lavoro usando il connettore [BizTalk Server locale](#on-premises-connectors). È quindi possibile estendere o eseguire operazioni simili a BizTalk nel flusso di lavoro usando questi connettori dell'account di integrazione.

> [!NOTE]
> Prima di poter usare i connettori dell'account di integrazione, è necessario [collegare l'app per la logica a un account di integrazione.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)


:::row:::
    :::column:::
        [![Icona dell'azione di decodifica AS2 in App per la logica][as2-icon]][as2-doc]
        \
        \
        [**Decodifica AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Icona dell'azione di codifica AS2 in App per la logica][as2-icon]][as2-doc]
        \
        \
        [**Codifica AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Icona dell'azione di decodifica EDIFACT in App per la logica][edifact-icon]][edifact-decode-doc]
        \
        \
        [**Decodifica EDIFACT**][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![Icona dell'azione di codifica EDIFACT in App per la logica][edifact-icon]][edifact-encode-doc]
        \
        \
        [**Codifica EDIFACT**][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icona dell'azione di decodifica file flat in App per la logica][flat-file-decode-icon]][flat-file-decode-doc]
        \
        \
        [**Decodifica file flat**][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![Icona dell'azione di codifica file flat in App per la logica][flat-file-encode-icon]][flat-file-encode-doc]
        \
        \
        [**Codifica di file flat**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![Icona di azione dell'account di integrazione in App per la logica][integration-account-icon]][integration-account-doc]
        \
        \
        [**Account di integrazione**][integration-account-doc]
    :::column-end:::
    :::column:::
        [![Icona dell'azione Liquid transforms in App per la logica][liquid-icon]][json-liquid-transform-doc]
        \
        \
        [**Trasformazioni liquide**][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icona dell'azione di decodifica X12 in App per la logica][x12-icon]][x12-decode-doc]
        \
        \
        [**Decodifica X12**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![Icona dell'azione di codifica X12 in App per la logica][x12-icon]][x12-encode-doc]
        \
        \
        [**Codifica X12**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![Icona di azione Trasforma XML in App per la logica][xml-transform-icon]][xml-transform-doc]
        \
        \
        [**Trasformazioni XML**][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![Icona dell'azione di convalida XML in App per la logica][xml-validate-icon]][xml-validate-doc]
        \
        \
        [**Convalida XML**][xml-validate-doc]
    :::column-end:::
:::row-end:::

## <a name="enterprise-connectors"></a>Connettori aziendali

I connettori seguenti forniscono l'accesso ai sistemi aziendali per un costo aggiuntivo:

:::row:::
    :::column:::
        [![Icona del connettore IBM 3270 Enterprise in App per la logica][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**CONNETTORE IBM 3270** Enterprise][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore IBM MQ Enterprise in App per la logica][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**CONNETTORE IBM MQ** Enterprise][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Icona di SAP Enterprise Connector in App per la logica][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** Enterprise Connector][sap-connector-doc]
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::


## <a name="ise-connectors"></a>Connettori ISE

In un ambiente del servizio di integrazione (ISE), questi connettori gestiti hanno anche versioni [ISE](apis-list.md#ise-and-connectors), che hanno funzionalità diverse rispetto alle versioni multi-tenant:

> [!NOTE]
> Le app per la logica eseguite in un ISE e nei relativi connettori, indipendentemente dalla posizione in cui vengono eseguiti tali connettori, seguono un piano tariffario fisso rispetto al piano tariffario basato sul consumo. Per altre informazioni, vedere Modello tariffario [di App per la logica](../logic-apps/logic-apps-pricing.md) e Dettagli sui prezzi di App per la [logica.](https://azure.microsoft.com/pricing/details/logic-apps/)

:::row:::
    :::column:::
        [![Icona del connettore AS2 ISE in App per la logica][as2-icon]][as2-doc]
        \
        \
        [**AS2** Ise][as2-doc]
    :::column-end:::
    :::column:::
        [![Automazione di Azure'icona del connettore ISE in App per la logica][azure-automation-icon]][azure-automation-doc]
        \
        \
        [**Automazione di Azure** Ise][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Archiviazione BLOB di Azure'icona del connettore ISE in App per la logica][azure-blob-storage-icon]][azure-blob-storage-doc]
        \
        \
        [**Archiviazione BLOB di Azure** Ise][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Cosmos DB'icona del connettore ISE in App per la logica][azure-cosmos-db-icon]][azure-cosmos-db-doc]
        \
        \
        [**Azure Cosmos DB** Ise][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Hub eventi di Azure'icona del connettore ISE in App per la logica][azure-event-hubs-icon]][azure-event-hubs-doc]
        \
        \
        [**Hub eventi di Azure** Ise][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Griglia di eventi di Azure'icona del connettore ISE in App per la logica][azure-event-grid-icon]][azure-event-grid-doc]
        \
        \
        [**Griglia di eventi di Azure** Ise][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore ISE di Archiviazione file di Azure in App per la logica][azure-file-storage-icon]][azure-file-storage-doc]
        \
        \
        [**Archiviazione file di Azure** Ise][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Azure Key Vault icona del connettore ISE in App per la logica][azure-key-vault-icon]][azure-key-vault-doc]
        \
        \
        [**Azure Key Vault** Ise][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Monitoraggio di Azure log icona del connettore ISE in App per la logica][azure-monitor-logs-icon]][azure-monitor-logs-doc]
        \
        \
        [**Monitoraggio di Azure log** Ise][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![bus di servizio di Azure connettore ISE in App per la logica][azure-service-bus-icon]][azure-service-bus-doc]
        \
        \
        [**bus di servizio di Azure** Ise][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Azure Synapse Analytics'icona del connettore ISE in App per la logica][azure-sql-data-warehouse-icon]][azure-sql-data-warehouse-doc]
        \
        \
        [**Azure Synapse Analytics** Ise][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore ISE di Archiviazione tabelle di Azure in App per la logica][azure-table-storage-icon]][azure-table-storage-doc]
        \
        \
        [**Archiviazione tabelle di Azure** Ise][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icona del connettore ISE delle code di Azure in App per la logica][azure-queues-icon]][azure-queues-doc]
        \
        \
        [**Code di Azure** Ise][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore EDIFACT ISE in App per la logica][edifact-icon]][edifact-doc]
        \
        \
        [**EDIFACT** Ise][edifact-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore ISE del file system in App per la logica][file-system-icon]][file-system-doc]
        \
        \
        [**File system** Ise][file-system-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore ISE FTP in App per la logica][ftp-icon]][ftp-doc]
        \
        \
        [**FTP** Ise][ftp-doc]
    :::column-end:::
:::row-end:::   
:::row:::
    :::column:::
        [![Icona del connettore IBM 3270 ISE in App per la logica][ibm-3270-icon]][ibm-3270-doc]
        \
        \
        [**IBM 3270** Ise][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore IBM DB2 ISE in App per la logica][ibm-db2-icon]][ibm-db2-doc]
        \
        \
        [**IBM DB2** Ise][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore IBM MQ ISE in App per la logica][ibm-mq-icon]][ibm-mq-doc]
        \
        \
        [**IBM MQ** Ise][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore SAP ISE in App per la logica][sap-icon]][sap-connector-doc]
        \
        \
        [**SAP** Ise][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Icona del connettore SFTP-SSH ISE in App per la logica][sftp-ssh-icon]][sftp-ssh-doc]
        \
        \
        [**SFTP-SSH** Ise][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore ISE SMTP in App per la logica][smtp-icon]][smtp-doc]
        \
        \
        [**SMTP** Ise][smtp-doc]
    :::column-end:::
    :::column:::
        [![SQL Server'icona del connettore ISE in App per la logica][sql-server-icon]][sql-server-doc]
        \
        \
        [**SQL Server** Ise][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Icona del connettore X12 ISE in App per la logica][x12-icon]][x12-doc]
        \
        \
        [**X12** Ise][x12-doc]
    :::column-end:::
:::row-end:::

Per altre informazioni, vedere gli argomenti seguenti:

* [Accesso alle risorse di rete virtuale di Azure da App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md)
* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare API personalizzate che è possibile chiamare da App per la logica](/logic-apps/logic-apps-create-api-app)

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png


<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Creare e gestire processi di automazione per il cloud e l'infrastruttura locale"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gestire i file in un contenitore BLOB con il connettore di archiviazione BLOB di Azure"
[azure-cosmos-db-doc]: /connectors/documentdb/ "Connettersi a Azure Cosmos DB in modo da poter accedere a documenti e stored procedure"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorare gli eventi pubblicati da Griglia di eventi, ad esempio quando le risorse di Azure o le risorse di terze parti cambiano"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Connettersi a Hub eventi di Azure in modo da poter ricevere e inviare eventi tra app per la logica e Hub eventi"
[azure-file-storage-doc]: /connectors/azurefile/ "Connettersi all'account Archiviazione di Azure per creare, aggiornare, ottenere ed eliminare file"
[azure-key-vault-doc]: /connectors/keyvault/ "Connettersi al Azure Key Vault per poter gestire segreti e chiavi"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Eseguire query sui log Monitoraggio di Azure log tra aree di lavoro Log Analytics e Application Insights componenti"
[azure-queues-doc]: /connectors/azurequeues/ "Connettersi all'account Archiviazione di Azure per poter creare e gestire code e messaggi"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Inviare messaggi da code e argomenti del bus di servizio e ricevere messaggi da code e sottoscrizioni del bus di servizio"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Connettersi Azure Synapse Analytics per poter visualizzare i dati"
[azure-table-storage-doc]: /connectors/azuretables/ "Connettersi all'account Archiviazione di Azure per creare, aggiornare ed eseguire query su tabelle e altro ancora"
[biztalk-server-doc]: /connectors/biztalk/ "Connettersi al BizTalk Server in modo che sia possibile eseguire applicazioni basate su BizTalk side-by-side con App per la logica di Azure"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Connettersi a un file system locale"
[ftp-doc]: ./connectors-create-api-ftp.md "Connettersi a un server FTP/FTPS ed eseguire diverse attività FTP, come caricare, recuperare ed eliminare file e altro ancora"
[github-doc]: ./connectors-create-api-github.md "Connettersi a GitHub e tenere traccia dei problemi"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Si connette a Google Calendar e può gestire il calendario"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Connettersi a Fogli Google per poter modificare i fogli"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Si connette a Google Tasks per poter gestire le attività"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Connettersi alle app 3270 nei mainframe IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Connettersi a IBM DB2 nel cloud o in locale. Aggiornare una riga, ottenere una tabella e altro ancora"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Connettersi a Informix nel cloud o in locale. Leggere una riga, elencare le tabelle e altro ancora"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Connettersi a IBM MQ in locale o in Azure per inviare e ricevere messaggi"
[instagram-doc]: ./connectors-create-api-instagram.md "Connettersi a Unie. Attivare o agire sugli eventi"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Connettersi a Mandrill per la comunicazione"
[mysql-doc]: /connectors/mysql/ "Connettersi al database MySQL locale per poter leggere e scrivere dati"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Connettersi all'account aziendale o dell'istituto di istruzione per poter inviare e ricevere messaggi di posta elettronica, gestire il calendario e i contatti e altro ancora"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Connettersi a Microsoft OneDrive personale per caricare, eliminare, elencare file e altro ancora"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Connettersi a Microsoft OneDrive aziendale per caricare, eliminare, elencare i file e altro ancora"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Connettersi a un database Oracle per aggiungere, inserire, eliminare righe e altro ancora"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Connettersi alla cassetta postale di Outlook per poter gestire posta elettronica, calendari, contatti e altro ancora"
[postgre-sql-doc]: /connectors/postgresql/ "Connettersi al database PostgreSQL per poter leggere i dati dalle tabelle"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Connettersi all'account Salesforce. Gestire account, lead, opportunità e altro ancora"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Connettersi a un sistema SAP locale"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Connettersi a SendGrid. Inviare messaggi di posta elettronica e gestire gli elenchi di destinatari"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Connettersi all'account SFTP usando SSH. Caricare, ottenere, eliminare file e altro ancora"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "Connettersi al server sharepoint locale. Gestire documenti, elementi di elenco e altro ancora"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "Connettersi a SharePoint Online. Gestire documenti, elementi di elenco e altro ancora"
[slack-doc]: ./connectors-create-api-slack.md "Connettersi a Slack e pubblicare messaggi nei relativi canali"
[smtp-doc]: ./connectors-create-api-smtp.md "Connettersi a un server SMTP e inviare messaggi di posta elettronica con allegati"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Connettersi a SparkPost per la comunicazione"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Connettersi a database SQL di Azure o SQL Server. Creare, aggiornare, ottenere ed eliminare voci in una tabella di database SQL"
[teradata-doc]: /connectors/teradata/ "Connettersi al database Teradata per leggere i dati dalle tabelle"
[twilio-doc]: ./connectors-create-api-twilio.md "Connettersi a Twilio. Inviare e ricevere messaggi, ottenere i numeri disponibili, gestire i numeri di telefono in ingresso e altro ancora"
[youtube-doc]: ./connectors-create-api-youtube.md "Connettersi a YouTube. Gestire video e canali"

<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Codificare e decodificare i messaggi che usano il protocollo AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Codificare e decodificare i messaggi che usano il protocollo EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Decodificare i messaggi che usano il protocollo EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Codificare i messaggi che usano il protocollo EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informazioni sul file flat di integrazione aziendale"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informazioni sul file flat di integrazione aziendale"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Gestire i metadati per gli artefatti dell'account di integrazione"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Trasformare JSON con modelli Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Codificare e decodificare i messaggi che usano il protocollo X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Decodificare i messaggi che usano il protocollo X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Codificare i messaggi che usano il protocollo X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Trasformare i messaggi XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Convalidare i messaggi XML"


<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Connettersi a origini dati in locale da app per la logica con il gateway dati locale"



<!--Integration account connector icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!-- Integration account connector docs -->

[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Codificare e decodificare messaggi che usano il protocollo AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Codificare e decodificare messaggi che usano il protocollo EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Decodificare i messaggi che usano il protocollo EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Codificare i messaggi che usano il protocollo EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informazioni sul file flat di Enterprise Integration"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informazioni sul file flat di Enterprise Integration"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Gestire i metadati per gli artefatti dell'account di integrazione"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Trasformare JSON con i modelli Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Codificare e decodificare messaggi che usano il protocollo X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Decodificare i messaggi che usano il protocollo X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Codificare i messaggi che usano il protocollo X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Trasformare i messaggi XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Convalidare i messaggi XML"
