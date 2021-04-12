---
title: "Esercitazione: Introduzione a Azure Active Directory credenziali verificabili tramite un'app di esempio (anteprima)"
description: In questa esercitazione si apprenderà come emettere credenziali verificabili usando l'app di esempio e il tenant di test
ms.service: identity
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 04/01/2021
ms.openlocfilehash: deebaf31197d8b7335f887ae447f05add45278b2
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222886"
---
#  <a name="tutorial---get-started-with-azure-active-directory-verifiable-credentials-using-a-sample-app-preview"></a>Esercitazione: Introduzione a Azure Active Directory credenziali verificabili tramite un'app di esempio (anteprima)

In questa esercitazione verranno esaminati i passaggi necessari per emettere la prima credenziale verificabile, ovvero una scheda Expert Credential verificata. È quindi possibile usare questa scheda per dimostrare a un verificatore che si è esperti di credenziali verificate, padroneggiato nell'arte delle credenziali digitali. Introduzione a Azure Active Directory credenziali verificabili usando l'app di esempio delle credenziali verificabili per emettere le prime credenziali verificabili.

![Si tratta di un'immagine di una scheda di esempio](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

- [NodeJS](https://nodejs.org/en/download/) versione 10,14 o successiva installata nel sistema di test.
- È necessario che [git](https://git-scm.com/downloads) sia installato se si vuole clonare il repository che ospita l'app di esempio,
- [Visual Studio Code](https://code.visualstudio.com/Download)
- Un sistema per ospitare il sito di esempio.
- Un dispositivo mobile con Microsoft Authenticator versione 6.2005.3599 o successiva installata.
- [NGROK](https://ngrok.com/) gratuito.

## <a name="download-the-sample-code"></a>Scaricare il codice di esempio

Per rilasciare una scheda Expert Credential verificata, è necessario eseguire un sito Web nel computer locale. Il sito Web viene usato per avviare un processo di rilascio delle credenziali verificabile. È stato fornito un semplice sito Web, scritto in NodeJS, che verrà usato in questa esercitazione.

Prima di tutto, scaricare il codice di esempio [da GitHub oppure](https://github.com/Azure-Samples/active-directory-verifiable-credentials)clonare il repository nel computer locale:

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

Si consiglia di acquisire familiarità con il codice presente nei siti Web di esempio. La `issuer` cartella contiene tutto il codice usato per emettere una credenziale verificabile. Ulteriori informazioni sono disponibili nel [file Leggimi](https://github.com/Azure-Samples/active-directory-verifiable-credentials)dell'esempio.

## <a name="run-the-issuer-website"></a>Eseguire il sito Web dell'autorità emittente

È possibile eseguire i passaggi dall'interno Visual Studio Code o da qualsiasi riga di comando disponibile nel sistema operativo. 

1. Passa alla cartella `issuer`. 

    ```terminal
    cd issuer
    ```

2. Quando è necessario installare tutti i pacchetti necessari e avviare il sito.

   ```terminal
    npm install
    node app.js
    ```

3. Nel terminale si noterà ora che l'app emittente è in ascolto sulla porta 8081. A questo punto è possibile configurare un proxy inverso con Ngrok, in modo che Authenticator possa comunicare con l'app. 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Creazione di un proxy inverso con Ngrok

Quando si esegue il sito Web di esempio, il dispositivo deve comunicare con il server node in esecuzione nel computer locale. Si consiglia di usare [ngrok](https://ngrok.com/) come metodo semplice per rendere disponibile il server di sviluppo locale tramite Internet.

1.  Dopo aver scaricato ed estratto **ngrok**, è necessario eseguire:

    ```terminal
     ngrok http 8081
    ```

Per impostazione predefinita, il sito Web di esempio viene eseguito sulla porta `8081` . **Ngrok** restituisce due URL di invio per il server. Copiare l'URL con il `https://` prefisso.

![ngrok consente di rendere disponibili i punti finali dell'applicazione tramite Internet](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> Se si usa PowerShell, potrebbe essere necessario digitare `./ngrok` per il comando da riconoscere.

Ora che la porta locale è esposta a Internet usando ngrok, il sito di esempio usa automaticamente il nome host generato da ngrok. Aprire il browser e passare all'URL di invio HTTPS ngrok. Dovrebbe essere possibile visitare correttamente la Home page del sito di esempio. Se la pagina si apre, il dispositivo può comunicare con l'app di esempio in esecuzione nel server locale. A questo punto si è pronti per emettere una scheda Expert Credential verificata.

## <a name="issue-a-credential"></a>Rilascia credenziali

1. Installare Authenticator nel dispositivo mobile. Microsoft Authenticator viene utilizzato per ricevere, archiviare e presentare le credenziali verificabili alle parti interessate.

2. Successivamente, emettere una credenziale verificabile. **Fare clic** sul pulsante **Get Credential** . Quando si fa clic sul pulsante **Get Credential** , nel sito Web di esempio viene visualizzato un codice a matrice, che può essere analizzato tramite l'autenticatore. Se si Visualizza il sito dal browser sul dispositivo mobile, facendo clic sul pulsante **Get Credential** viene attivato un collegamento Deep che apre l'app Authenticator e non richiede l'analisi di un codice a matrice.

   ![Pulsante Get Credential](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Eseguire la scansione del codice a matrice del sito Web usando Authenticator oppure, se si accede al sito Web tramite un dispositivo mobile, fare clic sul pulsante Get Credential per attivare il collegamento Deep. 

   ![Codice QR ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. Si noti che il pulsante **Aggiungi** è disattivato in questo momento. Scegliere **accedi al proprio account** sotto l'immagine della scheda.

   ![Accedi ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. Prima di ottenere la scheda Expert Credential, per il tenant usato è necessario fornire le informazioni di autenticazione. Se è la prima volta che si passa all'esercitazione, non si dispone di un account esperto di credenziali, creare un nuovo account utente nel tenant di B2C.

   ![eseguire l'autenticazione prima di procedere](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. Dopo aver eseguito l'accesso, il pulsante **Aggiungi** non viene più disattivato. Scegliere **Aggiungi** per accettare il nuovo VC.

   ![Scegliere Aggiungi dopo l'autenticazione](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. Congratulazioni! A questo punto si dispone di un VC esperto di credenziali verificato.

   ![Aggiunta del VC esperto credenziali](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
A questo punto è possibile verificare le credenziali.

## <a name="validate-credentials"></a>Convalida credenziali

Ora che è stata completata la parte relativa al rilascio dell'esercitazione e si dispone di una credenziale verificabile in Authenticator, è possibile convalidarla nella propria app Verifier.

1.  Arrestare l'esecuzione del servizio ngrok dell'autorità emittente.

    ```terminal
     control+c
    ```


2. In un'altra finestra del terminale aprire la cartella dell'app Verifier ed eseguirla in modo analogo al modo in cui è stata eseguita l'app emittente.

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. Eseguire ora ngrok con la porta Verifier 8082.

    ```terminal
    ngrok http 8082
    ```

4. Aprire l'URL di invio HTTPS di ngrok nel browser e toccare il pulsante **Verify Credential** .  

   ![pulsante Verifica esperto credenziali](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Aprire Authenticator ed eseguire la scansione del codice a matrice.

   ![eseguire la scansione del codice QR per verificare le credenziali](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > In iOS è la parte superiore destra e Android è il lato destro. Eseguire la scansione del codice QR.

6. Scegliere **Consenti** nella schermata nuova richiesta di autorizzazione in Authenticator. Se si preme Consenti, si sta firmando una presentazione verificabile con l'identificatore DID (decentralizzato) per dimostrare che è necessario controllare questa credenziale verificabile.

   ![nuova richiesta di autorizzazione](media/get-started-verifiable-credentials/new-permission-request.png)

    Dopo una presentazione corretta, è necessario aggiornare tre elementi:

   1. La pagina Web dovrebbe ora visualizzare "Congratulazioni, il nome" + è un esperto di credenziali verificato! ".
   
    ![Congratulazioni, verifica di nuovo](media/get-started-verifiable-credentials/congratulations.png)


   2. Il terminale dell'app Verifier dovrebbe anche visualizzare lo stesso messaggio dai log.
   
    ![attività dell'applicazione nel prompt dei comandi](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. In Authenticator dovrebbe essere presente una voce per l'attività recente di questa presentazione.

    ![Attività in Authenticator](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> Durante l'esecuzione dell'app Verifier, ngrok potrebbe smettere di funzionare e visualizzare un errore relativo a un numero eccessivo di connessioni. Questo problema può essere evitato registrando l'account con ngrok. 

## <a name="next-steps"></a>Passaggi successivi

Una volta completata la Guida introduttiva, è possibile creare il proprio identificatore decentralizzato nel Azure AD servizio di credenziali verificabile ed emettere credenziali verificabili.

>[!div class="nextstepaction"] 
>[Configurare un'autorità emittente personalizzata usando l'app di esempio credenziali verificabili](./enable-your-tenant-verifiable-credentials.md)
