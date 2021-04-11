---
title: Come revocare una credenziale verificabile come credenziale verificabile Azure Active Directory autorità di certificazione
description: Informazioni su come revocare una credenziale verificabile rilasciata
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 50f270dde59860e7c9dd2ac1b35039d5855859e5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222844"
---
# <a name="revoke-a-previously-issued-verifiable-credential-preview"></a>Revocare una credenziale verificabile precedentemente rilasciata (anteprima)

Come parte del processo di utilizzo delle credenziali verificabili (VCs), non solo è necessario rilasciare le credenziali, ma a volte è necessario revocarle. In questo articolo viene analizzata la proprietà **status** della specifica VC e viene illustrato in dettaglio il processo di revoca, perché potrebbe essere necessario revocare le credenziali e alcune implicazioni relative a dati e privacy.

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="status-property-in-verifiable-credentials-specification"></a>Proprietà Status nella specifica delle credenziali verificabili

Prima di poter comprendere le implicazioni della revoca di una credenziale verificabile, può essere utile conoscere il **controllo dello stato** e il relativo funzionamento.

La [specifica delle credenziali verificabili W3C](https://www.w3.org/TR/vc-data-model/) fa riferimento alla proprietà **status** nella sezione [4,9:](https://www.w3.org/TR/vc-data-model/#status)

"Questa specifica definisce la seguente proprietà **credentialStatus** per l'individuazione delle informazioni sullo stato corrente di una credenziale verificabile, ad esempio se viene sospesa o revocata".

Tuttavia, la specifica W3C non definisce un formato per il modo in cui deve essere implementato il **controllo dello stato** .

"La definizione del modello di dati, dei formati e dei protocolli per gli schemi di stato non rientra nell'ambito di questa specifica. È presente un registro estensioni di credenziali verificabile [VC-EXTENSION-REGISTRY] che contiene gli schemi di stato disponibili per gli implementatori che vogliono implementare il controllo dello stato delle credenziali verificabile ".

>[!NOTE]
>Per il momento, l'implementazione del controllo dello stato di Microsoft è proprietaria, ma stiamo lavorando attivamente con la community di DID per allinearsi a uno standard.

## <a name="how-does-the-status-property-work"></a>Funzionamento della proprietà **status**

In ogni credenziale verificabile emessa da Microsoft esiste un attributo denominato credentialStatus. Viene popolata con un'API di stato gestita da Microsoft per conto dell'utente. Di seguito è riportato un esempio di come appare.

```json
    "credentialStatus": {
      "id": "https://portableidentitycards.azure-api.net/v1.0/7952032d-d1f3-4c65-993f-1112dab7e191/portableIdentities/card/status",
      "type": "PortableIdentityCardServiceCredentialStatus2020"
    }
```

L'SDK di credenziali verificabili Open Source gestisce la chiamata dell'API di stato e fornisce i dati necessari.

Dopo che l'API è stata chiamata e fornito le informazioni corrette, l'API restituirà true o false. True se la credenziale verificabile è ancora attiva con l'emittente e false significa che la credenziale verificabile è stata revocata attivamente dall'emittente.

## <a name="why-you-may-want-to-revoke-a-vc"></a>Perché potrebbe essere necessario revocare un VC?

Ogni cliente avrà una ragione univoca per la revoca di una credenziale verificabile, ma di seguito sono riportati alcuni dei temi comuni finora riconoscibili. 

- ID studente: lo studente non è più uno studente attivo presso l'Università.
- ID dipendente: il dipendente non è più un dipendente attivo.
- Licenza per i driver di stato: il driver non risiede più in tale stato.

## <a name="how-to-set-up-a-verifiable-credential-with-the-ability-to-revoke"></a>Come configurare una credenziale verificabile con la possibilità di revocare

Per impostazione predefinita, tutti i dati delle credenziali verificabili non vengono archiviati con Microsoft. Non sono pertanto disponibili dati a cui fare riferimento per revocare un ID credenziale verificabile specifico. L'emittente deve specificare un campo specifico dall'attributo di credenziale verificabile per Microsoft da indicizzare e successivamente Salt e hash.

>[!NOTE]
>L'hashing è un'operazione di crittografia unidirezionale che converte un input, denominato ```preimage``` e produce un output denominato hash con lunghezza fissa. Al momento non è possibile eseguire il calcolo per invertire un'operazione hash.

È possibile indicare a Microsoft quale attributo della credenziale verificabile si desidera indicizzare. L'implicazione dell'indicizzazione è che i valori indicizzati possono essere usati per cercare le credenziali verificabili per il VCs che si vuole revocare.

**Esempio:** Alice è un dipendente Woodgrove. Alice ha lasciato la Woodgrove per lavorare a contoso. Jane, l'amministratore IT di Woodgrove, Cerca la posta elettronica di Alice nella query di ricerca di revoca delle credenziali verificabili. In questo esempio Jane ha indicizzato il campo email della credenziale Employee verificata della Woodgrove. 

Per un esempio di come viene modificato il file delle regole per includere l'indice, vedere di seguito.

```json
{
  "attestations": {
    "idTokens": [
      { 
        "mapping": {
          "Name": { "claim": "name" },
          "email": { "claim": "email", "indexed": true}
        },
        "configuration": "https://login.microsoftonline.com/tenant-id-here7/v2.0/.well-known/openid-configuration",
        "client_id": "c0d6b785-7a08-494e-8f63-c30744c3be2f",
        "redirect_uri": "vcclient://openid"
      }
    ]
  },
  "validityInterval": 25920000,
  "vc": {
    "type": ["WoodgroveEmployee"]
  }
}
```

>[!NOTE]
>È possibile indicizzare un solo attributo da un file di regole.  

## <a name="how-do-i-revoke-a-verifiable-credential"></a>Ricerca per categorie revocare una credenziale verificabile

Una volta che è stata impostata un'attestazione dell'indice e sono state rilasciate le credenziali verificabili agli utenti, è possibile vedere come revocare una credenziale verificabile nel pannello VC.

1. Passare al pannello credenziali verificabili in Azure Active Directory.
1. Scegliere le credenziali verificabili in cui è stata precedentemente configurata l'attestazione dell'indice ed è stata emessa una credenziale verificabile per un utente. =
1. Nel menu a sinistra scegliere **revoca una credenziale** 
    ![ revocare le credenziali](media/how-to-issuer-revoke/settings-revoke.png) 
1. Cercare l'attributo index dell'utente che si desidera revocare. 

   ![Trovare le credenziali da revocare](media/how-to-issuer-revoke/revoke-search.png)

    >[!NOTE]
    >Poiché viene memorizzato solo un hash dell'attestazione indicizzata dalla credenziale verificabile, solo una corrispondenza esatta compilerà i risultati della ricerca. L'input viene cercato dall'amministratore IT e viene usato lo stesso algoritmo di hashing per verificare se è presente una corrispondenza hash nel database.
    
1. Una volta trovata una corrispondenza, selezionare l'opzione **Revoke** a destra della credenziale che si desidera revocare.

   ![Avviso che informa che dopo la revoca l'utente ha ancora le credenziali](media/how-to-issuer-revoke/warning.png) 

1. Una volta completata la revoca, viene visualizzato l'aggiornamento dello stato e viene visualizzato un banner verde nella parte superiore della pagina. 
   ![Verifica il dominio in impostazioni](media/how-to-issuer-revoke/revoke-successful.png) 

A questo punto, ogni volta che un relying party chiama per verificare lo stato di questa specifica credenziale verificabile, l'API di stato Microsoft, che agisce per conto del tenant, restituisce una risposta "false".

## <a name="next-steps"></a>Passaggi successivi

Testare la funzionalità autonomamente con una credenziale di test per usare il flusso. È possibile visualizzare informazioni su come configurare il tenant per emettere credenziali verificabili esaminando [le esercitazioni](get-started-verifiable-credentials.md).