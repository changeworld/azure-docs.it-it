---
title: Informazioni sulle chiavi - Azure Key Vault
description: Panoramica dei dettagli di sviluppo e dell'interfaccia REST di Azure Key Vault per le chiavi.
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: 3c4bb61217c7b972220a55a4837c2b3db980f2ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095987"
---
# <a name="about-keys"></a>Informazioni sulle chiavi

Azure Key Vault fornisce due tipi di risorse per archiviare e gestire le chiavi crittografiche. Gli insiemi di credenziali supportano chiavi protette da software e con modulo di protezione hardware. I HSM gestiti supportano solo chiavi protette da HSM. 

|Tipo di risorsa|Metodi di protezione della chiave|URL di base dell'endpoint del piano dati|
|--|--|--|
| **Insiemi di credenziali** | Protetta da software<br/><br/>e<br/><br/>Con protezione HSM (con SKU Premium)</li></ul> | https://{nome-insiemecredenziali}.vault.azure.net |
| * * HSM gestito * * | Con protezione HSM | https://{nome-hsm}.managedhsm.azure.net |
||||

- **Insiemi di credenziali**: gli insiemi di credenziali offrono una soluzione di gestione delle chiavi a basso costo, facile da distribuire, multi-tenant, con resilienza della zona (dove disponibile) e a disponibilità elevata adatta per gli scenari di applicazioni cloud più comuni.
- **Moduli di protezione hardware gestiti**: i moduli di protezione hardware gestiti sono moduli a singolo tenant, con resilienza della zona (se disponibile) e a disponibilità elevata per l'archiviazione e la gestione delle chiavi crittografiche. Sono particolarmente adatti per le applicazioni e gli scenari di utilizzo che gestiscono chiavi con valore elevato. Soddisfano inoltre i requisiti di sicurezza, conformità e normativi più rigorosi. 

> [!NOTE]
> Gli insiemi di credenziali consentono anche di archiviare e gestire diversi tipi di oggetti, come segreti, certificati e chiavi dell'account di archiviazione, oltre alle chiavi crittografiche.

Le chiavi crittografiche in Key Vault sono rappresentate come oggetti JSON Web Key [JWK]. Le specifiche per JSON (JavaScript Object Notation) e JOSE (JavaScript Object Signing and Encryption) sono:

-   [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Le specifiche JWK/JWA di base vengono inoltre estese per abilitare tipi di chiave univoci per le implementazioni di Azure Key Vault e dei moduli di protezione hardware gestiti. 

Le chiavi con protezione HSM vengono elaborate in un modulo di protezione hardware e rimangono sempre entro i limiti di questa protezione. 

- Gli insiemi di credenziali usano moduli di protezione hardware convalidati in base agli standard **FIPS 140-2 livello 2** per proteggere le chiavi con protezione HSM nell'infrastruttura back-end HSM. 
- Il modulo HSM gestito USA moduli HSM convalidati **FIPS 140-2 di livello 3** per proteggere le chiavi. Ogni pool di moduli di protezione hardware è un'istanza a tenant singolo isolata con il proprio [dominio di sicurezza](../managed-hsm/security-domain.md) che fornisce un isolamento crittografico completo da tutte le altre HSM che condividono la stessa infrastruttura hardware.

Queste chiavi sono protette nei pool di moduli di protezione hardware a tenant singolo. È possibile importare una chiave RSA, EC o simmetrica protetta tramite software o mediante l'esportazione da un modulo di protezione hardware supportato. È possibile anche generare chiavi nei pool di moduli di protezione hardware. Quando si importano chiavi con protezione HSM usando il metodo descritto nella [specifica BYOK (Bring Your Own Key)](../keys/byok-specification.md), viene abilitato il materiale della chiave di trasporto sicuro nei pool di moduli di protezione hardware gestiti. 

Per ulteriori informazioni sui limiti geografici, vedere [Centro di protezione Microsoft Azure](https://azure.microsoft.com/support/trust-center/privacy/)

## <a name="key-types-and-protection-methods"></a>Tipi di chiave e metodi di protezione

Key Vault supporta chiavi RSA e EC. Il modulo di protezione hardware gestito supporta RSA, EC e chiavi simmetriche. 

### <a name="hsm-protected-keys"></a>Chiavi protette dal modulo di protezione hardware

|Tipo di chiave|Insiemi di credenziali (solo SKU Premium)|HSM gestito|
|--|--|--|
|**EC-HSM**: chiave a curva ellittica | Supportato | Supportato|
|**RSA-HSM**: chiave RSA|Supportato|Supportato|
|**Oct-HSM**: chiave simmetrica|Non supportato|Supportato|
|||

### <a name="software-protected-keys"></a>Chiavi protette tramite software

|Tipo di chiave|Insiemi di credenziali|HSM gestito|
|--|--|--|
**RSA**: chiave RSA protetta tramite software|Supportato|Non supportato
**EC**: chiave a curva ellittica protetta tramite software|Supportato|Non supportato
|||

### <a name="compliance"></a>Conformità

|Tipo di chiave e destinazione|Conformità|
|---|---|
|Chiavi protette da software negli insiemi di credenziali (SKU Premium & standard) | FIPS 140-2 livello 1|
|Chiavi protette da HSM negli insiemi di credenziali (SKU Premium)| FIPS 140-2 livello 2|
|Chiavi protette da HSM in HSM gestito|FIPS 140-2 livello 3|
|||



Per informazioni dettagliate su ogni tipo di chiave, algoritmi, operazioni, attributi e tag, vedere [tipi di chiave, algoritmi e operazioni](about-keys-details.md) .

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su Key Vault](../general/overview.md)
- [Informazioni sul modulo di protezione hardware gestito](../managed-hsm/overview.md)
- [Informazioni sui segreti](../secrets/about-secrets.md)
- [Informazioni sui certificati](../certificates/about-certificates.md)
- [Panoramica dell'API REST di Key Vault](../general/about-keys-secrets-certificates.md)
- [Autenticazione, richieste e risposte](../general/authentication-requests-and-responses.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)
