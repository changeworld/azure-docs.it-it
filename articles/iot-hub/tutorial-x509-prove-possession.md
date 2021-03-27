---
title: "Esercitazione: dimostrare la proprietà dei certificati della CA nell'hub Azure Microsoft Docs"
description: "Esercitazione: dimostrare di essere proprietari di un certificato della CA per l'hub Azure."
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 0eb91754c3c70a7b477d456158454f707a874207
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630718"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Esercitazione: dimostrazione del possesso di un certificato della CA

Dopo aver caricato il certificato dell'autorità di certificazione (CA) radice o il certificato della CA subordinata nell'hub Internet, è necessario dimostrare di essere il proprietario del certificato:

1. Nella portale di Azure passare a IoTHub e selezionare **impostazioni > certificati**.

2. Selezionare **Aggiungi** per aggiungere un nuovo certificato CA.

3. Immettere un nome visualizzato nel campo **nome certificato** e selezionare il certificato PEM da aggiungere.

4. Selezionare **Salva**. Il certificato viene visualizzato nell'elenco dei certificati con lo stato non **verificato**. Questo processo di verifica proverà a avere il possesso del certificato.

5. Selezionare il certificato per visualizzare la finestra di dialogo **Dettagli certificato** .

6. Selezionare **genera codice di verifica** nella finestra di dialogo.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Finestra di dialogo Dettagli certificato}":::

7. Copiare il codice di verifica negli Appunti. È necessario impostare il codice di verifica come soggetto del certificato. Se, ad esempio, il codice di verifica è 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3, aggiungerlo come oggetto del certificato, come illustrato nel passaggio successivo.

8. Esistono tre modi per generare un certificato di verifica:

    * Se si usa lo script di PowerShell fornito da Microsoft, eseguire `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` per creare un certificato denominato `VerifyCert4.cer` . Per ulteriori informazioni, vedere [utilizzo di script forniti da Microsoft](tutorial-x509-scripts.md).

    * Se si usa lo script bash fornito da Microsoft, eseguire `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` per creare un certificato denominato `verification-code.cert.pem` . Per ulteriori informazioni, vedere [utilizzo di script forniti da Microsoft](tutorial-x509-scripts.md).

    * Se si usa OpenSSL per generare i certificati, è necessario innanzitutto generare una chiave privata e una richiesta di firma del certificato (CSR):

      ```bash
      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      Quindi, creare un certificato usando il file di configurazione dell'autorità di certificazione radice (mostrato di seguito) o il file di configurazione della CA subordinata e la CSR.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Per altre informazioni, vedere [uso di OpenSSL per creare certificati di test](tutorial-x509-openssl.md).

10. Selezionare il nuovo certificato nella visualizzazione dei **Dettagli del certificato** .

11. Dopo il caricamento del certificato, selezionare **Verifica**. Lo stato del certificato della CA deve essere modificato in **verificato**.
