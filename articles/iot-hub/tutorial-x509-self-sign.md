---
title: "Esercitazione: usare OpenSSL per creare certificati autofirmati per l'hub Azure. Microsoft Docs"
description: "Esercitazione: usare OpenSSL per creare certificati X. 509 autofirmati per l'hub Azure."
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
ms.openlocfilehash: 82ef2e39d5d04914e1086e0b25ccbc8e5c7c762e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630710"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Esercitazione: uso di OpenSSL per la creazione di certificati autofirmati

È possibile autenticare un dispositivo nell'hub Internet con due certificati per dispositivi autofirmati. Questa operazione viene talvolta denominata autenticazione con identificazione personale perché i certificati contengono identificazioni personali (valori hash) inviati all'hub Internet. La procedura seguente illustra come creare due certificati autofirmati.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Passaggio 1: creare una chiave per il primo certificato

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Passaggio 2: creare un CSR per il primo certificato

Assicurarsi di specificare l'ID del dispositivo quando richiesto.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>Passaggio 3: controllare la CSR

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>Passaggio 4: certificato autofirmato 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>Passaggio 5: creare una chiave per il certificato 2

Quando richiesto, specificare lo stesso ID dispositivo usato per il certificato 1.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-6---self-sign-certificate-2"></a>Passaggio 6: certificato autofirmato 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>Passaggio 7: recuperare l'identificazione personale per il certificato 1

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>Passaggio 8: recuperare l'identificazione personale per il certificato 2

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>Passaggio 9: creare un nuovo dispositivo Internet

Passare all'hub Internet delle cose nel portale di Azure e creare una nuova identità del dispositivo Internet con le caratteristiche seguenti:

* Specificare l' **ID del dispositivo** che corrisponde al nome del soggetto dei due certificati.
* Selezionare il tipo di autenticazione **autofirmato X. 509** .
* Incollare le identificazioni personali della stringa esadecimale copiate dai certificati primari e secondari del dispositivo. Verificare che le stringhe esadecimali non includano delimitatori di due punti.

## <a name="next-steps"></a>Passaggi successivi

Passare a [test autenticazione certificati](tutorial-x509-test-certificate.md) per determinare se il certificato è in grado di autenticare il dispositivo nell'hub Internet.
