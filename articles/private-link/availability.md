---
title: Disponibilità collegamento privato di Azure
description: In questo articolo vengono fornite informazioni sui servizi di Azure che supportano il collegamento privato.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 26485c84749b7d4c91159476b3f683c2b0f3831b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555679"
---
# <a name="azure-private-link-availability"></a>Disponibilità collegamento privato di Azure

Collegamento privato di Azure consente di accedere ai servizi PaaS di Azure, ad esempio Archiviazione di Azure e Database SQL, nonché ai servizi di proprietà di clienti/partner ospitati in Azure tramite un [endpoint privato](private-endpoint-overview.md) nella rete virtuale. 

> [!IMPORTANT]
> Collegamento privato di Azure è ora disponibile a livello generale. L'endpoint privato e Collegamento privato (il servizio alla base di Load Balancer Standard) sono disponibili a livello generale. L'onboarding di soluzioni Azure PaaS diverse in Collegamento privato di Azure verrà eseguito in base a pianificazioni diverse. Per informazioni sulle limitazioni note, vedere [Endpoint privato](private-endpoint-overview.md#limitations) e [Servizio Collegamento privato](private-link-service-overview.md#limitations). 

## <a name="service-availability"></a>Disponibilità del servizio

Nelle tabelle seguenti sono elencati i servizi di collegamento privato e le aree in cui sono disponibili. 

### <a name="ai--machine-learning"></a>Intelligenza artificiale e Machine Learning

|Servizi supportati  |Aree disponibili | Altre considerazioni | Stato  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Tutte le aree pubbliche    |  | GA   <br/> [Informazioni su come creare un endpoint privato per Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Analisi

|Servizi supportati  |Aree disponibili | Altre considerazioni | Stato  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Tutte le aree pubbliche <br/> Tutte le aree per enti pubblici |  Supportato per i [criteri di connessione](../azure-sql/database/connectivity-architecture.md#connection-policy) proxy |GA <br/> [Informazioni su come creare un endpoint privato per Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Hub eventi di Azure | Tutte le aree pubbliche<br/>Tutte le aree per enti pubblici      |   | GA   <br/> [Informazioni su come creare un endpoint privato per Hub eventi di Azure.](../event-hubs/private-link-service.md)  |
| Monitoraggio di Azure <br/>(Log Analytics e Application Insights) | Tutte le aree pubbliche      |  | GA   <br/> [Informazioni su come creare un endpoint privato per Monitoraggio di Azure.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina    | Le credenziali devono essere archiviate in un insieme di credenziali delle chiavi di Azure| GA   <br/> [Informazioni su come creare un endpoint privato per Azure Data Factory.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>Calcolo

|Servizi supportati  |Aree disponibili | Altre considerazioni | Stato  |
|:-------------------|:-----------------|:----------------|:--------|
|Configurazione app di Azure | Tutte le aree pubbliche      |  | Anteprima  </br> [Informazioni su come creare un endpoint privato per Configurazione app di Azure.](../azure-app-configuration/concept-private-endpoint.md) |
|Dischi gestiti di Azure | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina    | [Seleziona per le limitazioni note](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [Informazioni su come creare un endpoint privato per Azure Managed Disks.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Contenitori

|Servizi supportati  |Aree disponibili | Altre considerazioni | Stato  |
|:-------------------|:-----------------|:----------------|:--------|
|Registro Azure Container | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici    | Supportato con il livello Premium del registro contenitori. [Selezionare per informazioni sui livelli di servizio](../container-registry/container-registry-skus.md)| GA   <br/> [Informazioni su come creare un endpoint privato per Registro Azure Container.](../container-registry/container-registry-private-link.md)   |
|Servizio Azure Kubernetes - API Kubernetes | Tutte le aree pubbliche      |  | GA   <br/> [Informazioni su come creare un endpoint privato per il servizio Azure Kubernetes.](../aks/private-clusters.md)   |

### <a name="databases"></a>Database

|Servizi supportati  |Aree disponibili | Altre considerazioni | Stato  |
|:-------------------|:-----------------|:----------------|:--------|
|  Database SQL di Azure         | Tutte le aree pubbliche <br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina      |  Supportato per i [criteri di connessione](../azure-sql/database/connectivity-architecture.md#connection-policy) proxy | GA <br/> [Informazioni su come creare un endpoint privato per Azure SQL.](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici</br> Tutte le aree della Cina | |GA <br/> [Informazioni su come creare un endpoint privato per Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Database di Azure per PostgreSQL - Server singolo         | Tutte le aree pubbliche <br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina     | Supportato per i piani tariffari per utilizzo generico e con ottimizzazione per la memoria | GA <br/> [Informazioni su come creare un endpoint privato per Database di Azure per PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Database di Azure per MySQL         | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina      |  | GA <br/> [Informazioni su come creare un endpoint privato per Database di Azure per MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Database di Azure per MariaDB         | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina     |  | GA <br/> [Informazioni su come creare un endpoint privato per Database di Azure per MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Integrazione

|Servizi supportati  |Aree disponibili | Altre considerazioni | Stato  |
|:-------------------|:-----------------|:----------------|:--------|
|Griglia di eventi di Azure| Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici       |  | GA   <br/> [Informazioni su come creare un endpoint privato per Griglia di eventi di Azure.](../event-grid/network-security.md) |
|Bus di servizio di Azure | Tutte le aree pubbliche<br/>Tutte le aree per enti pubblici  | Supportato con il livello Premium del bus di servizio di Azure. [Selezionare per informazioni sui livelli di servizio](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [Informazioni su come creare un endpoint privato per Bus di servizio di Azure.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Internet delle cose

|Servizi supportati  |Aree disponibili | Altre considerazioni | Stato  |
|:-------------------|:-----------------|:----------------|:--------|
| Hub IoT Azure | Tutte le aree pubbliche    |  | GA   <br/> [Informazioni su come creare un endpoint privato per Hub IoT di Azure.](../iot-hub/virtual-network-support.md) |
|  Gemelli digitali di Azure         | Tutte le aree pubbliche supportate dai dispositivi gemelli digitali di Azure     |  | Anteprima <br/> [Informazioni su come creare un endpoint privato per i dispositivi gemelli digitali di Azure.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Gestione e governance

| Servizi supportati | Aree disponibili | Altre considerazioni | Stato  |
| ------------ | ----------------| ------------| ----------------|
| Automazione di Azure  | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici |  | Anteprima </br> [Informazioni su come creare un endpoint privato per Automazione di Azure.](../automation/how-to/private-link-security.md)|
|Backup di Azure | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici   |  | GA <br/> [Informazioni su come creare un endpoint privato per Backup di Azure.](../backup/private-endpoints.md)   |

### <a name="security"></a>Sicurezza

|Servizi supportati  |Aree disponibili | Altre considerazioni | Stato  |
|:-------------------|:-----------------|:----------------|:--------|
|  Insieme di credenziali chiave di Azure         | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici      |  | GA   <br/> [Informazioni su come creare un endpoint privato per Azure Key Vault.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Archiviazione
|Servizi supportati  |Aree disponibili | Altre considerazioni | Stato  |
|:-------------------|:-----------------|:----------------|:--------|
| Archiviazione BLOB di Azure (con Data Lake Storage Gen2)       |  Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici       |  Supportato nel tipo di account per utilizzo generico V2 | GA <br/> [Informazioni su come creare un endpoint privato per l'archiviazione BLOB.](tutorial-private-endpoint-storage-portal.md)  |
| File di Azure | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici      | |   GA <br/> [Informazioni su come configurare gli endpoint di rete di File di Azure.](../storage/files/storage-files-networking-endpoints.md)   |
| Sincronizzazione file di Azure | Tutte le aree pubbliche      | |   GA <br/> [Informazioni su come configurare gli endpoint di rete di File di Azure.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Archiviazione code di Azure       |  Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici       |  Supportato nel tipo di account per utilizzo generico V2 | GA <br/> [Informazioni su come creare un endpoint privato per l'archiviazione code.](tutorial-private-endpoint-storage-portal.md) |
| Archiviazione tabelle di Azure       |  Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici       |  Supportato nel tipo di account per utilizzo generico V2 | GA <br/> [Informazioni su come creare un endpoint privato per l'archiviazione tabelle.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | Tutte le aree pubbliche, ad eccezione di: Germania CENTRALE, Germania NORD-ORIENTALE <br/> Tutte le aree per enti pubblici  | | GA <br/> [Informazioni su come creare un endpoint privato per Azure Batch.](../batch/private-connectivity.md) |

### <a name="web"></a>Web
|Servizi supportati  |Aree disponibili | Altre considerazioni | Stato  |
|:-------------------|:-----------------|:----------------|:--------|
| Servizio Azure SignalR | STATI UNITI ORIENTALI, STATI UNITI CENTRO-MERIDIONALI,<br/>STATI UNITI OCCIDENTALI 2, tutte le aree della Cina      |  | Anteprima   <br/> [Informazioni su come creare un endpoint privato per il Servizio Azure SignalR.](../azure-signalr/howto-private-endpoints.md)   |
|App Web di Azure | Tutte le aree pubbliche<br/> Cina settentrionale 2 & Est 2    | Supportato con il piano PremiumV2, PremiumV3 o Funzioni Premium  | GA   <br/> [Informazioni su come creare un endpoint privato per App Web di Azure.](./tutorial-private-endpoint-webapp-portal.md)   |
|Ricerca di Azure | Tutte le aree pubbliche <br/> Tutte le aree per enti pubblici | Supportato con il servizio in modalità privata | GA   <br/> [Informazioni su come creare un endpoint privato per Ricerca di Azure.](../search/service-create-private-endpoint.md)    |
|Servizio di inoltro di Azure | Tutte le aree pubbliche      |  | Anteprima <br/> [Informazioni su come creare un endpoint privato per Inoltro di Azure.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Servizio di collegamento privato con un servizio di bilanciamento del carico standard

|Servizi supportati  |Aree disponibili | Altre considerazioni | Stato  |
|:-------------------|:-----------------|:----------------|:--------|
|Servizi di Collegamento privato dietro Azure Load Balancer | Tutte le aree pubbliche<br/> Tutte le aree per enti pubblici<br/>Tutte le aree della Cina  | Supportati in Load Balancer Standard | GA <br/> [Informazioni su come creare un servizio Collegamento privato.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul servizio di collegamento privato di Azure:
- [Che cos'è Collegamento privato di Azure?](private-link-overview.md)
- [Creare un endpoint privato con il portale di Azure](create-private-endpoint-portal.md)
