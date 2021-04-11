---
title: Entità di identificazione
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/11/2021
ms.author: aahi
ms.openlocfilehash: a74c0cad971389168d643c9504f5bb809438a1ea
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106097241"
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

        Numeri di routing di transito American Banker Association (ABA). Restituito anche con `domain=phi` .

        Per ottenere questa categoria di entità, aggiungere `ABARoutingNumber` al `pii-categories` parametro. `ABARoutingNumber` verrà restituito anche nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`
      
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

        Codici SWIFT per informazioni sulle istruzioni di pagamento. Restituito anche con `domain=phi` .

        Per ottenere questa categoria di entità, aggiungere `SWIFTCode` al `pii-categories` parametro. `SWIFTCode` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`
      
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

        Numeri di carta di credito. Restituito anche con `domain=phi` .

        Per ottenere questa categoria di entità, aggiungere `CreditCardNumber` al `pii-categories` parametro. `CreditCardNumber` verrà restituito nella risposta dell'API se rilevato.

    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`, `ja`, `zh-hans`, `ja`, `ko`
      
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

        Codici IBAN per informazioni sulle istruzioni di pagamento. Restituito anche con `domain=phi` .

        Per ottenere questa categoria di entità, aggiungere `InternationlBankingAccountNumber` al `pii-categories` parametro. `InternationlBankingAccountNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="2":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt`, `pt-br`
      
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
    :::column span="2":::
        **Dettagli** Restituito anche con `domain=phi` .
        
        Per ottenere questa categoria di entità, aggiungere `ARNationalIdentityNumber` al `pii-categories` parametro. `ARNationalIdentityNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`, `es`
      
   :::column-end:::
:::row-end:::


#### <a name="austria"></a>Austria

:::row:::
    :::column span="":::
        **Entità**

        Scheda identità austriaca

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `ATIdentityCard` al `pii-categories` parametro. `ATIdentityCard` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione fiscale austriaco

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `ATTaxIdentificationNumber` al `pii-categories` parametro. `ATTaxIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di imposte IVA aggiunto al valore austriaco

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `ATValueAddedTaxNumber` al `pii-categories` parametro. `ATValueAddedTaxNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::



#### <a name="australia"></a>Australia

:::row:::
    :::column span="":::
        **Entità**

        Numero di conto bancario in Australia

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `AUDriversLicenseNumber` al `pii-categories` parametro. `AUDriversLicenseNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero aziendale australiano

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `AUBusinessNumber` al `pii-categories` parametro. `AUBusinessNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero aziendale Australia

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `AUCompanyNumber` al `pii-categories` parametro. `AUCompanyNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Licenza del driver Australia  

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `AUDriversLicense` al `pii-categories` parametro. `AUDriversLicense` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di account medico dell'Australia

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `AUMedicalAccountNumber` al `pii-categories` parametro. `AUMedicalAccountNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero Passport Australia

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `ATPassportNumber` al `pii-categories` parametro. `ATPassportNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di file fiscali Australia

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `ATTaxIdentificationNumber` al `pii-categories` parametro. `ATTaxIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="belgium"></a>Belgio

:::row:::
    :::column span="":::
        **Entità**

        Numero nazionale Belgio

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `BENationalNumber` al `pii-categories` parametro. `BENationalNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di imposte IVA aggiunto al valore Belgium

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `BEValueAddedTaxNumber` al `pii-categories` parametro. `BEValueAddedTaxNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::


#### <a name="brazil"></a>Brasile 

:::row:::
    :::column span="":::
        **Entità**

        Numero di entità legali in Brasile (CNPJ)

        

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `BRLegalEntityNumber` al `pii-categories` parametro. `BRLegalEntityNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero CPF Brasile

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `BRCPFNumber` al `pii-categories` parametro. `BRCPFNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Scheda ID nazionale (RG) del Brasile

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `BRNationalIDRG` al `pii-categories` parametro. `BRNationalIDRG` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`, `pt-br`
      
   :::column-end:::
:::row-end:::

#### <a name="canada"></a>Canada

:::row:::
    :::column span="":::
        **Entità**

        Numero conto bancario del Canada

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `CABankAccountNumber` al `pii-categories` parametro. `CABankAccountNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di licenza del driver Canada

    :::column-end:::

    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `CADriversLicenseNumber` al `pii-categories` parametro. `CADriversLicenseNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero del servizio integrità Canada

        
    :::column-end:::

    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `CAHealthServiceNumber` al `pii-categories` parametro. `CAHealthServiceNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::

    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero passaporto Canada

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `CAPassportNumber` al `pii-categories` parametro. `CAPassportNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione dell'integrità personale del Canada (PHIN)

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `CAPersonalHealthIdentification` al `pii-categories` parametro. `CAPersonalHealthIdentification` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di assicurazioni sociali Canada

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `CASocialInsuranceNumber` al `pii-categories` parametro. `CASocialInsuranceNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`, `fr`
      
   :::column-end:::
:::row-end:::

#### <a name="chile"></a>Cile 

:::row:::
    :::column span="":::
        **Entità**

        Numero di carta identità Chile

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `CLIdentityCardNumber` al `pii-categories` parametro. `CLIdentityCardNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `es`
      
   :::column-end:::
:::row-end:::

#### <a name="china"></a>Cina

:::row:::
    :::column span="":::
        **Entità**

        Numero di carta di identità residente Cina (PRC)

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `CNResidentIdentityCardNumber` al `pii-categories` parametro. `CNResidentIdentityCardNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="european-union-eu"></a>Unione europea (UE)

:::row:::
    :::column span="":::
        **Entità**

        Numero di carta di debito UE

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `EUDebitCardNumber` al `pii-categories` parametro. `EUDebitCardNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di licenza del driver UE

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `EUDriversLicenseNumber` al `pii-categories` parametro. `EUDriversLicenseNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Coordinate GPU UE

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `EUGPSCoordinates` al `pii-categories` parametro. `EUGPSCoordinates` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione nazionale UE

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `EUNationalIdentificationNumber` al `pii-categories` parametro. `EUNationalIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero passaporto UE

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `EUPassportNumber` al `pii-categories` parametro. `EUPassportNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di previdenza sociale (SSN) o ID equivalente UE

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `EUSocialSecurityNumber` al `pii-categories` parametro. `EUSocialSecurityNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero identificativo contribuente EU

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `EUTaxIdentificationNumber` al `pii-categories` parametro. `EUTaxIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`, `es`, `fr`, `de`, `it`, `pt-pt` 
      
   :::column-end:::
:::row-end:::

#### <a name="france"></a>Francia

:::row:::
    :::column span="":::
        **Entità**

        Numero di licenza del driver Francia

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `FRDriversLicenseNumber` al `pii-categories` parametro. `FRDriversLicenseNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di assicurazioni di integrità Francia

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `FRHealthInsuranceNumber` al `pii-categories` parametro. `FRHealthInsuranceNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Scheda ID nazionale Francia (CNI)

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `FRNationalID` al `pii-categories` parametro. `FRNationalID` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di passaporto Francia

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `FRPassportNumber` al `pii-categories` parametro. `FRPassportNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di previdenza sociale in Francia (INSEE)

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `FRSocialSecurityNumber` al `pii-categories` parametro. `FRSocialSecurityNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione fiscale Francia (numéro SPI)

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `FRTaxIdentificationNumber` al `pii-categories` parametro. `FRTaxIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero IVA aggiunto al valore Francia

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `FRValueAddedTaxNumber` al `pii-categories` parametro. `FRValueAddedTaxNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `fr` 
      
   :::column-end:::
:::row-end:::

#### <a name="germany"></a>Germania

:::row:::
    :::column span="":::
        **Entità**

        Numero di licenza del driver tedesco

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `DEDriversLicenseNumber` al `pii-categories` parametro. `DEDriversLicenseNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di carta di identità Germania

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `DEIdentityCardNumber` al `pii-categories` parametro. `DEIdentityCardNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di passaporto Germania

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `DEPassportNumber` al `pii-categories` parametro. `DEPassportNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `de` 
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione fiscale Germania

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `DETaxIdentificationNumber` al `pii-categories` parametro. `DETaxIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di imposta in Germania aggiunto al valore

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `DEValueAddedNumber` al `pii-categories` parametro. `DEValueAddedNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `de`
      
   :::column-end:::
:::row-end:::

#### <a name="hong-kong"></a>RAS di Hong Kong

:::row:::
    :::column span="":::
        **Entità**

        Numero della carta di identità di Hong Kong (HKID)

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `HKIdentityCardNumber` al `pii-categories` parametro. `HKIdentityCardNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="hungary"></a>Ungheria

:::row:::
    :::column span="":::
        **Entità**

        Numero di identificazione personale in Ungheria

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `HUPersonalIdentificationNumber` al `pii-categories` parametro. `HUPersonalIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione fiscale Ungheria

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `HUTaxIdentificationNumber` al `pii-categories` parametro. `HUTaxIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di imposta aggiunto al valore Hungary

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `HUValueAddedNumber` al `pii-categories` parametro. `HUValueAddedNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="india"></a>India

:::row:::
    :::column span="":::
        **Entità**

        Numero di account permanente India (PAN)

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `INPermanentAccount` al `pii-categories` parametro. `INPermanentAccount` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione univoca India (Aadhaar)

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `INUniqueIdentificationNumber` al `pii-categories` parametro. `INUniqueIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="indonesia"></a>Indonesia

:::row:::
    :::column span="":::
        **Entità**

        Numero KTP (Indonesia Identity Card)

    :::column-end:::
    :::column span="2":::

        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `IDIdentityCardNumber` al `pii-categories` parametro. `IDIdentityCardNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="ireland"></a>Irlanda

:::row:::
    :::column span="":::
        **Entità**

        Numero di servizio pubblico personale (PPS) dell'Irlanda

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `IEPersonalPublicServiceNumber` al `pii-categories` parametro. `IEPersonalPublicServiceNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::
 
        Numero di servizio pubblico personale (PPS) dell'Irlanda V2

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `IEPersonalPublicServiceNumberV2` al `pii-categories` parametro. `IEPersonalPublicServiceNumberV2` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="israel"></a>Israele

:::row:::
    :::column span="":::
        **Entità**

        ID nazionale Israel

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `ILNationalID` al `pii-categories` parametro. `ILNationalID` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero conto di Israele Bank

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `ILBankAccountNumber` al `pii-categories` parametro. `ILBankAccountNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="italy"></a>Italia

:::row:::
    :::column span="":::
        **Entità**

        ID licenza del driver Italia

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `ITDriversLicenseNumber` al `pii-categories` parametro. `ITDriversLicenseNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Codice fiscale Italia

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `ITFiscalCode` al `pii-categories` parametro. `ITFiscalCode` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di imposta aggiunto al valore Italia

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `ITValueAddedTaxNumber` al `pii-categories` parametro. `ITValueAddedTaxNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `it`
      
   :::column-end:::
:::row-end:::


#### <a name="japan"></a>Giappone

:::row:::
    :::column span="":::
        **Entità**

        Numero del conto bancario in Giappone

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `JPBankAccountNumber` al `pii-categories` parametro. `JPBankAccountNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di licenza del driver giapponese

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `JPDriversLicenseNumber` al `pii-categories` parametro. `JPDriversLicenseNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Giappone "numero mio" (personale)

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `JPMyNumberPersonal` al `pii-categories` parametro. `JPMyNumberPersonal` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Giappone "numero mio" (aziendale)

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `JPMyNumberCorporate` al `pii-categories` parametro. `JPMyNumberCorporate` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di registrazione residente in Giappone

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `ITValueAddedTaxNumber` al `pii-categories` parametro. `ITValueAddedTaxNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

     `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di carta di residenza del Giappone

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `JPResidenceCardNumber` al `pii-categories` parametro. `JPResidenceCardNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di assicurazioni sociali Giappone (SIN)

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `JPSocialInsuranceNumber` al `pii-categories` parametro. `JPSocialInsuranceNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero passaporto Giappone

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `JPPassportNumber` al `pii-categories` parametro. `JPPassportNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `ja`
      
   :::column-end:::
:::row-end:::

#### <a name="luxembourg"></a>Lussemburgo

:::row:::
    :::column span="":::
        **Entità**

        Numero di identificazione nazionale lussemburghese (persone naturali)

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `LUNationalIdentificationNumberNatural` al `pii-categories` parametro. `LUNationalIdentificationNumberNatural` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `fr`, `de`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione nazionale lussemburghese (persone non naturali)

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `LUNationalIdentificationNumberNonNatural` al `pii-categories` parametro. `LUNationalIdentificationNumberNonNatural` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `fr`, `de`
      
   :::column-end:::
:::row-end:::

#### <a name="malta"></a>Malta

:::row:::
    :::column span="":::
        **Entità**

        Numero di carta di identità Malta

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `MTIdentityCardNumber` al `pii-categories` parametro. `MTIdentityCardNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione fiscale Malta

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `MTTaxIDNumber` al `pii-categories` parametro. `MTTaxIDNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="new-zealand"></a>Nuova Zelanda

:::row:::
    :::column span="":::
        **Entità**

        Numero di conto bancario Nuova Zelanda

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `NZBankAccountNumber` al `pii-categories` parametro. `NZBankAccountNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di patente del driver neozelandese

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `NZDriversLicenseNumber` al `pii-categories` parametro. `NZDriversLicenseNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di ricavi Inland Nuova Zelanda

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `NZInlandRevenueNumber` al `pii-categories` parametro. `NZInlandRevenueNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Ministero della Nuova Zelanda per il numero di integrità

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `NZMinistryOfHealthNumber` al `pii-categories` parametro. `NZMinistryOfHealthNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numero di previdenza sociale in Nuova Zelanda

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `NZSocialWelfareNumber` al `pii-categories` parametro. `NZSocialWelfareNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="philippines"></a>Filippine

:::row:::
    :::column span="":::
        **Entità**

        Numero di ID multifunzione unificato per le Filippine

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `PHUnifiedMultiPurposeIDNumber` al `pii-categories` parametro. `PHUnifiedMultiPurposeIDNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="portugal"></a>Portogallo 

:::row:::
    :::column span="":::
        **Entità**

        Numero di carta Citizen del Portogallo

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `PTCitizenCardNumber` al `pii-categories` parametro. `PTCitizenCardNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `pt-pt`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numero di identificazione fiscale Portugal

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `PTTaxIdentificationNumber` al `pii-categories` parametro. `PTTaxIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `pt-pt`
      
   :::column-end:::
:::row-end:::

#### <a name="singapore"></a>Singapore

:::row:::
    :::column span="":::
        **Entità**

        Numero NRIC (National Registration ID card) di Singapore

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `PTTaxIdentificationNumber` al `pii-categories` parametro. `PTTaxIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`, `zh-hans`
      
   :::column-end:::
:::row-end:::


#### <a name="south-africa"></a>Sudafrica

:::row:::
    :::column span="":::
        **Entità**

        Numero di identificazione sudafricano

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `ZAIdentificationNumber` al `pii-categories` parametro. `ZAIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="south-korea"></a>Corea del Sud

:::row:::
    :::column span="":::
        **Entità**

        Numero di registrazione residente Corea del sud

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `KRResidentRegistrationNumber` al `pii-categories` parametro. `KRResidentRegistrationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `ko`
      
   :::column-end:::
:::row-end:::

#### <a name="spain"></a>Spagna

:::row:::
    :::column span="":::
        **Entità**

        DNI Spagna

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `ESDNI` al `pii-categories` parametro. `ESDNI` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Codice fiscale Spagna (SSN)

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `ESSocialSecurityNumber` al `pii-categories` parametro. `ESSocialSecurityNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero di identificazione fiscale Spagna

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `ESTaxIdentificationNumber` al `pii-categories` parametro. `ESTaxIdentificationNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `es`
      
   :::column-end:::
:::row-end:::
 
#### <a name="switzerland"></a>Svizzera

:::row:::
    :::column span="":::
        **Entità**

        Numero di previdenza sociale Svizzera AVS

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `CHSocialSecurityNumber` al `pii-categories` parametro. `CHSocialSecurityNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `fr`, `de`, `it`
      
   :::column-end:::
:::row-end:::


#### <a name="taiwan"></a>Taiwan 

:::row:::
    :::column span="":::
        **Entità**

        ID nazionale taiwanese

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `TWNationalID` al `pii-categories` parametro. `TWNationalID` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Certificato residente Taiwan (ARC/TARC Tax)

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `TWResidentCertificate` al `pii-categories` parametro. `TWResidentCertificate` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

        Numero passaporto Taiwan

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `TWPassportNumber` al `pii-categories` parametro. `TWPassportNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::

#### <a name="united-kingdom"></a>Regno Unito

:::row:::
    :::column span="":::
        **Entità**

        Regno Unito. Numero di patente del driver

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `UKDriversLicenseNumber` al `pii-categories` parametro. `UKDriversLicenseNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
    :::column-end:::
    
:::row-end:::
:::row:::
    :::column span="":::

       Regno Unito. Numero di rollup elettorale

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `UKNationalInsuranceNumber` al `pii-categories` parametro. `UKNationalInsuranceNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Regno Unito. Numero di Servizio integrità nazionale (NHS)

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `UKNationalHealthNumber` al `pii-categories` parametro. `UKNationalHealthNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Regno Unito. Numero assicurativo nazionale (NINO)

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `UKNationalInsuranceNumber` al `pii-categories` parametro. `UKNationalInsuranceNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Regno Unito. o numero di passaporto statunitense

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `USUKPassportNumber` al `pii-categories` parametro. `USUKPassportNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Regno Unito. Numero di riferimento del contribuente univoco

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `UKUniqueTaxpayerNumber` al `pii-categories` parametro. `UKUniqueTaxpayerNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::


#### <a name="united-states"></a>Stati Uniti

:::row:::
    :::column span="":::
        **Entità**

        Numero di previdenza sociale (SSN) degli Stati Uniti

    :::column-end:::
    :::column span="2":::
        **Dettagli**

        Per ottenere questa categoria di entità, aggiungere `USSocialSecurityNumber` al `pii-categories` parametro. `USSocialSecurityNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::
      **Lingue del documento supportate**

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numero di licenza del driver U.S.

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `USDriversLicenseNumber` al `pii-categories` parametro. `USDriversLicenseNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Stati Uniti o Regno Unito Numero passaporto

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `USUKPassportNumber` al `pii-categories` parametro. `USUKPassportNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numero di identificazione dei singoli contribuenti statunitensi

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `USIndividualTaxpayerIdentification` al `pii-categories` parametro. `USIndividualTaxpayerIdentification` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numero DEA (Drug Enforcement Agency) degli Stati Uniti

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `DrugEnforcementAgencyNumber` al `pii-categories` parametro. `DrugEnforcementAgencyNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
:::row:::
    :::column span="":::

       Numero di conto bancario statunitense

    :::column-end:::
    :::column span="2":::

        Per ottenere questa categoria di entità, aggiungere `USBankAccountNumber` al `pii-categories` parametro. `USBankAccountNumber` verrà restituito nella risposta dell'API se rilevato.
      
    :::column-end:::
    :::column span="":::

      `en`
      
   :::column-end:::
:::row-end:::
