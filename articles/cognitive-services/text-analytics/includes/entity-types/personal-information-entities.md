---
title: Entità denominate per informazioni personali
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: 97167485dae155670f0eb83fc3ef9cb658952251
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750092"
---
> [!NOTE]
> Per rilevare le informazioni di integrità protette (PHI), usare il `domain=phi` parametro e la versione del modello `2020-04-01` o versioni successive.
>
> ad esempio `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/recognition/pii?domain=phi&model-version=2021-01-15`
 
Quando si inviano richieste all'endpoint, vengono restituite le categorie di entità seguenti `/v3.1-preview.3/entities/recognition/pii` .


| Category   |  Descrizione                          |
|------------|-------------|
| [Person](#category-person)      |  Nomi delle persone.  |
| [PersonType](#category-persontype) | Tipi di processo o ruoli contenuti da una persona. |
| [Numero di telefono](#category-phonenumber) |Numeri di telefono (solo numeri di telefono US e UE). |
| [Organizzazione](#category-organization) |  Società, gruppi, enti governativi e altre organizzazioni.  |
| [Indirizzo](#category-address) | Indirizzi di posta elettronica completi.  |
| [Posta elettronica](#category-email) | Indirizzi di posta elettronica.   |
| [URL](#category-url) | URL per siti Web.  |
| [IP](#category-ip) | Indirizzi IP di rete.  |
| [DateTime](#category-datetime) | Date e ore del giorno. | 
| [Quantità](#category-quantity) | Numeri e quantità numeriche.  |
| [Informazioni su Azure](#azure-information) | Informazioni di Azure identificabili, ad esempio le informazioni di autenticazione.  |
| [Identificazione](#identification) | Identificazione finanziaria e specifica del paese.  |

### <a name="category-person"></a>Categoria: persona

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        Persona

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Nomi delle persone. Restituito anche con `domain=phi` .
      
    :::column-end:::
:::row-end:::

### <a name="category-persontype"></a>Categoria: PersonType

Questa categoria contiene l'entità seguente:


:::row:::
    :::column span="":::
        **Entità**

        PersonType

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Tipi di processo o ruoli contenuti da una persona.
      
    :::column-end:::
:::row-end:::

### <a name="category-phonenumber"></a>Categoria: PhoneNumber

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        PhoneNumber

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Numeri di telefono (solo numeri di telefono US e UE). Restituito anche con `domain=phi` .
      
    :::column-end:::
:::row-end:::


### <a name="category-organization"></a>Categoria: organizzazione

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        Organization

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Società, gruppi politici, bande musicali, sport clubs, enti governativi e organizzazioni pubbliche. Le nazionalità e le religioni non sono incluse in questo tipo di entità.
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sottocategorie

L'entità in questa categoria può includere le sottocategorie seguenti.

:::row:::
    :::column span="":::
        **Sottocategoria entità**

        Medicina

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Società e gruppi medicali.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Scambio scorte

    :::column-end:::
    :::column span="2":::

        Gruppi di scambio azionario. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Organizzazioni correlate allo sport.
      
    :::column-end:::

:::row-end:::


### <a name="category-address"></a>Categoria: Indirizzo

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        Indirizzo

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Indirizzo di posta elettronica completo.
      
    :::column-end:::
:::row-end:::

### <a name="category-email"></a>Categoria: posta elettronica

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        E-mail

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Indirizzi di posta elettronica.
      
    :::column-end:::
:::row-end:::

### <a name="category-url"></a>Categoria: URL

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        URL

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        URL per siti Web. 
      
    :::column-end:::
:::row-end:::

### <a name="category-ip"></a>Categoria: IP

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        IP

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        indirizzi IP di rete. 
      
    :::column-end:::
:::row-end:::

### <a name="category-datetime"></a>Categoria: DateTime

Questa categoria contiene le entità seguenti:

:::row:::
    :::column span="":::
        **Entità**

        Datetime

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Date e ore del giorno. 
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sottocategorie

L'entità in questa categoria può includere le sottocategorie seguenti.

:::row:::
    :::column span="":::
        **Sottocategoria entità**

        Data

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Date calendario.
      
    :::column-end:::
:::row-end:::

### <a name="category-quantity"></a>Categoria: Quantity

Questa categoria contiene le entità seguenti:

:::row:::
    :::column span="":::
        **Entità**

        Quantità

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Numeri e quantità numeriche.
      
    :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sottocategorie

L'entità in questa categoria può includere le sottocategorie seguenti.

:::row:::
    :::column span="":::
        **Sottocategoria entità**

        Età

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Età.
      
    :::column-end:::
:::row-end:::

### <a name="azure-information"></a>Informazioni su Azure

Queste categorie di entità includono informazioni di Azure identificabili, incluse le informazioni di autenticazione e le stringhe di connessione. Non restituito con il `domain=phi` parametro.

:::row:::
    :::column span="":::
        **Entità**

        Chiave di autenticazione di Azure DocumentDB 

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Chiave di autorizzazione per un server Azure Cosmos DB.   
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Stringa di connessione del database IaaS di Azure e stringa di connessione di SQL di Azure

    :::column-end:::
    :::column span="2":::

        Stringa di connessione per un database di infrastruttura distribuita come servizio (IaaS) di Azure e una stringa di connessione SQL.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Stringa di connessione SQL di Azure

    :::column-end:::
    :::column span="2":::

        Stringa di connessione per un database nel database SQL di Azure.
      
    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Stringa di connessione di Azure IoT  

    :::column-end:::
    :::column span="2":::

        Stringa di connessione per Azure. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Password delle impostazioni di pubblicazione di Azure  

    :::column-end:::
    :::column span="2":::

        Password per le impostazioni di pubblicazione di Azure.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Stringa di connessione di Cache Redis di Azure 

    :::column-end:::
    :::column span="2":::

        Stringa di connessione per una cache Redis.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Azure SAS 

    :::column-end:::
    :::column span="2":::

        Stringa di connessione per Azure Software as a Service (SaaS).
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Stringa di connessione del bus di servizio di Azure

    :::column-end:::
    :::column span="2":::

        Stringa di connessione per un bus di servizio di Azure.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chiave dell'account di archiviazione di Azure 

    :::column-end:::
    :::column span="2":::

       Chiave dell'account per un account di archiviazione di Azure. 
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Chiave dell'account di archiviazione di Azure (generico)

    :::column-end:::
    :::column span="2":::

       Chiave dell'account generico per un account di archiviazione di Azure.
      
    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Stringa di connessione di SQL Server 

    :::column-end:::
    :::column span="2":::

       Stringa di connessione per un computer che esegue SQL Server.
      
    :::column-end:::
:::row-end:::

### <a name="identification"></a>Identification

[!INCLUDE [supported identification entities](./identification-entities.md)]
