---
title: Set di attestazioni di Attestazione di Azure
description: Il set di attestazioni di Attestazione di Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: e82e9fc93bf8c816fcbfd5869156745dea630313
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517556"
---
# <a name="claim-sets"></a>Set di attestazioni

Le attestazioni generate nel processo di attestazione di enclavi usando Attestazione di Microsoft Azure possono essere divise nelle categorie seguenti:

- **Attestazioni in** ingresso: attestazioni generate dall'attestazione Microsoft Azure dopo l'analisi dell'evidenza di attestazione e possono essere usate dagli autori di criteri per definire le regole di autorizzazione in un criterio personalizzato

- **Attestazioni in uscita:** attestazioni generate da attestazione di Azure e incluse nel token di attestazione

- **Attestazioni di** proprietà: attestazioni create come output da attestazione di Azure. Sono incluse tutte le attestazioni che rappresentano proprietà del token di attestazione, ad esempio la codifica del report, la durata di validità del report e così via.

## <a name="incoming-claims"></a>Attestazioni in ingresso 

### <a name="sgx-attestation"></a>Attestazione SGX

Attestazioni che gli autori di criteri devono usare per definire le regole di autorizzazione nei criteri di attestazione SGX:

- **x-ms-sgx-is-debuggable:** valore booleano che indica se il debug dell'enclave è abilitato o meno.
  
  Gli enclave SGX possono essere caricati con il debug disabilitato o abilitato. Quando il flag è impostato su true nell'enclave, abilita le funzionalità di debug per il codice dell'enclave. Ciò include la possibilità di accedere alla memoria dell'enclave. È quindi consigliabile impostare il flag su true solo a scopo di sviluppo. Se abilitata nell'ambiente di produzione, le garanzie di sicurezza SGX non verranno mantenute.
  
  attestazione di Azure utenti possono usare i criteri di attestazione per verificare se il debug è disabilitato per l'enclave SGX. Dopo aver aggiunto la regola dei criteri, l'attestazione avrà esito negativo quando un utente malintenzionato attiva il supporto per il debug per ottenere l'accesso al contenuto dell'enclave.

- **x-ms-sgx-product-id:** valore intero che indica l'ID prodotto dell'enclave SGX.

  L'autore dell'enclave assegna un ID prodotto a ogni enclave. L'ID prodotto consente all'autore dell'enclave di segmentare le enclave firmate usando lo stesso MRSIGNER. Aggiungendo una regola di convalida nei criteri di attestazione, i clienti possono verificare se usano gli enclave destinati. L'attestazione avrà esito negativo se l'ID prodotto dell'enclave non corrisponde al valore pubblicato dall'autore dell'enclave.

- **x-ms-sgx-mrsigner:** valore stringa che identifica l'autore dell'enclave SGX.

  MRSIGNER è l'hash della chiave pubblica dell'autore dell'enclave usato per firmare il file binario dell'enclave. Convalidando MRSIGNER tramite un criterio di attestazione, i clienti possono verificare se i file binari attendibili sono in esecuzione all'interno di un'enclave. Quando l'attestazione dei criteri non corrisponde a MRSIGNER dell'autore dell'enclave, implica che il file binario dell'enclave non è firmato da un'origine attendibile e l'attestazione ha esito negativo.
  
  Quando un autore dell'enclave preferisce ruotare MRSIGNER per motivi di sicurezza, è necessario aggiornare i criteri attestazione di Azure per supportare i valori MRSIGNER nuovi e precedenti prima dell'aggiornamento dei file binari. In caso contrario, i controlli di autorizzazione avranno esito negativo e si verificheranno errori di attestazione.
  
  I criteri di attestazione devono essere aggiornati usando il formato seguente. 
 
  #### <a name="before-key-rotation"></a>Prima della rotazione delle chiavi
 
   ```
    version= 1.0;
    authorizationrules 
    {
    [ type=="x-ms-sgx-is-debuggable", value==false]&&
    [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
    };
  ```

   #### <a name="during-key-rotation"></a>Durante la rotazione delle chiavi

    ```
      version= 1.0;
      authorizationrules 
      {
      [ type=="x-ms-sgx-is-debuggable", value==false]&&
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner1"] => permit(); 
      [ type=="x-ms-sgx-is-debuggable", value==false ]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

   #### <a name="after-key-rotation"></a>Dopo la rotazione delle chiavi

    ```
      version= 1.0;
      authorizationrules 
      { 
      [ type=="x-ms-sgx-is-debuggable", value==false]&& 
      [ type=="x-ms-sgx-mrsigner", value=="mrsigner2"] => permit(); 
      };
    ```

- **x-ms-sgx-mrenclave:** valore stringa che identifica il codice e i dati caricati nella memoria dell'enclave. 

  MRENCLAVE è una delle misurazioni dell'enclave che possono essere usate per verificare i file binari dell'enclave. È l'hash del codice in esecuzione all'interno dell'enclave. La misura cambia a ogni modifica al codice binario dell'enclave. Convalidando MRENCLAVE tramite criteri di attestazione, i clienti possono verificare se i file binari sono in esecuzione all'interno di un enclave. Tuttavia, poiché si prevede che MRENCLAVE cambi di frequente con qualsiasi modifica semplice al codice esistente, è consigliabile verificare i file binari dell'enclave usando la convalida MRSIGNER in un criterio di attestazione.

- **x-ms-sgx-svn:** valore intero che indica il numero di versione di sicurezza dell'enclave SGX

  L'autore dell'enclave assegna un numero di versione di sicurezza (SVN) a ogni versione dell'enclave SGX. Quando viene individuato un problema di sicurezza nel codice dell'enclave, l'autore dell'enclave incrementa il valore SVN dopo la correzione della vulnerabilità. Per impedire l'interazione con codice enclave non sicuro, i clienti possono aggiungere una regola di convalida nei criteri di attestazione. Se il numero SVN del codice dell'enclave non corrisponde alla versione consigliata dall'autore dell'enclave, l'attestazione avrà esito negativo.

Le attestazioni seguenti sono considerate deprecate, ma sono completamente supportate e continueranno a essere incluse in futuro. È consigliabile usare i nomi di attestazione non deprecati.

Attestazione deprecata | Attestazione consigliata
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn

### <a name="tpm-attestation"></a>Attestazione TPM

Attestazioni che gli autori dei criteri devono usare per definire le regole di autorizzazione nei criteri di attestazione TPM:

- **aikValidated:** valore booleano contenente informazioni se il certificato della chiave di identità di attestazione (AIK) è stato convalidato o meno
- **aikPubHash:** stringa contenente base64(SHA256 (chiave pubblica AIK in formato DER))
- **tpmVersion:** valore intero contenente la versione principale Trusted Platform Module (TPM)
- **secureBootEnabled:** valore booleano che indica se l'avvio protetto è abilitato
- **iommuEnabled:** valore booleano che indica se l'unità di gestione della memoria di input/output (Iommu) è abilitata
- **bootDebuggingDisabled:** valore booleano che indica se il debug di avvio è disabilitato
- **notSafeMode:** valore booleano che indica se Windows non è in esecuzione in modalità sicura
- **notWinPE:** valore booleano che indica se Windows non è in esecuzione in modalità WinPE
- **vbsEnabled:** valore booleano che indica se VBS è abilitato
- **vbsReportPresent:** valore booleano che indica se il report dell'enclave VBS è disponibile

### <a name="vbs-attestation"></a>Attestazione VBS

Oltre alle attestazioni dei criteri di attestazione TPM, le attestazioni seguenti possono essere usate dagli autori di criteri per definire le regole di autorizzazione in un criterio di attestazione VBS.

- **enclaveAuthorId:** valore stringa contenente il valore codificato Base64Url dell'id dell'autore dell'enclave. Identificatore dell'autore del modulo primario per l'enclave
- **enclaveImageId:** valore stringa contenente il valore codificato Base64Url dell'id immagine dell'enclave. Identificatore dell'immagine del modulo primario per l'enclave
- **enclaveOwnerId:** valore stringa contenente il valore codificato Base64Url dell'ID proprietario dell'enclave: identificatore del proprietario per l'enclave
- **enclaveFamilyId:** valore stringa contenente il valore codificato Base64Url dell'ID famiglia dell'enclave. Identificatore della famiglia del modulo primario per l'enclave
- **enclaveSvn:** valore intero contenente il numero di versione di sicurezza del modulo primario per l'enclave
- **enclavePlatformSvn:** valore intero contenente il numero di versione di sicurezza della piattaforma che ospita l'enclave
- **enclaveFlags:** l'attestazione enclaveFlags è un valore Integer contenente Flag che descrivono i criteri di runtime per l'enclave

## <a name="outgoing-claims"></a>Attestazioni in uscita 

### <a name="common-for-all-attestation-types"></a>Comune per tutti i tipi di attestazione

attestazione di Azure le attestazioni seguenti nel token di attestazione per tutti i tipi di attestazione. 

- **x-ms-ver:** versione dello schema JWT (prevista "1.0")
- **x-ms-attestation-type:** valore stringa che rappresenta il tipo di attestazione 
- **x-ms-policy-hash:** hash dei criteri di valutazione attestazione di Azure calcolati come BASE64URL(SHA256(UTF8(BASE64URL(UTF8(policy text)))))
- **x-ms-policy-signer:** oggetto JSON con un membro "jwk" che rappresenta la chiave usata da un cliente per firmare i criteri. Questo è applicabile quando il cliente carica un criterio firmato

I nomi di attestazione seguenti vengono usati dalla [specifica JWT IETF](https://tools.ietf.org/html/rfc7519)

- **Attestazione "jti" (ID JWT):** identificatore univoco per il token JWT
- **Attestazione "iss" (autorità emittente):** entità che ha emesso il token JWT 
- **Attestazione "iat" (rilasciato alle):** ora in cui il token JWT è stato rilasciato in 
- **Attestazione "exp" (ora di scadenza):** ora di scadenza dopo la quale il token JWT non deve essere accettato per l'elaborazione
- **Attestazione "nbf" (Not Before)** - Not Before time before which the JWT must not be accepted for processing 

I nomi di attestazione seguenti vengono usati dalla [specifica IETF EAT draft](https://tools.ietf.org/html/draft-ietf-rats-eat-03#page-9)

- **"Attestazione nonce" (nonce):** copia diretta non trasformata di un valore nonce facoltativo fornito da un client 

Le attestazioni seguenti sono considerate deprecate, ma sono completamente supportate e continueranno a essere incluse in futuro. È consigliabile usare i nomi di attestazione non deprecati.

Attestazione deprecata | Attestazione consigliata
--- | --- 
ver | x-ms-ver
tee | x-ms-attestation-type
policy_hash | x-ms-policy-hash
maa-policyHash | x-ms-policy-hash
policy_signer  | x-ms-policy-signer

### <a name="sgx-attestation"></a>Attestazione SGX 

Le attestazioni seguenti vengono generate e incluse nel token di attestazione dal servizio per l'attestazione SGX.

- **x-ms-sgx-is-debuggable:** valore booleano che indica se il debug dell'enclave è abilitato o meno
- **x-ms-sgx-product-id:** valore dell'ID prodotto dell'enclave SGX 
- **x-ms-sgx-mrsigner:** valore con codifica esadecimale del campo "mrsigner" dell'offerta
- **x-ms-sgx-mrenclave:** valore con codifica esadecimale del campo "mrenclave" dell'offerta
- **x-ms-sgx-svn:** numero di versione di sicurezza codificato nell'offerta 
- **x-ms-sgx-ehd:** l'enclave ha mantenuto i dati formattati come BASE64URL(enclave held data)
- **x-ms-sgx-collateral:** oggetto JSON che descrive il materiale collaterale usato per eseguire l'attestazione. Il valore per l'attestazione x-ms-sgx-collateral è un oggetto JSON annidato con le coppie chiave/valore seguenti:
    - **qeidcertshash:** valore SHA256 di Quoting Enclave (QE) Identity issuing certs
    - **qeidcrlhash:** valore SHA256 dell'identità QE che emette l'elenco CRL dei certificati
    - **qeidhash:** valore SHA256 del materiale collaterale QE Identity
    - **quotehash:** valore SHA256 dell'offerta valutata
    - **tcbinfocertshash:** valore SHA256 dei certificati emittente delle informazioni TCB
    - **tcbinfocrlhash:** valore SHA256 dell'elenco CRL dei certificati emessi da TCB
    - **tcbinfohash:** valore SHA256 del materiale informativo TCB

Le attestazioni seguenti sono considerate deprecate, ma sono completamente supportate e continueranno a essere incluse in futuro. È consigliabile usare i nomi di attestazione non deprecati.

Attestazione deprecata | Attestazione consigliata
--- | --- 
$is-debuggable | x-ms-sgx-is-debuggable
$product-id | x-ms-sgx-product-id
$sgx-mrsigner | x-ms-sgx-mrsigner
$sgx-mrenclave | x-ms-sgx-mrenclave
$svn | x-ms-sgx-svn
$maa-ehd | x-ms-sgx-ehd
$aas-ehd | x-ms-sgx-ehd
$maa-attestationcollateral | x-ms-sgx-collateral

### <a name="tpm-and-vbs-attestation"></a>Attestazione TPM e VBS

- **cnf (conferma):** l'attestazione "cnf" viene usata per identificare la chiave di prova di possesso. L'attestazione di conferma definita in RFC 7800 contiene la parte pubblica della chiave dell'enclave attestata rappresentata come oggetto JSON Web Key (JWK) (RFC 7517)
- **rp_data (relying party dati):** dati della relying party, se presenti, specificati nella richiesta, usati dal relying party come nonce per garantire l'attendibilità del report. rp_data viene aggiunto solo se è presente rp_data

## <a name="property-claims"></a>Attestazioni di proprietà

### <a name="tpm-and-vbs-attestation"></a>Attestazione TPM e VBS

- **report_validity_in_minutes:** attestazione integer per indicare per quanto tempo il token è valido.
  - **Default value(time)** : un giorno in minuti.
  - **Maximum value(time)** : un anno in minuti.
- **omit_x5c**: attestazione di tipo booleano che indica se Attestazione di Azure deve omettere il certificato usato per fornire la prova dell'autenticità del servizio. Se true, x5t verrà aggiunto al token di attestazione. Se false, x5c verrà aggiunto al token di attestazione.

## <a name="next-steps"></a>Passaggi successivi
- [Come creare e firmare un criterio di attestazione](author-sign-policy.md)
- [Configurare Attestazione di Azure con PowerShell](quickstart-powershell.md)
