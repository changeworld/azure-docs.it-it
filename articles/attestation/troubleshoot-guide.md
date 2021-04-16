---
title: Guida alla risoluzione dei problemi di attestazione di Azure
description: Guida alla risoluzione dei problemi per i problemi comunemente osservati
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: 5eefcb55bb5447d557f097af872847576aa86eed
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519307"
---
# <a name="microsoft-azure-attestation-troubleshooting-guide"></a>Microsoft Azure risoluzione dei problemi di Attestazione

La gestione degli errori in attestazione di Azure viene implementata seguendo le [linee guida dell'API REST Microsoft](https://github.com/microsoft/api-guidelines/blob/vNext/Guidelines.md#7102-error-condition-responses). La risposta di errore restituita attestazione di Azure API contiene coppie nome/valore e codice di stato HTTP con i nomi "code" e "message". Il valore di "code" è leggibile dall'utente ed è un indicatore del tipo di errore. Il valore di "message" intende aiutare l'utente e fornisce i dettagli dell'errore.

Se il problema non viene risolto in questo articolo, è anche possibile inviare una richiesta di supporto tecnico di Azure nella supporto tecnico di Azure [pagina](https://azure.microsoft.com/support/options/).

Di seguito sono riportati alcuni esempi degli errori restituiti da attestazione di Azure:

## <a name="1-http401--unauthorized-exception"></a>1. HTTP-401: Eccezione non autorizzata

### <a name="http-status-code"></a>Codice di stato HTTP
401

**Codice errore** Non autorizzato

**Esempi di scenari**
  - Non è possibile gestire i criteri di attestazione perché all'utente non sono assegnati ruoli appropriati
  - Non è possibile gestire i firmatari dei criteri di attestazione perché all'utente non sono assegnati ruoli appropriati

Utente con ruolo Lettore che tenta di modificare un criterio di attestazione in PowerShell 

  ```powershell
  Set-AzAttestationPolicy : Operation returned HTTP Status Code 401
At line:1 char:1
+ Set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
  ```

**Passaggi per la risoluzione dei problemi**

Per gestire i criteri, gli utenti di Azure AD devono avere le autorizzazioni seguenti per "Azioni":
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

  Per eseguire queste azioni, un Azure AD utente deve avere il ruolo "Collaboratore attestazione" nel provider di attestazione. Queste autorizzazioni possono essere ereditate anche con ruoli come "Proprietario" (autorizzazioni con caratteri jolly), "Collaboratore" (autorizzazioni con caratteri jolly) nella sottoscrizione o nel gruppo di risorse.  

Per leggere i criteri, gli utenti di Azure AD devono avere l'autorizzazione seguente per "Azioni":
- Microsoft.Attestation/attestationProviders/attestation/read

  Per eseguire questa azione, un Azure AD utente deve avere il ruolo "Lettore di attestazione" nel provider di attestazione. L'autorizzazione di lettura può essere ereditata anche con ruoli come "Lettore" (autorizzazioni con caratteri jolly) nella sottoscrizione o nel gruppo di risorse.  

Per verificare i ruoli in PowerShell, eseguire la procedura seguente:

a. Avviare PowerShell e accedere ad Azure tramite il cmdlet "Connect-AzAccount"

b. Fare riferimento alle indicazioni [riportate qui per](../role-based-access-control/role-assignments-list-powershell.md) verificare l'assegnazione di ruolo di Azure nel provider di attestazione

c. Se non si trova un'assegnazione di ruolo appropriata, seguire le istruzioni [riportate](../role-based-access-control/role-assignments-powershell.md) qui

## <a name="2-http--400-errors"></a>2. Errori HTTP - 400

### <a name="http-status-code"></a>Codice di stato HTTP
400

Esistono diversi motivi per cui una richiesta può restituire 400. Di seguito sono riportati alcuni esempi di errori restituiti attestazione di Azure API:

### <a name="21-attestation-failure-due-to-policy-evaluation-errors"></a>2.1. Errore di attestazione a causa di errori di valutazione dei criteri

I criteri di attestazione includono regole di autorizzazione e regole di rilascio. L'evidenza dell'enclave viene valutata in base alle regole di autorizzazione. Le regole di rilascio definiscono le attestazioni da includere nel token di attestazione. Se le attestazioni nell'evidenza dell'enclave non sono conformi alle regole di autorizzazione, le chiamate attest restituiranno un errore di valutazione dei criteri. 

**Codice errore** PolicyEvaluationError

**Esempi di scenari** Quando le attestazioni nell'offerta dell'enclave non corrispondono alle regole di autorizzazione dei criteri di attestazione

```
Native operation failed with 65518: G:\Az\security\Attestation\src\AttestationServices\Instance\NativePolicyWrapper\NativePolicyEngine.cpp(168)\(null)!00007FF801762308: (caller: 00007FF80143DCC8) Exception(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Policy Engine Exception: A Deny claim was issued, authorization failed.]

G:\Az\security\Attestation\src\AttestationServices\Instance\Enclave\api.cpp(840)\(null)!00007FF801739FF3: (caller: 00007FF801232801) LogHr(0) 83FFFFEE Policy Evaluation Error has occurred Msg:[Unhandled Enclave Exception: "Policy Evaluation Error has occurred"]

```

**Passaggi per la risoluzione dei problemi** Gli utenti possono valutare l'evidenza dell'enclave rispetto a un criterio di attestazione SGX prima di configurare lo stesso.

Inviare una richiesta all'API di attestazione fornendo il testo dei criteri nel parametro "draftPolicyForAttestation". L'API AttestSgxEnclave userà questo documento di criteri durante la chiamata di attestazione e può essere usato per testare i criteri di attestazione prima che siano usati. Il token di attestazione generato quando questo campo è presente non sarà protetto.

Vedere esempi [di criteri di attestazione](./policy-examples.md)

### <a name="22-attestation-failure-due-to-invalid-input"></a>2.2. Errore di attestazione a causa di un input non valido

**Codice errore** InvalidParameter

**Esempi di scenari** Errore di attestazione SGX a causa di un input non valido. Di seguito sono riportati alcuni esempi di messaggi di errore:
- La citazione specificata non è valida a causa di un errore nel materiale collaterale dell'offerta 
- L'offerta specificata non è valida perché il dispositivo in cui è stata generata l'offerta non soddisfa i requisiti di base di Azure
- La citazione specificata non è valida perché il valore TCBInfo o QEID fornito dal servizio cache PCK non è valido

**Passaggi per la risoluzione dei problemi**

Microsoft Azure'attestazione supporta l'attestazione delle citazioni SGX generate da Intel SDK e Open Enclave SDK.

Fare riferimento agli [esempi di codice](/samples/browse/?expanded=azure&terms=attestation) per l'esecuzione dell'attestazione con Open Enclave SDK/Intel SDK

### <a name="23-invalid-certificate-chain-error-while-uploading-policypolicy-signer"></a>2.3. Errore della catena di certificati non valido durante il caricamento di criteri/firmatario di criteri

**Codice errore** InvalidParameter

**Esempi di scenari** Configurare criteri firmati o aggiungere/eliminare un firmatario di criteri firmato con una catena di certificati non valida, ad esempio quando l'estensione Vincoli di base del certificato radice non è impostata su Tipo soggetto = CA.

```
Native operation failed with 65529: C:\source\src\AttestationServices\Instance\SgxPal\sgxcert.cpp(1074)\(null)!00007FFA285CDAED: (caller: 00007FFA285C36E8) Exception(0) 83FFFFF9 The requested item is not found    Msg:[Unable to find issuer certificate CN=attestationsigningcert]
C:\source\src\AttestationServices\Instance\Enclave\api.cpp(618)\(null)!00007FFA286DCBF8: (caller: 00007FFA285860D3) LogHr(0) 83FFFFF9 The requested item is not found    Msg:[Unhandled Enclave Exception: "The requested item is not found"]
At line:1 char:1
+ Set-AzAttestationPolicy -Name "testpolicy1" -ResourceGroupName "BugBa ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy

```

**Passaggi per la risoluzione dei problemi** Il certificato radice deve essere contrassegnato come emesso da una CA (i vincoli di base X.509), altrimenti non verrà considerato come un certificato valido. 

Assicurarsi che l'estensione Basic Constraints del certificato radice sia impostata per indicare che Subject Type = CA

In altro modo, la catena di certificati viene considerata non valida.

Vedere [esempi di criteri e firmatari](./policy-signer-examples.md) [di](./policy-examples.md) criteri 

### <a name="24-adddelete-policy-signer-failure"></a>2.4. Errore di aggiunta/eliminazione del firmatario di criteri

**Codice errore** InvalidOperation

**Esempi di scenari**

Quando l'utente carica JWS senza attestazione "maa-policyCertificate"

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\Enclave\enclave.cpp(2213)\(null)!: (caller: ) Exception(0) 83FF004A Bad
message    Msg:[Could not find "maa-policyCertificate" claim in policy token]
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner

```

Quando l'utente non carica un certificato in formato JWS

```
Add-AzAttestationPolicySigner : Operation returned HTTP Status Code 400
Code: InvalidOperation
Message: Native operation failed with 74: ..\JsonWebToken\jsonwebtoken.cpp(375)\(null)!: (caller: ) LogHr(0) 83FF004A
Bad message    Msg:[RETURN_IF_TRUE('(firstPeriod == std::string::npos)') failed with 0x4a: Malformed JWT: Could not
find first period in the token.]
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message
Msg:[THROW_IF_ERROR('DecomposeJsonWebSignature(&policyJws, encodedJoseHeader, encodedJwsBody, jwsSignature)') failed
with 0x4a: 'Bad message']
..\Enclave\enclave.cpp(2106)\(null)!: (caller: ) Exception(0) 83FF004A Bad message
..\Enclave\api.cpp(496)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad
message"]
At line:1 char:1
+ Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGro ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Add-AzAttestationPolicySigner], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.AddAzureAttestationPolicySigner
```

**Passaggi per la risoluzione dei problemi** Per aggiungere/eliminare un nuovo certificato del firmatario di criteri, usare RFC7519 token JSON Web (JWT) con un'attestazione denominata "x-ms-policyCertificate". Il valore dell'attestazione è un JSON Web Key RFC7517, che contiene il certificato da aggiungere. Il token JWT deve essere firmato con la chiave privata di uno dei certificati del firmatario di criteri validi associati al provider. Vedere [esempi di firmatari di criteri.](./policy-signer-examples.md)

### <a name="25-attestation-policy-configuration-failure"></a>2.5. Errore di configurazione dei criteri di attestazione

**Codice errore** PolicyParsingError

**Esempi di scenari** Criteri forniti con sintassi non corretta (ad esempio, punto e virgola mancante)/criteri JWT validi)

```
Native operation failed with 65526: ..\NativePolicyWrapper\NativePolicyEngine.cpp(31)\(null)!: (caller: ) Exception(0) 83FFFFF6 Invalid policy was specified    Msg:[Policy Parser Exception Thrown: Offending
symbol: '['
Line: '2', Column: '1'
Failure message: 'mismatched input '[' expecting ';''
Failing rule: 'policy > versionInfo']
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FFFFF6 Invalid policy was specified    Msg:[Unhandled Enclave Exception: "Invalid policy was specified"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Codice errore** InvalidOperation

**Esempi di scenari** È stato specificato contenuto non valido (ad esempio, criteri di caricamento/criteri non firmati quando è necessaria la firma dei criteri)

```
Native operation failed with 74: ..\Shared\base64url.h(226)\(null)!: (caller: ) Exception(0) 83FF004A Bad message    Msg:[Unknown base64 character: 41 (')')]
..\Enclave\api.cpp(618)\(null)!: (caller: ) LogHr(0) 83FF004A Bad message    Msg:[Unhandled Enclave Exception: "Bad message"]
At line:1 char:1
+ set-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzAttestationPolicy], RestException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.Attestation.SetAzureAttestationPolicy
```

**Passaggi per la risoluzione dei problemi** Assicurarsi che il criterio in formato testo sia con codifica UTF-8.

Se è necessaria la firma dei criteri, i criteri di attestazione devono essere configurati solo nel formato RFC7519 token JSON Web (JWT). Se la firma dei criteri non è necessaria, i criteri possono essere configurati in formato testo o JWT.

Per configurare un criterio in formato JWT, usare JWT con un'attestazione denominata "AttestationPolicy". Il valore dell'attestazione è la versione con codifica Base64URL del testo dei criteri. Se il provider di attestazione è configurato con certificati del firmatario di criteri, il token JWT deve essere firmato con la chiave privata di uno dei certificati del firmatario di criteri validi associati al provider. 

Per configurare un criterio in formato testo, specificare direttamente il testo dei criteri.

In PowerShell specificare PolicyFormat come JWT per configurare i criteri in formato JWT. Il formato predefinito dei criteri è Testo.

Vedere esempi di criteri [di attestazione](./policy-examples.md) [e come creare un criterio di attestazione](./author-sign-policy.md) 

## <a name="3-azattestation-installation-issues-in-powershell"></a>3. Problemi di installazione di Az.Attestation in PowerShell

Non è possibile installare i moduli Az o Az.Attestation in PowerShell

### <a name="error"></a>Errore

AVVISO: Impossibile risolvere l'origine del pacchetto ' ' PackageManagement\Install-Package : non è stata trovata alcuna corrispondenza per i criteri di ricerca e il nome https://www.powershellgallery.com/api/v2 del modulo specificati

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

PowerShell Gallery ha deprecato Transport Layer Security (TLS) 1.0 e 1.1. 

È consigliabile usare TLS 1.2 o versione successiva. 

Per continuare a interagire con PowerShell Gallery, eseguire il comando seguente prima dei comandi Install-Module

**[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12**

## <a name="4-policy-accessconfiguration-issues-in-powershell"></a>4. Problemi di accesso/configurazione dei criteri in PowerShell

Utente assegnato con i ruoli appropriati. Si verificano tuttavia problemi di autorizzazione durante la gestione dei criteri di attestazione tramite PowerShell.

### <a name="error"></a>Errore
Il client con ID oggetto ID oggetto ID non ha l'autorizzazione per eseguire l'azione &lt; &gt;  Microsoft.Authorization/roleassignments/write over scope 'subcriptions/ &lt; subscriptionId &gt; resourcegroups/secure_enclave_poc/providers/Microsoft.Authorization/roleassignments/role &lt; assignmentId' o l'ambito non è &gt; valido. Se l'accesso è stato concesso di recente, aggiornare le credenziali

### <a name="troubleshooting-steps"></a>Passaggi per la risoluzione dei problemi

La versione minima dei moduli Az necessari per supportare le operazioni di attestazione è la seguente: 

 **Az 4.5.0** 
 
 **Az.Accounts 1.9.2**
 
 **Az.Attestation 0.1.8** 

Eseguire il comando seguente per verificare la versione installata di tutti i moduli Az 

```powershell
Get-InstalledModule 
```

Se le versioni non corrispondono al requisito minimo, eseguire Update-Module comandi

ad esempio - Update-Module -Name Az.Attestation
