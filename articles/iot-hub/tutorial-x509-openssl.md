---
title: 'Esercitazione: Usare OpenSSL per creare certificati di test X.509 per hub IoT di Azure| Microsoft Docs'
description: 'Esercitazione: Usare OpenSSL per creare certificati ca e dispositivi per Azure IoT hub'
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
ms.openlocfilehash: 0843e5d3a5e91cb4acdf18ad6bdf6f4f0c214f72
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378296"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Esercitazione: Uso di OpenSSL per creare certificati di test

Sebbene sia possibile acquistare certificati X.509 da un'autorità di certificazione attendibile, la creazione di una gerarchia di certificati di test o l'uso di certificati autofirmati è adeguata per testare l'autenticazione del dispositivo dell'hub IoT. Nell'esempio seguente [vengono utilizzati OpenSSL](https://www.openssl.org/) e [openSSL Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) per creare un'autorità di certificazione (CA), una CA subordinata e un certificato del dispositivo. L'esempio firma quindi la CA subordinata e il certificato del dispositivo in una gerarchia di certificati. Questa operazione viene presentata solo a scopo di esempio.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Passaggio 1: Creare la struttura di directory della CA radice

Creare una struttura di directory per l'autorità di certificazione.

* La directory **certs** archivia i nuovi certificati.
* La **directory db** viene usata per il database dei certificati.
* La **directory privata** archivia la chiave privata della CA.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Passaggio 2: Creare un file di configurazione della CA radice

Prima di creare una CA, creare un file di configurazione e salvarlo `rootca.conf` come nella directory rootca.

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>Passaggio 3: Creare una CA radice

Prima di tutto, generare la chiave e la richiesta di firma del certificato nella directory rootca.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Creare quindi un certificato CA autofirmato. La firma self-signing è adatta a scopo di test. Specificare le ca_ext file di configurazione nella riga di comando. Questi indicano che il certificato è per una CA radice e può essere usato per firmare certificati ed elenchi di revoche di certificati (CRL). Firmare il certificato ed eseguirlo il commit nel database.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Passaggio 4: Creare la struttura di directory della CA subordinata

Creare una struttura di directory per la CA subordinata.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Passaggio 5: Creare un file di configurazione della CA subordinata

Creare un file di configurazione e salvarlo come subca.conf nella `subca` directory .

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days           
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>Passaggio 6: Creare una CA subordinata

Creare un nuovo numero di serie nel `rootca/db/serial` file per il certificato della CA subordinata.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>È necessario creare un nuovo numero di serie per ogni certificato della CA subordinata e per ogni certificato del dispositivo creato. Certificati diversi non possono avere lo stesso numero di serie.

Questo esempio illustra come creare una CA subordinata o di registrazione. Poiché è possibile usare la CA radice per firmare i certificati, la creazione di una CA subordinata non è strettamente necessaria. La presenza di una CA subordinata, tuttavia, simula gerarchie di certificati reali in cui la CA radice viene mantenuta offline e le CA subordinate emettere certificati client.

Usare il file di configurazione per generare una chiave e una richiesta di firma del certificato.

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Inviare la richiesta di firma del certificato alla CA radice e usare la CA radice per rilasciare e firmare il certificato della CA subordinata. Specificare sub_ca_ext per l'opzione extensions nella riga di comando. Le estensioni indicano che il certificato è per una CA che può firmare certificati ed elenchi di revoche di certificati (CRL). Quando richiesto, firmare il certificato ed eseguirlo il commit nel database.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Passaggio 7: Dimostrare la prova di possesso

A questo punto si dispone sia di un certificato CA radice che di un certificato della CA subordinata. È possibile usare uno dei due per firmare i certificati del dispositivo. Quello scelto deve essere caricato nell'hub IoT. I passaggi seguenti presuppongono che si utilizzi il certificato della CA subordinata. Per caricare e registrare il certificato della CA subordinata nell'hub IoT:

1. Nel portale di Azure passare a IoTHub e selezionare Impostazioni > **certificati**.

1. Selezionare **Aggiungi** per aggiungere il nuovo certificato della CA subordinata.

1. Immettere un nome visualizzato nel **campo Nome** certificato e selezionare il file di certificato PEM creato in precedenza.

1. Selezionare **Salva**. Il certificato viene visualizzato nell'elenco dei certificati con stato **Unverified**. Il processo di verifica proverà che si è proprietari del certificato.

   
1. Selezionare il certificato per visualizzare la **finestra di dialogo Dettagli** certificato.

1. Selezionare **Genera codice di verifica**. Per altre informazioni, vedere [Dimostrare il possesso di un certificato ca.](tutorial-x509-prove-possession.md)

1. Copiare il codice di verifica negli Appunti. È necessario impostare il codice di verifica come soggetto del certificato. Ad esempio, se il codice di verifica è BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A, aggiungerlo come oggetto del certificato, come illustrato nel passaggio 9.

1. Generare una chiave privata.

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. Generare una richiesta di firma del certificato dalla chiave privata. Aggiungere il codice di verifica come oggetto del certificato.

  ```bash
  openssl req -new -key pop.key -out pop.csr
  
    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

10. Creare un certificato usando il file di configurazione della CA radice e il certificato di verifica del possesso.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. Selezionare il nuovo certificato nella **visualizzazione Dettagli** certificato. Per trovare il file PEM, passare alla cartella certs.

12. Dopo il caricamento del certificato, selezionare **Verifica**. Lo stato del certificato della CA deve essere modificato in **Verificato.**

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Passaggio 8: Creare un dispositivo nell'hub IoT

Passare all'hub IoT nel portale di Azure e creare una nuova identità del dispositivo IoT con i valori seguenti:

1. Fornire l'**ID dispositivo** corrispondente al nome soggetto dei certificati del dispositivo.

1. Selezionare il **tipo di autenticazione Firmata CA X.509.**

1. Selezionare **Salva**.

## <a name="step-9---create-a-client-device-certificate"></a>Passaggio 9: Creare un certificato del dispositivo client

Per generare un certificato client, è innanzitutto necessario generare una chiave privata. Il comando seguente illustra come usare OpenSSL per creare una chiave privata. Creare la chiave nella directory della sottoca.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Creare una richiesta di firma del certificato per la chiave. Non è necessario immettere una password di verifica o un nome di società facoltativo. È tuttavia necessario immettere l'ID dispositivo nel campo nome comune.

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

Verificare che la csr sia quella prevista.

```bash
openssl req -text -in device.csr -noout
```

Inviare la richiesta di firma del certificato alla CA subordinata per l'accesso alla gerarchia di certificati. Specificare `client_ext` `-extensions` nell'opzione . Si noti che `Basic Constraints` nel certificato emesso indica che questo certificato non è per una CA. Se si firmano più certificati, assicurarsi di aggiornare il numero di serie prima di generare ogni certificato usando il comando `rand -hex 16 > db/serial` openssl.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Passaggi successivi

Passare a [Test dell'autenticazione del](tutorial-x509-test-certificate.md) certificato per determinare se il certificato può autenticare il dispositivo nell'hub IoT.
