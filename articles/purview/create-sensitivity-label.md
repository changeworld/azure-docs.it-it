---
title: Applicare automaticamente le etichette di riservatezza ai dati
description: Informazioni su come creare etichette di riservatezza e applicarle automaticamente ai dati durante un'analisi.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/22/2020
ms.openlocfilehash: b286533da90565429f96c5701aaa72dab0fb21d0
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555197"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>Etichettare automaticamente i dati in ambito Azure

Questo articolo descrive come creare etichette di riservatezza di Microsoft Information Protection (MIP) e applicarle automaticamente alle risorse di Azure in ambito Azure.

## <a name="what-are-sensitivity-labels"></a>Che cosa sono le etichette di riservatezza? 

Per svolgere il lavoro, gli utenti dell'organizzazione collaborano con altri utenti all'interno e all'esterno dell'organizzazione. I dati non vengono sempre mantenuti nel cloud e spesso vengono spostati ovunque, tra dispositivi, app e servizi. 

Quando si esegue il roaming dei dati, si vuole farlo in modo sicuro e protetto che soddisfi i criteri aziendali e di conformità dell'organizzazione.

L'applicazione di etichette di riservatezza consente di indicare il grado di riservatezza di determinati dati nell'organizzazione. Ad esempio, un nome di progetto specifico può essere altamente riservato all'interno dell'organizzazione, mentre lo stesso termine non è riservato ad altre organizzazioni. 

### <a name="sensitivity-labels-in-azure-purview"></a>Etichette di riservatezza in Azure

In ambito, le classificazioni sono simili ai tag Subject e vengono usate per contrassegnare e identificare i dati di un tipo specifico che si trova all'interno dell'area dati durante l'analisi.

La competenza usa le stesse classificazioni, note anche come tipi di informazioni riservate, come Microsoft 365.  Le etichette di riservatezza MIP vengono create nel centro sicurezza e conformità di Microsoft 365 (SCC). In questo modo è possibile estendere le etichette di riservatezza esistenti tra le risorse di competenza di Azure.

> [!NOTE]
> Mentre le classificazioni vengono confrontate direttamente (un numero di previdenza sociale ha una classificazione di un **numero di previdenza sociale**), le etichette di riservatezza vengono applicate quando vengono rilevate una o più classificazioni e scenari. 
> 

Le etichette di riservatezza in Azure possono essere usate per:

- **Applicare automaticamente le etichette** ai file e alle colonne del database

- **Controllare** chi può e non può accedere ai dati

- **Applicare le impostazioni di protezione**, ad esempio la crittografia, sui dati con etichetta

Per altre informazioni, vedere:

- Informazioni [sulle etichette di riservatezza](/microsoft-365/compliance/sensitivity-labels) nella documentazione di Microsoft 365
- [Che cosa sono le regole di etichetta automatica?](#what-are-autolabeling-rules)
- [Tipi di dati supportati per le etichette di riservatezza in Azure](#supported-data-types-for-sensitivity-labels-in-azure-purview)

#### <a name="what-are-autolabeling-rules"></a>Che cosa sono le regole di etichetta automatica?

I dati sono in continua crescita e cambiano. Il rilevamento dei dati attualmente senza etichetta e l'esecuzione di un'azione per applicare manualmente le etichette non sono solo complessi, ma è anche un mal di lavoro superfluo. 

Le regole di etichetta automatica sono condizioni specificate, indicando quando applicare un'etichetta specifica. Quando vengono soddisfatte queste condizioni, l'etichetta viene assegnata automaticamente ai dati, mantenendo le etichette di riservatezza coerenti sui dati, su larga scala.

Quando si creano le etichette, assicurarsi di definire le regole di etichetta automatica per [i file](#define-autolabeling-rules-for-files) e le [colonne del database](#define-autolabeling-rules-for-database-columns) per applicare automaticamente le etichette a ogni analisi dei dati. 

Dopo aver analizzato i dati in ambito, è possibile visualizzare le etichette applicate automaticamente nei report catalogo e Insight di competenza.

#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Tipi di dati supportati per le etichette di riservatezza in Azure

Le etichette di riservatezza sono supportate in ambito Azure per i tipi di dati seguenti:

|Tipo di dati  |Origini  |
|---------|---------|
|Assegnazione automatica di etichette per i file     |      - Archiviazione BLOB di Azure  </br>-Azure Data Lake Storage generazione 1 e generazione 2  |
|Assegnazione automatica di etichette per le colonne del database     |  -SQL Server </br>-Database SQL di Azure </br>-Istanza gestita di database SQL di Azure   <br> -Sinapsi di Azure  <br> - Azure Cosmos DB   |
| | |

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>Come creare etichette di riservatezza in Microsoft 365

Se non si dispone già di etichette di riservatezza, è necessario crearle e renderle disponibili per Azure. È anche possibile modificare le etichette di riservatezza esistenti per renderle disponibili per Azure.

Per altre informazioni, vedere:

- [Requisiti di licenza](#licensing-requirements)
- [Estensione delle etichette di riservatezza ad Azure](#extending-sensitivity-labels-to-azure-purview)
- [Creazione di nuove etichette di riservatezza o modifica di etichette esistenti](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>Requisiti di licenza

Le etichette di riservatezza MIP vengono create e gestite nella Microsoft 365 Centro sicurezza e conformità. Per creare etichette di riservatezza da usare in Azure, è necessario disporre di una licenza attiva Microsoft 365 E5.

Se non si ha ancora la licenza richiesta, è possibile iscriversi per ottenere una versione di valutazione di [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion).

### <a name="extending-sensitivity-labels-to-azure-purview"></a>Estensione delle etichette di riservatezza ad Azure

Per impostazione predefinita, le etichette di riservatezza MIP sono disponibili solo per gli asset in Microsoft 365, in cui è possibile applicarli a file e messaggi di posta elettronica.

Per applicare le etichette di riservatezza MIP alle risorse di Azure in ambito Azure, è necessario fornire esplicitamente il consenso per estendere le etichette e selezionare le etichette specifiche che si desidera rendere disponibili in ambito.

Estendendo le etichette di riservatezza del MIP con ambito Azure, le organizzazioni possono ora individuare, classificare e ottenere informazioni approfondite su una vasta gamma di origini dati, riducendo al minimo i rischi di conformità.

> [!NOTE]
> Poiché Microsoft 365 e Azure sono servizi distinti, è possibile che vengano distribuiti in aree diverse. I nomi di etichetta e i nomi dei tipi di informazioni riservate personalizzate sono considerati dati del cliente e vengono conservati nella stessa posizione geografica per impostazione predefinita per proteggere la riservatezza dei dati e per evitare leggi GDPR.
>
> Per questo motivo, le etichette e i tipi di informazioni riservate personalizzate non vengono condivisi in Azure per impostazione predefinita e richiedono il consenso per usarli in Azure.

> [!IMPORTANT]
> Il consenso consente a Microsoft di condividere il nome dell'etichetta e il nome del tipo di informazioni riservate personalizzate sia in Azure *sia* in Azure Security Center (ASC). Microsoft usa le informazioni sulle etichette di Azure per arricchire le raccomandazioni e gli avvisi in ASC. 
>
> Il consenso in Microsoft 365 Compliance Center si applica alla condivisione di questi dati con entrambi i servizi. Attualmente non è possibile condividere le informazioni sull'etichetta solo con l'ambito di competenza di Azure.

**Per estendere le etichette di riservatezza alle competenze:**

In Microsoft 365 passare alla pagina **Information Protection** . Nella finestra di dialogo **Estendi etichetta per asset in Azure**, selezionare il pulsante **attiva** , quindi selezionare **Sì** nella finestra di conferma visualizzata.

Ad esempio:

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="Selezionare * * Attiva * * per estendere le etichette di riservatezza alla competenza" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
Quando si estende l'assegnazione di etichette alle risorse in Azure, è possibile selezionare le etichette che si vuole rendere disponibili in ambito. Per altre informazioni, vedere [Creating New Sensitivity labels o Modifying existing labels](#creating-new-sensitivity-labels-or-modifying-existing-labels).
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>Creazione di nuove etichette di riservatezza o modifica di etichette esistenti

1. Aprire il [Centro sicurezza e conformità Microsoft 365](https://protection.office.com/homepage). 

1. In **soluzioni** selezionare **Information Protection**, quindi selezionare **Crea un'etichetta**. 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="Creare etichette di riservatezza nel centro sicurezza e conformità di Microsoft 365" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. Assegnare un nome all'etichetta. Quindi, in **definire l'ambito per questa etichetta**, selezionare **file e messaggi di posta elettronica** e **risorse di Azure per competenza**.
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="Creare l'etichetta nel centro sicurezza e conformità di Microsoft 365" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. Seguire le altre richieste nella procedura guidata per le impostazioni dell'etichetta. 

    In particolare, definire le regole di etichetta automatica per i file e le colonne del database:

    - [Definire le regole di etichetta automatica per i file](#define-autolabeling-rules-for-files)
    - [Definire le regole di etichetta automatica per le colonne del database](#define-autolabeling-rules-for-database-columns)

    Per ulteriori informazioni sulle opzioni della procedura guidata, vedere [quali etichette di riservatezza possono](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) essere eseguite nella documentazione Microsoft 365.

1. Ripetere i passaggi elencati in precedenza per creare etichette aggiuntive. 

    Per creare un'etichetta secondaria, selezionare l'etichetta padre > **...**  >  **Altre azioni**  >  **Aggiungi etichetta secondaria**.

1. Per modificare le etichette esistenti, passare a **Information Protection**  >  **etichette** e selezionare l'etichetta. 

    Selezionare quindi **Modifica etichetta** per aprire di nuovo la procedura guidata **Modifica etichetta di riservatezza** , con tutte le impostazioni definite al momento della creazione dell'etichetta.

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="Modificare un'etichetta di riservatezza esistente" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. Al termine della creazione di tutte le etichette, assicurarsi di visualizzare l'ordine dell'etichetta e riordinarle in base alle esigenze. 

    Per modificare l'ordine di un'etichetta, selezionare **...** **> più azioni** si  >  **spostano** verso l'alto o lo **spostamento verso il basso.** 

    Per ulteriori informazioni, vedere la pagina relativa alla [priorità delle etichette (Order Matters)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) nella documentazione Microsoft 365.

> [!IMPORTANT]
> Non eliminare un'etichetta, a meno che non si sia a conoscenza dell'effetto degli utenti. 
>
> Per ulteriori informazioni, vedere [rimozione ed eliminazione di etichette](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels) nella documentazione di Microsoft 365.

Continuare analizzando [i dati per applicare automaticamente le etichette](#scan-your-data-to-apply-labels-automatically)e quindi:

- [Visualizzare le etichette negli asset](#view-labels-on-assets)
- [Visualizzare i report dettagliati per le classificazioni e le etichette di riservatezza](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-autolabeling-rules-for-files"></a>Definire le regole di etichetta automatica per i file

Definire le regole di etichettatura automatica per i file nella procedura guidata quando si crea o si modifica l'etichetta. 

Nella pagina **etichetta automatica per le app di Office** abilitare l' **etichettatura automatica per le app di Office** e quindi definire le condizioni in cui si vuole che l'etichetta venga applicata automaticamente ai dati.

Ad esempio:

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="Definire le regole di etichetta automatica per i file nel centro sicurezza e conformità di Microsoft 365" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
Per ulteriori informazioni, vedere [applicare automaticamente un'etichetta di riservatezza ai dati](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) nella documentazione Microsoft 365. 

#### <a name="define-autolabeling-rules-for-database-columns"></a>Definire le regole di etichetta automatica per le colonne del database

Definire le regole di etichetta automatica per le colonne del database nella procedura guidata quando si crea o si modifica l'etichetta. 

Nell'opzione **asset di Azure (anteprima)** :

1. Selezionare il dispositivo **di scorrimento etichetta automatica per colonne di database** .

1. Selezionare **Controlla tipi di informazioni riservate** per scegliere i tipi di informazioni riservate che si desidera applicare all'etichetta.

Ad esempio:
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="Definire le regole di etichetta automatica per le colonne SQL nel centro sicurezza e conformità di Microsoft 365" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>Analizza i dati per applicare le etichette automaticamente

Analizza i dati in Azure per applicare automaticamente le etichette create, in base alle regole di autoetichettatura definite. 

Per altre informazioni su come configurare le analisi in diversi asset in Azure, vedere:

|Source (Sorgente)  |Informazioni di riferimento  |
|---------|---------|
|**Archiviazione BLOB di Azure**     |[Registrare e analizzare l'archivio BLOB di Azure](register-scan-azure-blob-storage-source.md)         |
|**Archiviazione di Azure Data Lake**     |[Registra e analizza Azure Data Lake Storage Gen1](register-scan-adls-gen1.md) </br>[Registra e analizza Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)         |
|**Database SQL di Azure**|[Registrare e analizzare un database SQL di Azure](register-scan-azure-sql-database.md) </br>[Registrare e analizzare un Istanza gestita di database SQL di Azure](register-scan-azure-sql-database-managed-instance.md)|
|**Account di archiviazione protetti da firewall**     |[Analizza gli account di archiviazione protetti da un firewall in Azure](scan-sqlresource-firewall.md)         |
| | |

## <a name="view-labels-on-assets"></a>Visualizzare le etichette negli asset

Una volta definite le regole di etichetta automatica per le etichette in Microsoft 365 ed eseguito l'analisi dei dati in Azure, le etichette vengono applicate automaticamente agli asset. 

**Per visualizzare le etichette applicate agli asset nel catalogo di competenze di Azure:**

Nel catalogo di Azure per le competenze usare le opzioni di filtro **etichette** per visualizzare solo i file con etichette specifiche. Ad esempio: 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="Cerca asset per etichetta" lightbox="media/create-sensitivity-label/filter-search-results.png":::

Ad esempio:

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="Visualizzare un'etichetta di riservatezza in un file nell'archivio BLOB di Azure" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>Visualizzare i report dettagliati per le classificazioni e le etichette di riservatezza

Trova informazioni dettagliate sui dati classificati e con etichetta in Azure, usando i report di **classificazione** e **riservatezza delle etichette** .

> [!div class="nextstepaction"]
> [Informazioni dettagliate sulla classificazione](./classification-insights.md)

> [!div class="nextstepaction"]
> [Insights label Sensitivity](sensitivity-insights.md)


