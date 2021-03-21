---
title: Entità denominate generali
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2021
ms.author: aahi
ms.openlocfilehash: c1ff099dd6dffe06e9707ff23fffd57ae753ab64
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99500159"
---
La funzionalità NER per Analisi del testo restituisce le categorie di entità generale (non di identificazione) seguenti. ad esempio quando si inviano richieste all' `/entities/recognition/general` endpoint.


| Category | Descrizione                          |
|------------|-------------|--------------------------------------|-------------------------------------------------------------|--------------------------------------|
| [Person](#category-person)     | Nomi delle persone.  |
| [PersonType](#category-persontype) | Tipi di processo o ruoli contenuti da una persona. |
| [Posizione](#category-location)    | Punti di interesse naturali e umani, strutture, funzionalità geografiche e entità geopolitiche |
| [Organizzazione](#category-organization)  | Società, gruppi politici, bande musicali, sport clubs, enti governativi e organizzazioni pubbliche.  |
| [Event](#category-event)  | Eventi cronologici, di social networking e naturalmente in corso. |
| [Prodotto](#category-product) | Oggetti fisici di diverse categorie. |
| [Competenza](#category-skill) | Funzionalità, competenze o esperienza.  |
| [Indirizzo](#category-address) | Indirizzi di posta elettronica completi.  |
| [Numero di telefono](#category-phonenumber) | Numeri di telefono. |
| [Posta elettronica](#category-email) | Indirizzi di posta elettronica. |
| [URL](#category-url) | URL per siti Web. |
| [IP](#category-ip) | Indirizzi IP di rete. |
| [DateTime](#category-datetime) | Date e ore del giorno. |
| [Quantità](#category-quantity) | Unità e misurazioni numeriche. |


### <a name="category-person"></a>Categoria: persona

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        Persona

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Nomi delle persone.
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, <br> `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt`-`pt`, `ru`, `es`, `sv`, `tr`   
      
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

        Tipi di processo o ruoli contenuti da una persona
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::

### <a name="category-location"></a>Categoria: località

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        Location

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Punti di interesse naturali e umani, strutture, funzionalità geografiche e entità geopolitiche.
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sottocategorie

L'entità in questa categoria può includere le sottocategorie seguenti.

:::row:::
    :::column span="":::
        **Sottocategoria entità**

        Entità geopolitica (GPE)

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Città, Paesi o aree geografiche.
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Structural

    :::column-end:::
    :::column span="2":::

        Strutture artificiali. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Geografica

    :::column-end:::
    :::column span="2":::

        Funzionalità geografiche e naturali, ad esempio fiumi, oceani e deserti.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
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
    :::column span="2":::
      **Lingue del documento supportate**

      `ar`, `cs`, `da`, `nl`, `en`, `fi`, `fr`, `de`, `he`, `hu`, `it`, `ja`, `ko`, `no`, `pl`, `pt-br`, `pt-pt`, `ru`, `es`, `sv`, `tr`   
      
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
    :::column span="2":::
      **Lingue del documento supportate**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Scambio scorte

    :::column-end:::
    :::column span="2":::

        Gruppi di scambio azionario. 
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Organizzazioni correlate allo sport.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-event"></a>Categoria: evento

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        Evento

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Eventi cronologici, di social networking e naturalmente in corso.
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es` , `fr` , `de` , `it` , `zh-hans` , `ja` , `ko` `pt-pt` e `pt-br`  
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sottocategorie

L'entità in questa categoria può includere le sottocategorie seguenti.

:::row:::
    :::column span="":::
        **Sottocategoria entità**

        Culturale

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Eventi culturali e festivi.
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Natural

    :::column-end:::
    :::column span="2":::

        Eventi che si verificano naturalmente.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Sport

    :::column-end:::
    :::column span="2":::

        Eventi sportivi.
      
    :::column-end:::
    :::column span="2":::

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-product"></a>Categoria: prodotto

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        Prodotto

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Oggetti fisici di diverse categorie.
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::


#### <a name="subcategories"></a>Sottocategorie

L'entità in questa categoria può includere le sottocategorie seguenti.

:::row:::
    :::column span="":::
        **Sottocategoria entità**

        Elaborazione dei prodotti
    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Elaborazione di prodotti.
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`   
      
   :::column-end:::
:::row-end:::

### <a name="category-skill"></a>Categoria: skill

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        Competenza

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Funzionalità, competenze o esperienza.
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`  
      
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
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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

        Numeri di telefono (solo numeri di telefono US e UE).
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt` `pt-br`
      
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
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
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
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

Le entità di questa categoria possono includere le sottocategorie seguenti

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
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Tempo

    :::column-end:::
    :::column span="2":::

        Ora del giorno.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        DateRange

    :::column-end:::
    :::column span="2":::

        Intervalli di date.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Intervallo di tempo

    :::column-end:::
    :::column span="2":::

        Intervalli di tempo.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Duration

    :::column-end:::
    :::column span="2":::

        Durate
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Set

    :::column-end:::
    :::column span="2":::

        Set, ripetuto volte.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`  
      
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
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `ja`, `ko`, `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="subcategories"></a>Sottocategorie

L'entità in questa categoria può includere le sottocategorie seguenti.

:::row:::
    :::column span="":::
        **Sottocategoria entità**

        Number

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Numeri.
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Percentuale

    :::column-end:::
    :::column span="2":::

        Percentuali
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Numeri ordinali

    :::column-end:::
    :::column span="2":::

        Numeri ordinali.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Età

    :::column-end:::
    :::column span="2":::

        Età.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Valuta

    :::column-end:::
    :::column span="2":::

        Valute
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Dimensioni

    :::column-end:::
    :::column span="2":::

        Dimensioni e misurazioni.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
        Temperatura

    :::column-end:::
    :::column span="2":::

        Temperature.
      
    :::column-end:::
    :::column span="2":::

      `en`, `es`, `fr`, `de`, `it`, `zh-hans`, `pt-pt`, `pt-br`   
      
   :::column-end:::
:::row-end:::
