---
title: Come personalizzare le credenziali Azure Active Directory verificabili (anteprima)
description: Questo articolo illustra come creare credenziali verificabili personalizzate
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: c830f9c7edb252508824b3a92bd31b6fad31395d
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170068"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>Come personalizzare le credenziali verificabili (anteprima)

Le credenziali verificabili sono costituite da due componenti, ovvero le regole e i file di visualizzazione. Il file delle regole determina le operazioni che l'utente deve fornire prima di ricevere una credenziale verificabile. Il file di visualizzazione controlla la personalizzazione della credenziale e lo stile delle attestazioni. In questa guida verrà illustrato come modificare entrambi i file per soddisfare i requisiti dell'organizzazione. 

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rules-file-requirements-from-the-user"></a>File delle regole: requisiti dell'utente

Il file delle regole è un semplice file JSON che descrive le proprietà importanti delle credenziali verificabili. In particolare, descrive come vengono usate le attestazioni per popolare le credenziali verificabili.

Attualmente sono disponibili tre tipi di input che possono essere configurati nel file delle regole. Questi tipi vengono utilizzati dal servizio di emissione delle credenziali verificabile per inserire le attestazioni in una credenziale verificabile e attestare tali informazioni con il fatto. Di seguito sono riportati i tre tipi con spiegazioni.

- Token ID
- Credenziali verificabili tramite una presentazione verificabile.
- Attestazioni Self-Attested

**Token ID:** L'app di esempio e l'esercitazione usano il token ID. Quando questa opzione è configurata, sarà necessario specificare un URI di configurazione Open ID Connect e includere le attestazioni che devono essere incluse nel VC. All'utente verrà richiesto di accedere all'app Authenticator per soddisfare questo requisito e aggiungere le attestazioni associate dal proprio account. 

**Credenziali verificabili:** Il risultato finale di un flusso di rilascio è quello di produrre una credenziale verificabile, ma è anche possibile richiedere all'utente di presentare una credenziale verificabile per emetterne una. Il file delle regole è in grado di richiedere attestazioni specifiche dalle credenziali verificabili presentate e includere tali attestazioni nelle credenziali verificabili appena rilasciate dall'organizzazione. 

Attestazioni **autocertificate:** Quando questa opzione è selezionata, l'utente sarà in grado di digitare direttamente le informazioni nell'autenticatore. A questo punto, le stringhe sono l'unico input supportato per le attestazioni autocertificate. 

![visualizzazione dettagliata della scheda credenziali verificabile](media/credential-design/issuance-doc.png) 

**Attestazioni statiche:** Inoltre, è possibile dichiarare un'attestazione statica nel file delle regole, tuttavia questo input non proviene dall'utente. L'emittente definisce un'attestazione statica nel file delle regole e dovrebbe essere simile a qualsiasi altra attestazione nella credenziale verificabile. È sufficiente aggiungere un credentialSubject dopo VC. Type e dichiarare l'attributo e l'attestazione. 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>Tipo di input: token ID

Per ottenere il token ID come input, il file delle regole deve configurare l'endpoint noto del sistema di identità compatibile con OIDC. In tale sistema è necessario registrare un'applicazione con le informazioni corrette dagli [esempi di comunicazione del servizio dell'autorità emittente](issuer-openid.md). Inoltre, è necessario inserire il client_id nel file delle regole, oltre a un parametro di ambito che deve essere compilato con gli ambiti corretti. Ad esempio, Azure Active Directory necessita dell'ambito di posta elettronica se si vuole restituire un'attestazione di posta elettronica nel token ID.
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| Proprietà | Descrizione |
| -------- | ----------- |
| `attestations.idTokens` | Matrice di provider di identità OpenID Connect supportati per l'approvvigionamento delle informazioni utente. |
| `...mapping` | Oggetto che descrive la modalità di mapping delle attestazioni in ogni token ID agli attributi nella credenziale verificabile risultante. |
| `...mapping.{attribute-name}` | Attributo che deve essere popolato nella credenziale verificabile risultante. |
| `...mapping.{attribute-name}.claim` | Attestazione nei token ID il cui valore deve essere utilizzato per popolare l'attributo. |
| `...configuration` | Percorso del documento di configurazione del provider di identità. Questo URL deve rispettare lo [standard OpenID Connect per i metadati del provider di identità](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata). Il documento di configurazione deve includere `issuer` i `authorization_endpoint` campi,, `token_endpoint` e `jwks_uri` . |
| `...client_id` | ID client ottenuto durante il processo di registrazione client. |
| `...scope` | Elenco di ambiti delimitati da spazi in cui l'IDP deve essere in grado di restituire le attestazioni corrette nel token ID. |
| `...redirect_uri` | Deve sempre usare il valore `vcclient://openid/` . |
| `validityInterval` | Durata, in secondi, che rappresenta la durata delle credenziali verificabili. Una volta trascorso questo periodo di tempo, la credenziale verificabile non sarà più valida. Omettendo questo valore si indica che ogni credenziale verificabile rimarrà valida fino a quando non verrà revocata in modo esplicito. |
| `vc.type` | Matrice di stringhe che indica gli schemi che soddisfano le credenziali verificabili. Per altri dettagli, vedere la sezione seguente. |

### <a name="vctype-choose-credential-types"></a>VC. Type: scegliere i tipi di credenziali 

Tutte le credenziali verificabili devono dichiarare il "tipo" nel file delle regole. Il tipo di credenziale distingue le credenziali verificabili dalle credenziali emesse da altre organizzazioni e garantisce l'interoperabilità tra autorità emittenti e verificatori. Per indicare un tipo di credenziale, è necessario specificare uno o più tipi di credenziali che soddisfano le credenziali. Ogni tipo è rappresentato da una stringa univoca. spesso viene usato un URI per garantire l'univocità globale. Non è necessario che l'URI sia indirizzabile. viene considerato come una stringa. 

Ad esempio, le credenziali del diploma emesse da Contoso University possono dichiarare i tipi seguenti:

| Tipo | Scopo |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | Dichiara che i diplomi rilasciati da Contoso University contengono attributi definiti dall'oggetto schema. org `EducationaCredential` . |
| `https://schemas.ed.gov/universityDiploma2020` | Dichiara che i diplomi rilasciati da Contoso University contengono attributi definiti da Stati Uniti Department of Education. |
| `https://schemas.contoso.edu/diploma2020` | Dichiara che i diplomi rilasciati da Contoso University contengono attributi definiti da Contoso University. |

Dichiarando tutti e tre i tipi, i diplomi di Contoso University possono essere usati per soddisfare richieste diverse da verificatori. Una banca può richiedere un set di `EducationCredential` da un utente e il diploma può essere usato per soddisfare la richiesta. Tuttavia, l'associazione Alumni di Contoso University può richiedere una credenziale di tipo `https://schemas.contoso.edu/diploma2020` e il diploma soddisferà anche la richiesta.

Per garantire l'interoperabilità delle proprie credenziali, è consigliabile collaborare con le organizzazioni correlate per definire i tipi di credenziali, gli schemi e gli URI da usare nel settore. Molti corpi del settore forniscono indicazioni sulla struttura dei documenti ufficiali che possono essere riproposti per la definizione del contenuto di credenziali verificabili. È inoltre consigliabile collaborare con i verificatori delle proprie credenziali per comprendere come si intende richiedere e utilizzare le credenziali verificabili.

## <a name="input-type-verifiable-credential"></a>Tipo di input: credenziale verificabile

>[!NOTE]
>I file delle regole che richiedono una credenziale verificabile non utilizzano il formato Presentation Exchange per la richiesta di credenziali. Questa operazione verrà aggiornata quando il servizio emittente supporta il manifesto delle credenziali standard. 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| Proprietà | Descrizione |
| -------- | ----------- |
| `attestations.presentations` | Matrice di credenziali verificabili richieste come input. |
| `...mapping` | Oggetto che descrive in che modo viene eseguito il mapping delle attestazioni in ogni credenziale verificabile agli attributi nella credenziale verificabile risultante. |
| `...mapping.{attribute-name}` | Attributo che deve essere popolato nella credenziale verificabile risultante. |
| `...mapping.{attribute-name}.claim` | Attestazione nelle credenziali verificabili il cui valore deve essere utilizzato per popolare l'attributo. |
| `...mapping.{attribute-name}.indexed` | È possibile abilitare una sola credenziale verificabile per il salvataggio per REVOKE. Per ulteriori informazioni [, vedere l'articolo su come revocare le credenziali](how-to-issuer-revoke.md) . |
| `credentialType` | CredentialType della credenziale verificabile a cui si chiede di presentare l'utente. |
| `contracts` | URI del contratto nel portale del servizio credenziali verificabile. |
| `issuers.iss` | L'emittente ha fatto per la credenziale verificabile richiesta all'utente. |
| `validityInterval` | Durata, in secondi, che rappresenta la durata delle credenziali verificabili. Una volta trascorso questo periodo di tempo, la credenziale verificabile non sarà più valida. Omettendo questo valore si indica che ogni credenziale verificabile rimarrà valida fino a quando non verrà revocata in modo esplicito. |
| `vc.type` | Matrice di stringhe che indica gli schemi che soddisfano le credenziali verificabili. |


## <a name="input-type-self-attested-claims"></a>Tipo di input: attestazioni Self-Attested

Durante il flusso di rilascio, all'utente può essere richiesto di immettere alcune informazioni autocertificate. Al momento, l'unico tipo di input è "String". 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| Proprietà | Descrizione |
| -------- | ----------- |
| `attestations.selfIssued` | Matrice di attestazioni autocertificate che richiedono l'input dell'utente. |
| `...mapping` | Oggetto che descrive come viene eseguito il mapping delle attestazioni autocertificate agli attributi nella credenziale verificabile risultante. |
| `...mapping.alias` | Attributo che deve essere popolato nella credenziale verificabile risultante. |
| `...mapping.alias.claim` | Attestazione nelle credenziali verificabili il cui valore deve essere utilizzato per popolare l'attributo. |
| `validityInterval` | Durata, in secondi, che rappresenta la durata delle credenziali verificabili. Una volta trascorso questo periodo di tempo, la credenziale verificabile non sarà più valida. Omettendo questo valore si indica che ogni credenziale verificabile rimarrà valida fino a quando non verrà revocata in modo esplicito. |
| `vc.type` | Matrice di stringhe che indica gli schemi che soddisfano le credenziali verificabili. |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>File di visualizzazione: credenziali verificabili in Microsoft Authenticator

Le credenziali verificabili offrono un set limitato di opzioni che possono essere usate per riflettere il marchio. Questo articolo fornisce istruzioni su come personalizzare le credenziali e procedure consigliate per la progettazione di credenziali che hanno un aspetto ottimale una volta emesse agli utenti.

Le credenziali verificabili rilasciate agli utenti vengono visualizzate come schede in Microsoft Authenticator. In qualità di amministratore, è possibile scegliere il colore, l'icona e le stringhe di testo della scheda in modo che corrispondano al marchio dell'organizzazione.

![documentazione di rilascio](media/credential-design/detailed-view.png) 

Le schede contengono anche campi personalizzabili che è possibile usare per consentire agli utenti di conoscerne lo scopo, gli attributi che contiene e altro ancora.

## <a name="create-a-credential-display-file"></a>Creazione di un file di visualizzazione delle credenziali

Analogamente al file delle regole, il file di visualizzazione è un semplice file JSON che descrive la modalità di visualizzazione del contenuto delle credenziali verificabili nell'app Microsoft Authenticator. 

>[!NOTE]
> A questo punto, questo modello di visualizzazione viene usato solo da Microsoft Authenticator.

Il file di visualizzazione presenta la struttura seguente.

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| Proprietà | Descrizione |
| -------- | ----------- |
| `locale` | Lingua della credenziale verificabile. Riservato per utilizzi futuri. | 
| `card.title` | Visualizza il tipo di credenziale per l'utente. Lunghezza massima consigliata di 25 caratteri. | 
| `card.issuedBy` | Visualizza il nome dell'organizzazione emittente per l'utente. Lunghezza massima consigliata di 40 caratteri. |
| `card.backgroundColor` | Determina il colore di sfondo della scheda, in formato esadecimale. Una sfumatura sottile verrà applicata a tutte le schede. |
| `card.textColor` | Determina il colore del testo della scheda, in formato esadecimale. È consigliabile usare il nero o il bianco. |
| `card.logo` | Logo visualizzato sulla scheda. L'URL specificato deve essere indirizzabile pubblicamente. Altezza massima consigliata di 36 px e larghezza massima di 100 px indipendentemente dalle dimensioni del telefono. Consigliare PNG con sfondo trasparente. | 
| `card.description` | Testo supplementare visualizzato insieme a ogni scheda. Può essere usato per qualsiasi scopo. Lunghezza massima consigliata di 100 caratteri. |
| `consent.title` | Testo supplementare visualizzato quando viene emessa una scheda. Utilizzato per fornire dettagli sul processo di rilascio. Lunghezza consigliata di 100 caratteri. |
| `consent.instructions` | Testo supplementare visualizzato quando viene emessa una scheda. Utilizzato per fornire dettagli sul processo di rilascio. Lunghezza consigliata di 100 caratteri. |
| `claims` | Consente di specificare le etichette per gli attributi inclusi in ogni credenziale. |
| `claims.{attribute}` | Indica l'attributo della credenziale a cui si applica l'etichetta. |
| `claims.{attribute}.type` | Indica il tipo di attributo. Attualmente è supportato solo ' String '. |
| `claims.{attribute}.label` | Valore che deve essere utilizzato come etichetta per l'attributo, che verrà visualizzato in Authenticator. Questo potrebbe essere diverso dall'etichetta utilizzata nel file delle regole. Lunghezza massima consigliata di 40 caratteri. |

>[!note]
  >Se un'attestazione viene inclusa nel file delle regole e quindi omessa nel file di visualizzazione, esistono due tipi diversi di esperienza. In iOS, l'attestazione non verrà visualizzata nella sezione dettagli mostrata nell'immagine precedente, mentre in Android verrà visualizzata l'attestazione.  

## <a name="next-steps"></a>Passaggi successivi

A questo punto si ha una migliore comprensione della progettazione delle credenziali verificabili e del modo in cui è possibile crearne una personalizzata per soddisfare le proprie esigenze.

- [Esempi di comunicazione del servizio emittente](issuer-openid.md)
