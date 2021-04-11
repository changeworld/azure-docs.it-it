---
title: Collegare il dominio all'identificatore decentralizzato (DID) (anteprima)
description: Informazioni su come eseguire il binding DNS
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: be7db16a8e3a827d08c0db637961bf004af1d621
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170243"
---
# <a name="link-your-domain-to-your-decentralized-identifier-did"></a>Collegare il dominio all'identificatore decentralizzato (DID)

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Contenuto dell'articolo:
> [!div class="checklist"]
> * Perché è necessario collegare il fatto al nostro dominio?
> * Come si collegano DIDs e domini?
> * Come funziona il processo di collegamento del dominio?
> * Come funziona la logica di dominio Verify/unverified?

## <a name="prerequisites"></a>Prerequisiti

Per collegare l'utente al dominio, è necessario avere completato quanto segue.

- Completare il [Introduzione](get-started-verifiable-credentials.md) e il [set di esercitazioni](enable-your-tenant-verifiable-credentials.md)successivo.

## <a name="why-do-we-need-to-link-our-did-to-our-domain"></a>Perché è necessario collegare il fatto al nostro dominio?

Un ha iniziato come un identificatore non ancorato ai sistemi esistenti. Un è utile perché un utente o un'organizzazione può possederlo e controllarlo. Se un'entità che interagisce con l'organizzazione non conosce ' Who ' a cui appartiene, il fatto non è altrettanto utile.

Il collegamento di un oggetto a un dominio risolve il problema di attendibilità iniziale consentendo a qualsiasi entità di verificare crittograficamente la relazione tra un DID e un dominio.

## <a name="how-do-we-link-dids-and-domains"></a>Come si collegano DIDs e domini?

Viene creato un collegamento tra un dominio e un oggetto implementando uno standard aperto scritto da decentralizzato Identity Foundation denominato [configurazione ben nota](https://identity.foundation/.well-known/resources/did-configuration/). Il servizio di credenziali verificabile in Azure Active Directory (Azure AD) consente all'organizzazione di creare il collegamento tra il fatto e il dominio includendo le informazioni sul dominio fornite nel DID e generando il file di configurazione noto:

1. Azure AD usa le informazioni di dominio fornite durante la configurazione dell'organizzazione per scrivere un endpoint di servizio all'interno del documento DID. Tutte le parti che interagiscono con il sono in grado di visualizzare il dominio al quale è stato associato il Proclaiming.  

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

2. Il servizio di credenziali verificabile in Azure AD genera una risorsa di configurazione ben nota conforme che è possibile ospitare nel dominio. Il file di configurazione include una credenziale verificabile autocertificata credentialType ' DomainLinkageCredential ' firmata con il fatto che ha un'origine del dominio. Di seguito è riportato un esempio del documento di configurazione archiviato nell'URL del dominio radice.


    ```json
    {
      "@context": "https://identity.foundation/.well-known/contexts/did-configuration-v0.0.jsonld",
      "linked_dids": [
        "jwt..."
      ]
    }
    ```

Dopo aver creato il file di configurazione noto, è necessario rendere disponibile il file usando il nome di dominio specificato durante l'abilitazione di AAD per le credenziali verificabili.

* Ospitare il file di configurazione ben noto alla radice del dominio.
* Non usare reindirizzamenti.
* Usare HTTPS per distribuire il file di configurazione.

>[!IMPORTANT]
>Microsoft Authenticator non rispetta i reindirizzamenti, l'URL specificato deve essere l'URL di destinazione finale.

## <a name="user-experience"></a>Esperienza dell'utente 

Quando un utente sta attraversando un flusso di rilascio o presentando una credenziale verificabile, deve conoscere qualcosa sull'organizzazione e il suo stato. Se il dominio il raccoglitore di credenziali verificabile, Microsoft Authenticator, convalida una relazione di tipo DID con il dominio nel documento DID e presenta agli utenti due esperienze diverse a seconda del risultato.

## <a name="verified-domain"></a>Dominio verificato

Prima che Microsoft Authenticator visualizzi un'icona **verificata** , è necessario che siano soddisfatte le condizioni seguenti:

* La firma della richiesta SIOP (autocertificate Open ID) deve disporre di un endpoint di servizio per il dominio collegato.
* Il dominio radice non utilizza un reindirizzamento e utilizza HTTPS.
* Il dominio elencato nel documento DID dispone di una risorsa nota risolvibile.
* La credenziale verificabile della risorsa nota è firmata con lo stesso oggetto usato per firmare il SIOP che Microsoft Authenticator usato per avviare il flusso.

Se tutti i indicati in precedenza sono true, Microsoft Authenticator Visualizza una pagina verificata e include il dominio che è stato convalidato.

![nuova richiesta di autorizzazione](media/how-to-dnsbind/new-permission-request.png) 

## <a name="unverified-domain"></a>Dominio non verificato

Se uno dei precedenti non è true, il Microsoft Authenticator visualizzerà un avviso di pagina completa per l'utente che il dominio non è verificato, l'utente si trova al centro di una transazione rischiosa ed è necessario procedere con cautela. Si è scelto di eseguire questa route perché:

* L'oggetto non è ancorato a un dominio.
* La configurazione non è stata configurata correttamente.
* L'oggetto con cui l'utente interagisce è dannoso e non è in grado di dimostrare di essere proprietario di un dominio (poiché in realtà non lo è). A causa di questo ultimo punto, è fondamentale collegare il fatto al dominio con cui l'utente ha familiarità, per evitare la propagazione del messaggio di avviso.

![avviso di dominio non verificato nella schermata Aggiungi credenziale](media/how-to-dnsbind/add-credential-not-verified-authenticated.png)

## <a name="distribute-well-known-config"></a>Distribuire una configurazione ben nota

1. Passare alla pagina impostazioni in credenziali verificabili e scegliere **verifica il dominio**

   ![Verifica il dominio in impostazioni](media/how-to-dnsbind/settings-verify.png) 

2. Scaricare il did-configuration.jsnel file illustrato nell'immagine seguente.

   ![Scaricare la configurazione ben nota](media/how-to-dnsbind/verify-download.png) 

3. Copiare JWT, aprire [JWT.ms](https://www.jwt.ms) e verificare che il dominio sia corretto.

4. Copiare il fatto e aprire [Ion Network Explorer](https://identity.foundation/ion/explorer) per verificare che lo stesso dominio sia incluso nel documento did. 

5. Ospitare la risorsa di configurazione nota nel percorso specificato. Esempio: https://www.example.com/.well-known/did-configuration.json

6. Testare l'emissione o la presentazione con Microsoft Authenticator per la convalida. Verificare che l'impostazione in Authenticator ' avvisa per le app non sicure ' sia attivata o disattivata.

>[!NOTE]
>Per impostazione predefinita,' avvisa per le app non sicure ' è attivato.

A questo punto è stato avviato il Web di trust con il fatto.

## <a name="next-steps"></a>Passaggi successivi

Se durante l'onboarding si immettono le informazioni di dominio errate, è necessario [rifiutare esplicitamente](how-to-opt-out.md)la modifica. Al momento non è supportato l'aggiornamento del documento DID. La disattivazione e la riattivazione creeranno un nuovo marchio.