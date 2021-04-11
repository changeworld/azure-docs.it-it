---
title: Set di attestazioni di Attestazione di Azure
description: Il set di attestazioni di Attestazione di Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 0d6d5a08ea85ebb666acc0336f1e1d7ec5e097da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105044669"
---
# <a name="claim-sets"></a>Set di attestazioni

Le attestazioni generate nel processo di attestazione di enclavi usando Attestazione di Microsoft Azure possono essere divise nelle categorie seguenti:

- **Attestazioni in ingresso**: attestazioni generate dall'attestazione Microsoft Azure dopo l'analisi dell'evidenza di attestazione e possono essere usate dagli autori di criteri per definire le regole di autorizzazione in un criterio personalizzato

- **Attestazioni in uscita**: attestazioni generate dall'attestazione di Azure e incluse nel token di attestazione

- **Attestazioni proprietà**: attestazioni create come output dall'attestazione di Azure. Sono incluse tutte le attestazioni che rappresentano proprietà del token di attestazione, ad esempio la codifica del report, la durata di validità del report e così via.

## <a name="incoming-claims"></a>Attestazioni in ingresso 

### <a name="sgx-attestation"></a>Attestazione SGX

Attestazioni che devono essere usate dagli autori di criteri per definire le regole di autorizzazione in un criterio di attestazione SGX:

- **x-ms-SGX-is-debuggingable**: valore booleano che indica se l'enclave ha o meno abilitato il debug
- **x-ms-SGX-Product-ID**: valore ID prodotto dell'ENCLAVe SGX 
- **x-ms-SGX-mrsigner**: valore con codifica esadecimale del campo "mrsigner" dell'offerta
- **x-ms-SGX-mrenclave**: valore con codifica esadecimale del campo "mrenclave" dell'offerta
- **x-ms-SGX-svn**: numero di versione della sicurezza codificato nell'offerta 

Le attestazioni seguenti sono considerate deprecate, ma sono completamente supportate e continueranno a essere incluse in futuro. Si consiglia di usare i nomi delle attestazioni non deprecate.

Attestazione deprecata | Attestazione consigliata
--- | --- 
sottoposta a debug $is | x-ms-SGX-is-debuggingable
ID $product | x-ms-SGX-Product-ID
$sgx-mrsigner | x-ms-SGX-mrsigner
$sgx-mrenclave | x-ms-SGX-mrenclave
$svn | x-ms-SGX-SVN

### <a name="tpm-attestation"></a>Attestazione TPM

Attestazioni che devono essere utilizzate dagli autori di criteri per definire le regole di autorizzazione in un criterio di attestazione TPM:

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

### <a name="vbs-attestation"></a>Attestazione VBS

Oltre alle attestazioni dei criteri di attestazione TPM, le attestazioni seguenti possono essere utilizzate dagli autori dei criteri per definire le regole di autorizzazione in un criterio di attestazione VBS.

- **enclaveAuthorId**: valore stringa che contiene il valore codificato Base64Url dell'ID autore dell'enclave. identificatore autore del modulo primario per l'enclave
- **enclaveImageId**: valore stringa che contiene il valore codificato Base64Url dell'ID immagine dell'enclave, ovvero l'identificatore dell'immagine del modulo primario per l'enclave
- **enclaveOwnerId**: valore stringa contenente il valore codificato Base64Url dell'ID proprietario dell'enclave. identificatore del proprietario per l'enclave
- **enclaveFamilyId**: valore stringa che contiene il valore codificato BASE64URL dell'ID famiglia dell'enclave. Identificatore della famiglia del modulo primario per l'enclave
- **enclaveSvn**: valore integer contenente il numero di versione di sicurezza del modulo primario per l'enclave
- **enclavePlatformSvn**: valore integer contenente il numero della versione di sicurezza della piattaforma che ospita l'enclave
- **enclaveFlags**: l'attestazione enclaveFlags è un valore integer contenente i flag che descrivono i criteri di runtime per l'enclave

## <a name="outgoing-claims"></a>Attestazioni in uscita 

### <a name="common-for-all-attestation-types"></a>Comune per tutti i tipi di attestazione

L'attestazione di Azure include le seguenti attestazioni nel token di attestazione per tutti i tipi di attestazione. 

- **x-ms-ver**: versione dello schema JWT (prevista come "1,0")
- **x-ms-Attestation-Type**: valore stringa che rappresenta il tipo di attestazione 
- **x-ms-Policy-hash**: hash dei criteri di valutazione dell'attestazione di Azure calcolati come BASE64URL (SHA256 (UTF8 (BASE64URL (UTF8 (testo del criterio))))
- **x-ms-Policy-Signer**: oggetto JSON con un membro "JWK" che rappresenta la chiave utilizzata da un cliente per firmare i criteri. Questa operazione è applicabile quando il cliente carica un criterio firmato

I nomi delle attestazioni seguenti vengono usati dalla [specifica IETF JWT](https://tools.ietf.org/html/rfc7519)

- **attestazione "ITC" (ID JWT)** : identificatore univoco per JWT
- **attestazione "ISS" (emittente)** : l'entità che ha emesso il JWT 
- **attestazione "IAT" (rilasciato a)** : ora in cui è stato emesso il JWT 
- **attestazione "EXP" (ora di scadenza)** : ora di scadenza dopo la quale il JWT non deve essere accettato per l'elaborazione
- **attestazione "NBF" (non prima)** : non prima dell'ora prima della quale JWT non deve essere accettato per l'elaborazione 

I nomi delle attestazioni seguenti vengono usati da [IETF Eat Draft Specification](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **"Nonce claim" (nonce)** : copia diretta non trasformata di un valore nonce facoltativo fornito da un client 

Le attestazioni seguenti sono considerate deprecate, ma sono completamente supportate e continueranno a essere incluse in futuro. Si consiglia di usare i nomi delle attestazioni non deprecate.

Attestazione deprecata | Attestazione consigliata
--- | --- 
ver | x-ms-ver
tee | x-ms-attestazione-tipo
policy_hash | x-ms-Policy-hash
MAA-policyHash | x-ms-Policy-hash
policy_signer  | x-ms-Policy-firmatario

### <a name="sgx-attestation"></a>Attestazione SGX 

Le attestazioni seguenti vengono generate e incluse nel token di attestazione dal servizio per l'attestazione SGX.

- **x-ms-SGX-is-debuggingable**: valore booleano che indica se l'enclave ha o meno abilitato il debug
- **x-ms-SGX-Product-ID**: valore ID prodotto dell'ENCLAVe SGX 
- **x-ms-SGX-mrsigner**: valore con codifica esadecimale del campo "mrsigner" dell'offerta
- **x-ms-SGX-mrenclave**: valore con codifica esadecimale del campo "mrenclave" dell'offerta
- **x-ms-SGX-svn**: numero di versione della sicurezza codificato nell'offerta 
- **x-ms-SGX-EHD**: dati contenuti nell'enclave formattati come BASE64URL (dati conservati nell'enclave)
- **x-ms-SGX-Collateral**: oggetto JSON che descrive il materiale collaterale usato per eseguire l'attestazione. Il valore per l'attestazione x-ms-SGX-Collateral è un oggetto JSON annidato con le coppie chiave/valore seguenti:
    - **qeidcertshash**: valore SHA256 dell'identità dell'enclave di quota (QE) che emette i certificati
    - **qeidcrlhash**: valore SHA256 dell'identità QE che emette l'elenco CRL dei certificati
    - **qeidhash**: valore SHA256 della garanzia di identità QE
    - **quotehash**: valore SHA256 dell'offerta valutata
    - **tcbinfocertshash**: valore SHA256 delle informazioni TCB che emettono certificati
    - **tcbinfocrlhash**: valore SHA256 dell'elenco CRL delle informazioni TCB che emette i certificati
    - **tcbinfohash**: valore SHA256 della garanzia di informazioni TCB

Le attestazioni seguenti sono considerate deprecate, ma sono completamente supportate e continueranno a essere incluse in futuro. Si consiglia di usare i nomi delle attestazioni non deprecate.

Attestazione deprecata | Attestazione consigliata
--- | --- 
sottoposta a debug $is | x-ms-SGX-is-debuggingable
ID $product | x-ms-SGX-Product-ID
$sgx-mrsigner | x-ms-SGX-mrsigner
$sgx-mrenclave | x-ms-SGX-mrenclave
$svn | x-ms-SGX-SVN
$maa-EHD | x-ms-SGX-EHD
$aas-EHD | x-ms-SGX-EHD
$maa-attestationcollateral | x-ms-SGX-collaterale

### <a name="tpm-and-vbs-attestation"></a>Attestazione TPM e VBS

- **CNF (conferma)**: l'attestazione "CNF" viene usata per identificare la chiave di prova del possesso. Attestazione di conferma come definito nella RFC 7800, contiene la parte pubblica della chiave dell'enclave attestata rappresentata come oggetto chiave Web JSON (JWK) (RFC 7517)
- **rp_data (relying party data)**: i dati della relying party specificati nella richiesta, usati dal relying party come parametro nonce per garantire l'aggiornamento del report. rp_data viene aggiunto solo se è presente rp_data

## <a name="property-claims"></a>Attestazioni di proprietà

### <a name="tpm-and-vbs-attestation"></a>Attestazione TPM e VBS

- **report_validity_in_minutes**: attestazione integer per indicare per quanto tempo il token è valido.
  - **Default value(time)** : un giorno in minuti.
  - **Maximum value(time)** : un anno in minuti.
- **omit_x5c**: attestazione di tipo booleano che indica se Attestazione di Azure deve omettere il certificato usato per fornire la prova dell'autenticità del servizio. Se true, x5t verrà aggiunto al token di attestazione. Se false, x5c verrà aggiunto al token di attestazione.

## <a name="next-steps"></a>Passaggi successivi
- [Come creare e firmare un criterio di attestazione](author-sign-policy.md)
- [Configurare Attestazione di Azure con PowerShell](quickstart-powershell.md)
