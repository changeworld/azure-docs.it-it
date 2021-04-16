---
title: 'Esercitazione: Dimostrare la proprietà dei certificati ca in hub IoT di Azure | Microsoft Docs'
description: 'Esercitazione: Dimostrare di essere proprietari di un certificato della CA per hub IoT di Azure'
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
ms.openlocfilehash: b7740fa1f6a54dcfcc1181dddedcdd5fdb50402c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378228"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Esercitazione: Dimostrare il possesso di un certificato ca

Dopo aver caricato il certificato dell'autorità di certificazione radice (CA) o il certificato della CA subordinata nell'hub IoT, è necessario dimostrare di essere proprietari del certificato:

1. Nel portale di Azure passare a IoTHub e selezionare Impostazioni > **certificati**.

2. Selezionare **Aggiungi** per aggiungere un nuovo certificato CA.

3. Immettere un nome visualizzato nel **campo Nome** certificato e selezionare il certificato PEM da aggiungere.

4. Selezionare **Salva**. Il certificato viene visualizzato nell'elenco dei certificati con stato **Unverified**. Questo processo di verifica dimostra di essere in possesso del certificato.

5. Selezionare il certificato per visualizzare la **finestra di dialogo Dettagli** certificato.

6. Selezionare **Genera codice di verifica** nella finestra di dialogo.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Finestra di dialogo Dettagli certificato}":::

7. Copiare il codice di verifica negli Appunti. È necessario impostare il codice di verifica come soggetto del certificato. Ad esempio, se il codice di verifica è 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3, aggiungerlo come oggetto del certificato, come illustrato nel passaggio successivo.

8. Esistono tre modi per generare un certificato di verifica:

    * Se si usa lo script di PowerShell fornito da Microsoft, eseguire `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` per creare un certificato denominato `VerifyCert4.cer` . Per altre informazioni, vedere [Uso di script forniti da Microsoft.](tutorial-x509-scripts.md)

    * Se si usa lo script Bash fornito da Microsoft, eseguire `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` per creare un certificato denominato `verification-code.cert.pem` . Per altre informazioni, vedere [Uso di script forniti da Microsoft.](tutorial-x509-scripts.md)

    * Se si usa OpenSSL per generare i certificati, è necessario prima generare una chiave privata e quindi una richiesta di firma del certificato (CSR):

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

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

      Creare quindi un certificato usando il file di configurazione della CA radice (illustrato di seguito) o il file di configurazione della CA subordinata e la richiesta di firma del certificato.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Per altre informazioni, vedere [Uso di OpenSSL per creare certificati di test.](tutorial-x509-openssl.md)

10. Selezionare il nuovo certificato nella **visualizzazione Dettagli** certificato.

11. Dopo il caricamento del certificato, selezionare **Verifica**. Lo stato del certificato della CA deve essere modificato in **Verificato.**
