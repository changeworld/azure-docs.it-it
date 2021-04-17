---
title: Collegare il dominio all'identificatore decentralizzato (anteprima) - Azure Active Directory credenziali verificabili
description: Informazioni su come eseguire il binding DNS?
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: ad5bb6e45479b4cccfa0b002427066439135e468
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588446"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>Collegare il dominio all'identificatore decentralizzato (DID)

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Contenuto dell'articolo:
> [!div class="checklist"]
> * Perché è necessario collegare did al dominio?
> * Come si collegano DID e domini?
> * Come funziona il processo di collegamento del dominio?
> * Come funziona la logica di verifica/non verifica del dominio?

## <a name="prerequisites"></a>Prerequisiti

Per collegare did al dominio, è necessario aver completato quanto segue.

- Completare il [Attività iniziali](get-started-verifiable-credentials.md) e l'esercitazione [successiva impostare](enable-your-tenant-verifiable-credentials.md).

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Perché è necessario collegare did al dominio?

Un DID inizia come identificatore non ancorato ai sistemi esistenti. Un DID è utile perché un utente o un'organizzazione può essere proprietario e controllarlo. Se un'entità che interagisce con l'organizzazione non conosce "a chi" appartiene did, did non è così utile.

Il collegamento di un DID a un dominio risolve il problema di trust iniziale consentendo a qualsiasi entità di verificare crittograficamente la relazione tra un DID e un dominio.

## <a name="how-do-we-link-dids-and-domains"></a>Come si collegano DID e domini?

Viene fatto un collegamento tra un dominio e un DID implementando uno standard aperto scritto da Decentralized Identity Foundation denominato [Configurazione well-known DID.](https://identity.foundation/.well-known/resources/did-configuration/) Il servizio di credenziali verificabili in Azure Active Directory (Azure AD) consente all'organizzazione di creare il collegamento tra il did e il dominio includendo le informazioni di dominio fornite nell'DID e generando il file di configurazione noto:

1. Azure AD usa le informazioni sul dominio fornite durante la configurazione dell'organizzazione per scrivere un endpoint di servizio all'interno del documento DID. Tutte le parti che interagiscono con l'DID possono visualizzare il dominio a cui associare il did did.  

    ```json
        "service": [
          {
            "id": "#linkeddomains",
            "type": "LinkedDomains",
            "serviceEndpoint": {
              "origins": [
                "https://www.contoso.com/"
              ]
            }
          }
    ```

2. Il servizio credenziali verificabile in Azure AD genera una risorsa di configurazione nota conforme che è possibile ospitare nel dominio. Il file di configurazione include una credenziale verificabile auto-rilasciata di credentialType 'DomainLinkageCredential' firmata con did che ha un'origine del dominio. Di seguito è riportato un esempio del documento di configurazione archiviato nell'URL del dominio radice.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

Dopo aver creato il file di configurazione noto, è necessario rendere disponibile il file usando il nome di dominio specificato quando si abilita AAD per le credenziali verificabili.

* Ospitare il file di configurazione DID noto nella radice del dominio.
* Non usare i reindirizzamenti.
* Usare https per distribuire il file di configurazione.

>[!IMPORTANT]
>Microsoft Authenticator non rispetta i reindirizzamenti, l'URL specificato deve essere l'URL di destinazione finale.

## <a name="user-experience"></a>Esperienza utente 

Quando un utente sta passando attraverso un flusso di rilascio o presenta una credenziale verificabile, deve conoscere qualcosa sull'organizzazione e il relativo DID. Se il dominio del portafoglio di credenziali verificabile, Microsoft Authenticator, convalida la relazione di un DID con il dominio nel documento DID e presenta agli utenti due esperienze diverse a seconda del risultato.

## <a name="verified-domain"></a>Dominio verificato

Prima Microsoft Authenticator **un'icona Verificata,** è necessario che siano vere alcune cose:

* La firma DID della richiesta SIOP (Self-Issued Open ID) deve avere un endpoint di servizio per il dominio collegato.
* Il dominio radice non usa un reindirizzamento e usa https.
* Il dominio elencato nel documento DID ha una risorsa nota risolvibile.
* La credenziale verificabile della risorsa nota viene firmata con lo stesso DID usato per firmare il SIOP Microsoft Authenticator usato per avviare il flusso.

Se tutte le informazioni indicate in precedenza sono vere, Microsoft Authenticator una pagina verificata e include il dominio convalidato.

![nuova richiesta di autorizzazione](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Dominio nonverificato

Se una delle condizioni precedenti non è vera, il Microsoft Authenticator visualizza un avviso a pagina intera per l'utente che il dominio non è stato verificato, l'utente è nel mezzo di una transazione rischiosa e deve procedere con cautela. È stato scelto di eseguire questa route perché:

* DID non è ancorato a un dominio.
* La configurazione non è stata configurata correttamente.
* L'did con cui l'utente interagisce è dannoso e in realtà non può dimostrare di essere proprietario di un dominio (perché in realtà non lo è). A causa di quest'ultimo punto, è fondamentale collegare did al dominio con cui l'utente ha familiarità, per evitare di propagare il messaggio di avviso.

![Avviso di dominio non verificato nella schermata aggiungi credenziali](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Distribuire la configurazione nota

1. Passare alla pagina Impostazioni in Credenziali verificabili e scegliere **Verifica questo dominio**

   ![Verificare questo dominio nelle impostazioni](media/how-to-dnsbind/settings-verify.png) 

2. Scaricare il did-configuration.jsfile illustrato nell'immagine seguente.

   ![Scaricare la configurazione nota](media/how-to-dnsbind/verify-download.png) 

3. Copiare il token JWT, [aprire jwt.ms](https://www.jwt.ms) e verificare che il dominio sia corretto.

4. Copiare il did e aprire il Esplora rete [ION](https://identity.foundation/ion/explorer) per verificare che lo stesso dominio sia incluso nel documento DID. 

5. Ospitare la risorsa di configurazione nota nel percorso specificato. Esempio: `https://www.example.com/.well-known/did-configuration.json`

6. Testare l'emissione o la presentazione con Microsoft Authenticator da convalidare. Assicurarsi che l'impostazione in Authenticator "Avvisa in caso di app non sicure" sia attivata.

>[!NOTE]
>Per impostazione predefinita, l'opzione "Avvisa in caso di app non sicure" è attivata.

A questo punto è stato avviato il bootstrap del Web di attendibilità con DID.

## <a name="next-steps"></a>Passaggi successivi

Se durante l'onboarding si immettono le informazioni di dominio erre di si decide di modificarle, sarà necessario [rifiutare esplicitamente](how-to-opt-out.md). Al momento non è possibile aggiornare il documento DID. Rifiutare esplicitamente e rifiutare esplicitamente l'accesso creerà un nuovo DID.
