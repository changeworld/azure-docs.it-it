---
title: Esportare la catena di certificati CA client attendibili per l'autenticazione client
titleSuffix: Azure Application Gateway
description: Informazioni su come esportare una catena di certificati CA client attendibili per l'autenticazione client nel gateway applicazione Azure
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2021
ms.author: caya
ms.openlocfilehash: 2329dc7426b223ef2c81dd0e2e607bccf73192e6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231557"
---
# <a name="export-a-trusted-client-ca-certificate-chain-to-use-with-client-authentication"></a>Esportare una catena di certificati CA client attendibile da usare con l'autenticazione client
Per configurare l'autenticazione reciproca con il client o l'autenticazione client, il gateway applicazione richiede il caricamento di una catena di certificati CA client attendibile nel gateway. Se sono presenti più catene di certificati, è necessario creare le catene separatamente e caricarle come file diversi nel gateway applicazione. In questo articolo si apprenderà come esportare una catena di certificati CA client attendibile che è possibile usare nella configurazione dell'autenticazione client nel gateway.  

## <a name="prerequisites"></a>Prerequisiti

Per generare la catena di certificati CA client attendibili è necessario un certificato client esistente. 

## <a name="export-trusted-client-ca-certificate"></a>Esporta certificato CA client attendibile

Il certificato della CA client attendibile è necessario per consentire l'autenticazione client nel gateway applicazione. In questo esempio viene usato un certificato TLS/SSL per il certificato client, viene esportata la chiave pubblica e quindi vengono esportati i certificati della CA dalla chiave pubblica per ottenere i certificati della CA client attendibile. Verranno quindi concatenati tutti i certificati della CA client in una catena di certificati CA client attendibile. 

La procedura seguente consente di esportare il file con estensione PEM o CER per il certificato:

### <a name="export-public-certificate"></a>Esporta certificato pubblico 

1. Per ottenere un file con estensione cer dal certificato, aprire **Gestire i certificati utente**. Individuare il certificato, in genere in ' Certificates-Current Corrente\personale\certificati ' e fare clic con il pulsante destro del mouse. Fare clic su **Tutte le attività** e quindi su **Esporta**. Si avvia la procedura di **Esportazione guidata certificati**. Se non è possibile trovare il certificato in Utente corrente\Personale\Certificati, è possibile aver accidentalmente aperto Certificati - Computer locale" invece di "Certificati - Utente corrente". Se si vuole aprire Gestione certificati nell'ambito dell'utente corrente usando PowerShell, digitare *certmgr* nella finestra della console.

    > [!div class="mx-imgBorder"]
    > ![Screenshot mostra il gestore di certificati con certificati selezionati e un menu contestuale con tutte le attività, quindi Esporta selezionato.](./media/certificates-for-backend-authentication/export.png)

2. Nella procedura guidata, fare clic su **Avanti**.
    > [!div class="mx-imgBorder"]
    > ![Esportare il certificato](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selezionare **No, non esportare la chiave privata** e quindi fare clic su **Avanti**.
    > [!div class="mx-imgBorder"]
    > ![Non esportare la chiave privata](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Nella pagina **Formato file di esportazione** selezionare **Codificato Base 64 X.509 (.CER)** e quindi fare clic su **Avanti**.
    > [!div class="mx-imgBorder"]
    > ![Codifica Base 64](./media/certificates-for-backend-authentication/base64.png)

5. In **File da esportare** fare clic su **Sfoglia** e passare alla posizione in cui si vuole esportare il certificato. Per **Nome file**, assegnare un nome al file del certificato. Quindi fare clic su **Next**.

    > [!div class="mx-imgBorder"]
   > ![Screenshot che mostra l'esportazione guidata certificati in cui si specifica un file da esportare.](./media/certificates-for-backend-authentication/browse.png)

6. Fare clic su **Fine** per esportare il certificato.

    > [!div class="mx-imgBorder"]
    > ![Screenshot mostra l'esportazione guidata certificati dopo aver completato l'esportazione del file.](./media/certificates-for-backend-authentication/finish.png)

7. Il certificato è stato esportato correttamente.

    > [!div class="mx-imgBorder"]
    > ![Screenshot che mostra l'esportazione guidata certificati con un messaggio di operazione completata.](./media/certificates-for-backend-authentication/success.png)

   Il certificato esportato è simile al seguente:

    > [!div class="mx-imgBorder"]
    > ![Screenshot mostra un simbolo del certificato.](./media/certificates-for-backend-authentication/exported.png)

### <a name="export-ca-certificates-from-the-public-certificate"></a>Esporta i certificati CA dal certificato pubblico

Ora che è stato esportato il certificato pubblico, i certificati CA verranno esportati dal certificato pubblico. Se si dispone solo di una CA radice, sarà necessario solo esportare il certificato. Tuttavia, se si dispone di una CA intermedia, sarà necessario esportare anche ognuno di essi. 

1. Una volta esportata la chiave pubblica, aprire il file.

    > [!div class="mx-imgBorder"]
    > ![Apri certificato di autorizzazione](./media/certificates-for-backend-authentication/openAuthcert.png)

    > [!div class="mx-imgBorder"]
    > ![informazioni sul certificato](./media/mutual-authentication-certificate-management/general.png)

1. Selezionare la scheda percorso certificazione per visualizzare l'autorità di certificazione.

    > [!div class="mx-imgBorder"]
    > ![Dettagli certificato](./media/mutual-authentication-certificate-management/cert-details.png) 

1. Selezionare il certificato radice e fare clic su **Visualizza certificato**.

    > [!div class="mx-imgBorder"]
    > ![percorso certificato](./media/mutual-authentication-certificate-management/root-cert.png) 

   Verranno visualizzati i dettagli del certificato radice.

    > [!div class="mx-imgBorder"]
    > ![informazioni sul certificato](./media/mutual-authentication-certificate-management/root-cert-details.png)

1. Selezionare la scheda **Dettagli** e fare clic su **copia su file...**

    > [!div class="mx-imgBorder"]
    > ![copia certificato radice](./media/mutual-authentication-certificate-management/root-cert-copy-to-file.png)

1. A questo punto, sono stati estratti i dettagli del certificato CA radice dal certificato pubblico. Verrà visualizzata l' **esportazione guidata certificati**. Per completare l'esportazione guidata certificati, seguire i passaggi 2-7 della sezione precedente ([Esporta certificato pubblico](./mutual-authentication-certificate-management.md#export-public-certificate)). 

1. Ripetere ora i passaggi 2-6 da questa sezione corrente ([Esporta i certificati CA dal certificato pubblico](./mutual-authentication-certificate-management.md#export-ca-certificates-from-the-public-certificate)) per tutte le CA intermedie per esportare tutti i certificati della CA intermedi in X. 509 con codifica base 64 (. Formato CER).

    > [!div class="mx-imgBorder"]
    > ![certificato intermedio](./media/mutual-authentication-certificate-management/intermediate-cert.png)

    Si consiglia, ad esempio, di ripetere i passaggi 2-6 da questa sezione sulla CA intermedia *MSIT CAZ2* per estrarla come certificato. 

### <a name="concatenate-all-your-ca-certificates-into-one-file"></a>Concatenare tutti i certificati della CA in un unico file

1. Eseguire il comando seguente con tutti i certificati della CA estratti in precedenza. 

    Windows:
    ```console
    type intermediateCA.cer rootCA.cer > combined.cer
    ```
    
    Linux:
    ```console
    cat intermediateCA.cer rootCA.cer >> combined.cer
    ```

    Il certificato combinato risultante avrà un aspetto simile al seguente:
    
    > [!div class="mx-imgBorder"]
    > ![certificato combinato](./media/mutual-authentication-certificate-management/combined-cert.png)

## <a name="next-steps"></a>Passaggi successivi

A questo punto si dispone della catena di certificati CA client attendibile. È possibile aggiungerlo alla configurazione di autenticazione client nel gateway applicazione per consentire l'autenticazione reciproca con il gateway. Vedere [configurare l'autenticazione reciproca usando il gateway applicazione con il portale](./mutual-authentication-portal.md) o [configurare l'autenticazione reciproca usando il gateway applicazione con PowerShell](./mutual-authentication-powershell.md).

