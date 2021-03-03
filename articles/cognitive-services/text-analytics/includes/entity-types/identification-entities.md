---
title: Entità di identificazione
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/17/2021
ms.author: aahi
ms.openlocfilehash: a376b050d79709885e3542d330bb6b1eea48d046
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750777"
---
### <a name="financial-account-identification"></a>Identificazione account finanziario

Questa categoria di entità include informazioni finanziarie e forme ufficiali di identificazione.

#### <a name="category-aba-routing-number"></a>Categoria: numero di Routing ABA

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        Numero di Routing ABA

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Numeri di routing di transito American Banker Association (ABA).
      
    :::column-end:::
:::row-end:::

#### <a name="category-swift-code"></a>Categoria: codice SWIFT

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        Codice SWIFT

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Codici SWIFT per informazioni sulle istruzioni di pagamento.
      
    :::column-end:::
:::row-end:::

#### <a name="category-credit-card"></a>Categoria: carta di credito

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        Carta di credito

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Numeri di carta di credito. 
      
    :::column-end:::
:::row-end:::

#### <a name="category-international-banking-account-number-iban"></a>Categoria: numero di conto bancario internazionale (IBAN) 

Questa categoria contiene l'entità seguente:

:::row:::
    :::column span="":::
        **Entità**

        Carta di credito

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Codici IBAN per informazioni sulle istruzioni di pagamento.
      
    :::column-end:::
:::row-end:::

### <a name="government-and-countryregion-specific-identification"></a>Identificazione per enti pubblici e paesi/aree geografiche

> [!NOTE]
> Le entità finanziarie e specifiche del paese seguenti non vengono restituite con il `domain=phi` parametro:
> * Numeri di passaporto
> * ID imposta

Le entità seguenti sono raggruppate ed elencate in base al paese:

#### <a name="argentina"></a>Argentina

:::row:::
    :::column span="":::
        **Entità**

        Numero di identità nazionale Argentina (DNI)

    :::column-end:::
:::row-end:::


#### <a name="austria"></a>Austria

:::row:::
    :::column span="":::
        **Entità**

        Scheda identità austriaca

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione fiscale austriaco

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numero di imposte IVA aggiunto al valore austriaco

    :::column-end:::
:::row-end:::



#### <a name="australia"></a>Australia

:::row:::
    :::column span="":::
        **Entità**

        Numero di conto bancario in Australia

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numero aziendale australiano

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numero aziendale Australia

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Licenza del driver Australia  

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di account medico dell'Australia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero Passport Australia

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numero Passport Australia

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numero di file fiscali Australia

    :::column-end:::

:::row-end:::


#### <a name="belgium"></a>Belgio

:::row:::
    :::column span="":::
        **Entità**

        Numero nazionale Belgio

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di imposte IVA aggiunto al valore Belgium

    :::column-end:::

:::row-end:::


#### <a name="brazil"></a>Brasile 

:::row:::
    :::column span="":::
        **Entità**

        Numero di entità legali in Brasile (CNPJ)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero CPF Brasile

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Scheda ID nazionale (RG) del Brasile

    :::column-end:::
:::row-end:::

#### <a name="canada"></a>Canada

:::row:::
    :::column span="":::
        **Entità**

        Numero conto bancario del Canada

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numero di licenza del driver Canada

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero del servizio integrità Canada

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero passaporto Canada

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione dell'integrità personale del Canada (PHIN)

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numero di assicurazioni sociali Canada

    :::column-end:::
:::row-end:::

#### <a name="chile"></a>Cile 

:::row:::
    :::column span="":::
        **Entità**

        Numero di carta identità Chile

    :::column-end:::
:::row-end:::

#### <a name="china"></a>Cina

:::row:::
    :::column span="":::
        **Entità**

        Numero di carta di identità residente Cina (PRC)

    :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Unione europea (UE)

:::row:::
    :::column span="":::
        **Entità**

        Numero di carta di debito UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di licenza del driver UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione nazionale UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero passaporto UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di previdenza sociale (SSN) o ID equivalente UE

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero identificativo contribuente EU

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Coordinate GPS UE

    :::column-end:::
:::row-end:::

#### <a name="france"></a>Francia

:::row:::
    :::column span="":::
        **Entità**

        Numero di licenza del driver Francia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di assicurazioni di integrità Francia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Scheda ID nazionale Francia (CNI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di passaporto Francia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di previdenza sociale in Francia (INSEE)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione fiscale Francia (numéro SPI)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero IVA aggiunto al valore Francia

    :::column-end:::
:::row-end:::

#### <a name="germany"></a>Germania

:::row:::
    :::column span="":::
        **Entità**

        Numero di licenza del driver tedesco

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di carta di identità Germania

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di passaporto Germania

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione fiscale Germania

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di imposta in Germania aggiunto al valore

    :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>RAS di Hong Kong

:::row:::
    :::column span="":::
        **Entità**

        Numero della carta di identità di Hong Kong (HKID)

    :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Ungheria

:::row:::
    :::column span="":::
        **Entità**

        Numero di identificazione personale in Ungheria

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione fiscale Ungheria

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di imposta aggiunto al valore Hungary

    :::column-end:::
:::row-end:::

#### <a name="india"></a>India

:::row:::
    :::column span="":::
        **Entità**

        Numero di account permanente India (PAN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione univoca India (Aadhaar)

    :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonesia

:::row:::
    :::column span="":::
        **Entità**

        Numero KTP (Indonesia Identity Card)

    :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irlanda

:::row:::
    :::column span="":::
        **Entità**

        Numero di servizio pubblico personale (PPS) dell'Irlanda

    :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israele

:::row:::
    :::column span="":::
        **Entità**

        ID nazionale Israel

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero conto di Israele Bank

    :::column-end:::
:::row-end:::

#### <a name="italy"></a>Italia

:::row:::
    :::column span="":::
        **Entità**

        ID licenza del driver Italia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Codice fiscale Italia

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di imposta aggiunto al valore Italia

    :::column-end:::
:::row-end:::


#### <a name="japan"></a>Giappone

:::row:::
    :::column span="":::
        **Entità**

        Numero del conto bancario in Giappone

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

        Numero di licenza del driver giapponese

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Giappone "numero mio" (personale)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Giappone "numero mio" (aziendale)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di registrazione residente in Giappone

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di carta di residenza del Giappone

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di assicurazioni sociali Giappone (SIN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero passaporto Giappone

    :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Lussemburgo

:::row:::
    :::column span="":::
        **Entità**

        Numero di identificazione nazionale lussemburghese (persone naturali)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione nazionale lussemburghese (persone non naturali)

    :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entità**

        Numero di carta di identità Malta

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione fiscale Malta

    :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nuova Zelanda

:::row:::
    :::column span="":::
        **Entità**

        Numero di conto bancario Nuova Zelanda

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di patente del driver neozelandese

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di ricavi Inland Nuova Zelanda

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ministero della Nuova Zelanda per il numero di integrità

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numero di previdenza sociale in Nuova Zelanda

    :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filippine

:::row:::
    :::column span="":::
        **Entità**

        Numero di ID multifunzione unificato per le Filippine

    :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portogallo 

:::row:::
    :::column span="":::
        **Entità**

        Numero di carta Citizen del Portogallo

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numero di identificazione fiscale Portugal

    :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapore

:::row:::
    :::column span="":::
        **Entità**

        Numero NRIC (National Registration ID card) di Singapore

    :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Sudafrica

:::row:::
    :::column span="":::
        **Entità**

        Numero di identificazione sudafricano

    :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Corea del Sud

:::row:::
    :::column span="":::
        **Entità**

        Numero di registrazione residente Corea del sud

    :::column-end:::
:::row-end:::

#### <a name="spain"></a>Spagna

:::row:::
    :::column span="":::
        **Entità**

        DNI Spagna

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Codice fiscale Spagna (SSN)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione fiscale Spagna

    :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Svizzera

:::row:::
    :::column span="":::
        **Entità**

        Numero di previdenza sociale Svizzera AVS

    :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taiwan 

:::row:::
    :::column span="":::
        **Entità**

        ID nazionale taiwanese

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certificato residente Taiwan (ARC/TARC Tax)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero passaporto Taiwan

    :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Regno Unito

:::row:::
    :::column span="":::
        **Entità**

        Regno Unito. Numero di patente del driver

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Regno Unito. Numero di rollup elettorale

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Regno Unito. Numero di Servizio integrità nazionale (NHS)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Regno Unito. Numero assicurativo nazionale (NINO)

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Regno Unito. o numero di passaporto statunitense

    :::column-end:::

:::row-end:::
:::row:::
    :::column span="":::

       Regno Unito. Numero di riferimento del contribuente univoco

    :::column-end:::

:::row-end:::


#### <a name="united-states"></a>Stati Uniti

:::row:::
    :::column span="":::
        **Entità**

        Numero di previdenza sociale (SSN) degli Stati Uniti

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numero di licenza del driver U.S.

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Stati Uniti o Regno Unito Numero passaporto

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numero di identificazione dei singoli contribuenti statunitensi

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numero DEA (Drug Enforcement Agency) degli Stati Uniti

    :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numero di conto bancario statunitense

    :::column-end:::
:::row-end:::
