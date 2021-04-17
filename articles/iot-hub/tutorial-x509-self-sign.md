---
title: 'Esercitazione: Usare OpenSSL per creare certificati autofirmati per hub IoT di Azure | Microsoft Docs'
description: 'Esercitazione: Usare OpenSSL per creare certificati X.509 autofirmati per hub IoT di Azure'
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
ms.openlocfilehash: 982e402946cbd71d946bc1e316cef99621c536a3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378194"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Esercitazione: Uso di OpenSSL per creare certificati autofirmati

È possibile autenticare un dispositivo nell'hub IoT usando due certificati di dispositivo autofirmati. Questa operazione viene talvolta definita autenticazione dell'identificazione personale perché i certificati contengono identificazioni personali (valori hash) inviate all'hub IoT. I passaggi seguenti illustrano come creare due certificati autofirmati.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Passaggio 1: Creare una chiave per il primo certificato

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Passaggio 2: Creare una richiesta di firma del certificato per il primo certificato

Assicurarsi di specificare l'ID dispositivo quando richiesto.

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

## <a name="step-3---check-the-csr"></a>Passaggio 3: Controllare la csr

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>Passaggio 4: Certificato autofirmato 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>Passaggio 5: Creare una chiave per il certificato 2

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

## <a name="step-6---self-sign-certificate-2"></a>Passaggio 6: Certificato autofirmato 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>Passaggio 7 - Recuperare l'identificazione personale per il certificato 1

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>Passaggio 8: Recuperare l'identificazione personale per il certificato 2

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>Passaggio 9: Creare un nuovo dispositivo IoT

Passare all'hub IoT nel portale di Azure e creare una nuova identità del dispositivo IoT con le caratteristiche seguenti:

* Specificare **l'ID** dispositivo corrispondente al nome soggetto dei due certificati.
* Selezionare il **tipo di autenticazione autofirmato X.509.**
* Incollare le identificazioni personali della stringa esadecimale copiate dai certificati primario e secondario del dispositivo. Assicurarsi che le stringhe esadecimali non presentino delimitatori dei due punti.

## <a name="next-steps"></a>Passaggi successivi

Passare a [Test dell'autenticazione del](tutorial-x509-test-certificate.md) certificato per determinare se il certificato può autenticare il dispositivo nell'hub IoT.
