---
title: Domande frequenti
description: Questo articolo fornisce le risposte alle domande frequenti su Azure.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 94b765cbcbdd81505b08052845207ee1d93a28d9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667813"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Domande frequenti sulle competenze di Azure

## <a name="overview"></a>Panoramica

Molte organizzazioni non hanno una conoscenza olistica dei dati. È difficile comprendere quali dati esistono, dove si trovano i dati e come individuare e accedere ai dati rilevanti. I dati non sono configurati come derivazione, classificazione e metadati completi, rendendo difficile per gli utenti aziendali cercare i dati corretti e utilizzare tali dati in modo appropriato. Di conseguenza, viene usata solo una piccola frazione di dati raccolti per informare le decisioni aziendali. Infine, identificare i problemi di sicurezza dei dati e proteggere i dati sensibili non è coerente. Richiede tempo e impegno continuo, soprattutto mantenendo l'agilità dei dati.

Azure competenza è una soluzione di governance dei dati. Consente ai clienti di acquisire una conoscenza approfondita di tutti i dati mantenendo il controllo sull'utilizzo. Con la competenza di Azure, le organizzazioni scoprono e curano i dati. Ottengono informazioni approfondite sui propri dati e regolano l'accesso ai dati in modo centralizzato.

## <a name="purpose-of-this-faq"></a>Scopo di queste domande frequenti

Queste domande frequenti rispondono a domande comuni che i clienti e i team dei campi chiedono spesso. È concepito per chiarire le domande sull'ambito di competenza di Azure e le soluzioni correlate, ad esempio Azure Data Catalog (ADC) Gen 2 (deprecato) e Azure Information Protection.

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>Quali sono i tipi di origine disponibili per l'analisi e la classificazione dei metadati?

|Azure|Non Azure|
|---------|---------|
|Archiviazione BLOB di Azure|Power BI|
|Azure Synapse Analytics (SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata (disponibile per fine 2020)|
|Istanza gestita di SQL di Azure|SAP ECC (disponibile alla fine del 2020)|
|Esplora dati di Azure|SAP S/4 HANA (disponibile alla fine del 2020)|
|Azure Data Lake Storage Gen1|Metastore hive (disponibile alla fine di 2020)|
|Azure Data Lake Storage Gen2|Amazon S3|
|File di Azure|--|
|Database SQL di Azure|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>Quali sistemi di dati/processori è possibile connettere e ottenere la derivazione?

|Sistema dati/processore 
|---------
|Azure Data Factory: attività di copia, attività flusso di dati 
|Derivazione personalizzata   
|Condivisione dati di Azure   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>In che modo sono correlati ADC gen 2, Azure Information Protection e Azure competenza?

Azure competenza è stata originariamente avviata come ADC gen 2 ma è stata ampliata nell'ambito. Include ora le funzionalità di catalogo avanzate di ADC gen 2 combinate con le funzionalità di classificazione, assegnazione di etichette e criteri di conformità dei dati di Azure Information Protection. Attualmente, è più aderente alla definizione di settore più ampia di governance dei dati.

### <a name="what-happens-to-customers-using-adc-gen-1"></a>Cosa accade ai clienti che usano ADC gen 1?

Azure competenza è l'obiettivo di tutte le innovazioni dei prodotti nello spazio della soluzione del catalogo per Microsoft. ADC gen 1 continuerà a essere supportato.

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>I clienti possono avere più account Azure per l'ambito nella stessa sottoscrizione?

Sì, sono supportati molti account Azure per ogni sottoscrizione e per ogni tenant.

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>È possibile eseguire ADC gen 1 e Azure competenza in parallelo?

Sì. Entrambi sono servizi indipendenti.

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>Ricerca per categorie eseguire la migrazione degli asset di dati ADC gen 1 esistenti ad Azure.

Usa le API di Azure per le competenze per estrarre da ADC gen 1 e inserirle in Azure. Per il glossario sono supportati gli strumenti in blocco basati su CSV.

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Ricerca per categorie crittografare i dati sensibili per le tabelle SQL con Azure competenza?

La crittografia dei dati viene eseguita a livello di origine dati. Azure ambito archivia solo i metadati. Non Visualizza in anteprima i dati.

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>Tutte le funzionalità di ADC gen 2 sono disponibili in ambito Azure?

Sì.

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>Qual è la differenza tra un glossario e una classificazione?

Un glossario utilizza una convenzione di denominazione seguita da utenti non tecnici/aziendali dei dati, noti anche come consumer di dati. Questi tipi di persone sono analisti aziendali o data scientist che usano la competenza di Azure per la ricerca di determinati tipi di dati, in base all'utilizzo aziendale. Ad esempio, è possibile che gli analisti della supply chain debbano cercare i termini *tipi di SKU* e *Dettagli della spedizione*. Questi termini vengono cercati nel glossario per trovare i dati rilevanti.
Classificazione è un tag applicato a un asset di dati a livello di tabella, di colonna o di file, che identifica i dati esistenti nell'asset. La classificazione può essere applicata automaticamente o manualmente, in base al tipo di dati trovati. In genere, si usano i tag di classificazione per identificare se un asset contiene dati sensibili e il tipo di dati sensibili che potrebbero essere.

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Azure competenza esegue l'analisi e la classificazione di messaggi di posta elettronica, PDF e così via in SharePoint e OneDrive?

L'analisi per siti e librerie di SharePoint locali viene fornita tramite lo scanner Azure Information Protection. Lo scanner è disponibile per l'uso tramite la sottoscrizione Microsoft 365 di un cliente con gli SKU seguenti: AIP P1, EMS E3 e M365 E3. Se si dispone di uno di questi SKU, è necessario disporre dei diritti appropriati per iniziare a usare lo scanner Azure Information Protection.

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>Qual è il calcolo usato per l'analisi?
È disponibile un'infrastruttura di analisi gestita da Microsoft. Per la maggior parte delle risorse di Azure/AWS supportate, non è necessario distribuire un'infrastruttura di analisi.

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>Esiste un modo per eseguire il provisioning di Azure con Azure Resource Manager modello (ARM)/interfaccia della riga di comando/PowerShell?

Sì, il modello ARM è disponibile

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>Si sta già usando Atlas, è possibile passare facilmente ad Azure competenza?

Azure competenza è compatibile con l'API Atlas. Se si esegue la migrazione da Atlas, è consigliabile analizzare prima le origini dati usando Azure. Una volta che gli asset sono disponibili nell'account, è possibile usare API Atlas simili per integrarle, ad esempio l'aggiornamento degli asset o l'aggiunta di una derivazione personalizzata. Azure competenza modifica l'API di ricerca per usare ricerca di Azure, in modo da poter usare la ricerca avanzata.

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>È possibile creare più cataloghi nel tenant?

Sì, è possibile creare più account Azure per ogni sottoscrizione e tenant. È possibile esaminare la pagina relativa ai limiti [per gestire e aumentare le quote per le risorse con Azure competenza](how-to-manage-quotas.md).

Suggerimenti aggiuntivi su quando è necessario o non devono avere più account sono documentati nelle [procedure consigliate](deployment-best-practices.md)per la distribuzione di Azure.

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>È possibile registrare più tenant in un singolo account Azure.

No, attualmente, per analizzare l'origine dati di un altro tenant, è necessario creare un account di Azure per la competenza in tale tenant.

### <a name="does-azure-purview-support-column-level-lineage"></a>Azure competenze supporta la derivazione a livello di colonna?

Sì, Azure competenza supporta la derivazione a livello di colonna.