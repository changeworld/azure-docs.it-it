---
title: Set di attestazioni di Attestazione di Azure
description: Il set di attestazioni di Attestazione di Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: eb08bb262806cb662822a75898196546a5c1058e
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762536"
---
# <a name="claim-sets"></a>Set di attestazioni

Le attestazioni generate nel processo di attestazione di enclavi usando Attestazione di Microsoft Azure possono essere divise nelle categorie seguenti:

- **Attestazioni in ingresso**: attestazioni generate dall'attestazione Microsoft Azure dopo l'analisi dell'evidenza di attestazione e possono essere usate dagli autori di criteri per definire le regole di autorizzazione in un criterio personalizzato

- **Attestazioni in uscita**: attestazioni generate dall'attestazione di Azure e contiene tutte le attestazioni che finiscono nel token di attestazione

- **Attestazioni di proprietà**: le attestazioni create come output da Attestazione di Azure. Sono incluse tutte le attestazioni che rappresentano proprietà del token di attestazione, ad esempio la codifica del report, la durata di validità del report e così via.

### <a name="common-incoming-claims-across-all-attestation-types"></a>Attestazioni in ingresso comuni per tutti i tipi di attestazione

Le attestazioni seguenti vengono generate dall'attestazione di Azure e possono essere usate per definire le regole di autorizzazione in un criterio personalizzato:
- **x-ms-ver**: versione dello schema JWT (prevista come "1,0")
- **x-ms-Attestation-Type**: valore stringa che rappresenta il tipo di attestazione 
- **x-ms-Policy-hash**: hash dei criteri di valutazione dell'attestazione di Azure calcolati come BASE64URL (SHA256 (UTF8 (BASE64URL (UTF8 (testo del criterio))))
- **x-ms-Policy-Signer**: oggetto JSON con un membro "JWK" che rappresenta la chiave utilizzata da un cliente per firmare i criteri, quando il cliente carica un criterio firmato

Le attestazioni seguenti sono considerate deprecate, ma sono completamente supportate. Si consiglia di usare i nomi delle attestazioni non deprecate.

Attestazione deprecata | Attestazione consigliata 
--- | --- 
ver | x-ms-ver
tee | x-ms-attestazione-tipo
MAA-policyHash | x-ms-Policy-hash
policy_hash | x-ms-Policy-hash
policy_signer | x-ms-Policy-firmatario

### <a name="common-outgoing-claims-across-all-attestation-types"></a>Attestazioni in uscita comuni in tutti i tipi di attestazione

Di seguito sono riportate le attestazioni definite dalla [JWT IETF](https://tools.ietf.org/html/rfc7519) e usate dall'attestazione di Azure nell'oggetto Response:

- **Attestazione "ITC" (ID JWT)**
- **Attestazione "ISS" (emittente)**
- **Attestazione "IAT" (rilasciato a)**
- **Attestazione "EXP" (ora di scadenza)**
- **Attestazione "NBF" (non prima)**

Di seguito sono riportate le attestazioni definite da [IETF Eat](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9) e usate dall'attestazione di Azure nell'oggetto Response:
- **"Nonce claim" (nonce)**

Le attestazioni seguenti vengono generate per impostazione predefinita in base alle attestazioni in ingresso
- **x-ms-ver**: versione dello schema JWT (prevista come "1,0")
- **x-ms-Attestation-Type**: valore stringa che rappresenta il tipo di attestazione 
- **x-ms-Policy-hash**: valore stringa contenente l'hash SHA256 del testo del criterio calcolato da BASE64URL (SHA256 (UTF8 (BASE64URL (UTF8 (testo del criterio))))
- **x-ms-Policy-Signer**: contiene un JWK con la chiave pubblica o la catena di certificati presente nell'intestazione dei criteri firmati. x-ms-Policy-firmatario viene aggiunto solo se il criterio è firmato

## <a name="claims-specific-to-sgx-enclaves"></a>Attestazioni specifiche per le enclavi SGX

### <a name="incoming-claims-specific-to-sgx-attestation"></a>Attestazioni in ingresso specifiche per l'attestazione SGX

Le attestazioni seguenti vengono generate dal servizio per l'attestazione SGX e possono essere usate per definire le regole di autorizzazione in un criterio personalizzato:
- **x-ms-SGX-is-debuggingable**: valore booleano che indica se l'enclave ha o meno abilitato il debug
- **x-ms-SGX-Product-ID**
- **x-ms-SGX-mrsigner**: valore con codifica esadecimale del campo "mrsigner" dell'offerta
- **x-ms-SGX-mrenclave**: valore con codifica esadecimale del campo "mrenclave" dell'offerta
- **x-ms-SGX-svn**: numero di versione della sicurezza codificato nell'offerta 

### <a name="outgoing-claims-specific-to-sgx-attestation"></a>Attestazioni in uscita specifiche per l'attestazione SGX

Le attestazioni seguenti vengono generate dal servizio e incluse nell'oggetto risposta per l'attestazione SGX:
- **x-ms-SGX-is-debuggingable**: valore booleano che indica se l'enclave ha o meno abilitato il debug
- **x-ms-SGX-Product-ID**
- **x-ms-SGX-mrsigner**: valore con codifica esadecimale del campo "mrsigner" dell'offerta
- **x-ms-SGX-mrenclave**: valore con codifica esadecimale del campo "mrenclave" dell'offerta
- **x-ms-SGX-svn**: numero di versione della sicurezza codificato nell'offerta 
- **x-ms-SGX-EHD**: dati contenuti nell'enclave formattati come BASE64URL (dati conservati nell'enclave)
- **x-ms-SGX-Collateral**: oggetto JSON che descrive il materiale collaterale usato per eseguire l'attestazione. Il valore per l'attestazione x-ms-SGX-Collateral è un oggetto JSON annidato con le coppie chiave/valore seguenti:
    - **qeidcertshash**: valore SHA256 dell'identità QE che emette i certificati
    - **qeidcrlhash**: valore SHA256 dell'identità QE che emette l'elenco CRL dei certificati
    - **qeidhash**: valore SHA256 della garanzia di identità QE
    - **quotehash**: valore SHA256 dell'offerta valutata
    - **tcbinfocertshash**: valore SHA256 delle informazioni TCB che emettono certificati
    - **tcbinfocrlhash**: valore SHA256 dell'elenco CRL delle informazioni TCB che emette i certificati
    - **tcbinfohash**: oggetto JSON che descrive il materiale collaterale usato per eseguire l'attestazione

Le attestazioni seguenti sono considerate deprecate, ma sono completamente supportate e continueranno a essere incluse in futuro. Si consiglia di usare i nomi delle attestazioni non deprecate.

Attestazione deprecata | Attestazione consigliata
--- | --- 
sottoposta a debug $is | x-ms-SGX-is-debuggingable
$sgx-mrsigner | x-ms-SGX-mrsigner
$sgx-mrenclave | x-ms-SGX-mrenclave
ID $product | x-ms-SGX-Product-ID
$svn | x-ms-SGX-SVN
$tee | x-ms-attestazione-tipo
MAA-EHD | x-ms-SGX-EHD
AAS-EHD | x-ms-SGX-EHD
MAA-attestationcollateral | x-ms-SGX-collaterale

## <a name="claims-specific-to-trusted-platform-module-tpm-vbs-attestation"></a>Attestazioni specifiche per Trusted Platform Module (TPM)/attestazione VBS

### <a name="incoming-claims-for-tpm-attestation"></a>Attestazioni in ingresso per l'attestazione TPM

Attestazioni rilasciate dall'attestazione di Azure per l'attestazione TPM. La disponibilità delle attestazioni dipende dall'evidenza fornita per l'attestazione.

- **aikValidated**: valore booleano che contiene informazioni se il certificato della chiave di identità di attestazione (AIK) è stato convalidato o meno
- **aikPubHash**: stringa contenente Base64 (SHA256 (chiave pubblica AIK nel formato der))
- **tpmVersion**: valore integer contenente la versione principale di Trusted Platform Module (TPM)
- **della securebootenabled**: valore booleano che indica se l'avvio protetto è abilitato
- **iommuEnabled**: valore booleano che indica se l'unità di gestione della memoria di input/output (IOMMU) è abilitata
- **bootDebuggingDisabled**: valore booleano che indica se il debug di avvio è disabilitato
- **notSafeMode**: valore booleano per indicare se Windows non è in esecuzione in modalità provvisoria
- **notWinPE**: valore booleano che indica se Windows non è in esecuzione in modalità WinPE
- **vbsEnabled**: valore booleano che indica se vbs è abilitato
- **vbsReportPresent**: valore booleano che indica se il rapporto enclave vbs è disponibile

### <a name="incoming-claims-for-vbs-attestation"></a>Attestazioni in ingresso per l'attestazione VBS

Le attestazioni rilasciate dall'attestazione di Azure per l'attestazione VBS sono in aggiunta alle attestazioni rese disponibili per l'attestazione TPM. La disponibilità delle attestazioni dipende dall'evidenza fornita per l'attestazione.

- **enclaveAuthorId**: valore stringa che contiene il valore codificato Base64Url dell'ID autore dell'enclave. identificatore autore del modulo primario per l'enclave
- **enclaveImageId**: valore stringa che contiene il valore codificato Base64Url dell'ID immagine dell'enclave, ovvero l'identificatore dell'immagine del modulo primario per l'enclave
- **enclaveOwnerId**: valore stringa contenente il valore codificato Base64Url dell'ID proprietario dell'enclave. identificatore del proprietario per l'enclave
- **enclaveFamilyId**: valore stringa che contiene il valore codificato BASE64URL dell'ID famiglia dell'enclave. Identificatore della famiglia del modulo primario per l'enclave
- **enclaveSvn**: valore integer contenente il numero di versione di sicurezza del modulo primario per l'enclave
- **enclavePlatformSvn**: valore integer contenente il numero della versione di sicurezza della piattaforma che ospita l'enclave
- **enclaveFlags**: l'attestazione enclaveFlags è un valore integer contenente i flag che descrivono i criteri di runtime per l'enclave

### <a name="outgoing-claims-specific-to-tpm-and-vbs-attestation"></a>Attestazioni in uscita specifiche per TPM e attestazione VBS

- **CNF (conferma)**: l'attestazione "CNF" viene usata per identificare la chiave di prova del possesso. Attestazione di conferma come definito nella RFC 7800, contiene la parte pubblica della chiave dell'enclave attestata rappresentata come oggetto chiave Web JSON (JWK) (RFC 7517)
- **rp_data (relying party data)**: i dati della relying party specificati nella richiesta, usati dal relying party come parametro nonce per garantire l'aggiornamento del report. rp_data viene aggiunto solo se è presente rp_data

### <a name="property-claims"></a>Attestazioni di proprietà

- **report_validity_in_minutes**: attestazione di tipo intero che indica la durata di validità del token.
  - **Default value(time)** : un giorno in minuti.
  - **Maximum value(time)** : un anno in minuti.
- **omit_x5c**: attestazione di tipo booleano che indica se Attestazione di Azure deve omettere il certificato usato per fornire la prova dell'autenticità del servizio. Se true, x5t verrà aggiunto al token di attestazione. Se false, x5c verrà aggiunto al token di attestazione.

## <a name="next-steps"></a>Passaggi successivi
- [Come creare e firmare un criterio di attestazione](author-sign-policy.md)
- [Configurare Attestazione di Azure con PowerShell](quickstart-powershell.md)
