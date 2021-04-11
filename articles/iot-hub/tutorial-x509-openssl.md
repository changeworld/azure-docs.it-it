---
title: "Esercitazione: usare OpenSSL per creare certificati di test X. 509 per l'hub Azure Internet | Microsoft Docs"
description: "Esercitazione: usare OpenSSL per creare certificati di autorità di certificazione e dispositivi per l'hub Azure."
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
ms.openlocfilehash: 4379c8f43bbfa539179b821bf6b18a01518afad6
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384307"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Esercitazione: uso di OpenSSL per creare certificati di test

Sebbene sia possibile acquistare certificati X. 509 da un'autorità di certificazione attendibile, creare una gerarchia di certificati di test personalizzata o usare certificati autofirmati è sufficiente per testare l'autenticazione dei dispositivi dell'hub. L'esempio seguente usa [openssl](https://www.openssl.org/) e il [Cookbook di OpenSSL](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) per creare un'autorità di certificazione (CA), una CA subordinata e un certificato del dispositivo. Nell'esempio vengono quindi firmati la CA subordinata e il certificato del dispositivo in una gerarchia di certificati. Questa operazione viene presentata solo a scopo esemplificativo.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Passaggio 1: creare la struttura della directory CA radice

Creare una struttura di directory per l'autorità di certificazione.

* La directory **certs** archivia nuovi certificati.
* La directory **DB** viene utilizzata per il database del certificato.
* La directory **privata** archivia la chiave privata della CA.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Passaggio 2: creare un file di configurazione dell'autorità di certificazione radice

Prima di creare una CA, creare un file di configurazione e salvarlo come `rootca.conf` nella directory rootca

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

## <a name="step-3---create-a-root-ca"></a>Passaggio 3: creare una CA radice

Per prima cosa, generare la chiave e la richiesta di firma del certificato (CSR) nella directory rootca

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Successivamente, creare un certificato della CA autofirmato. La firma automatica è adatta a scopo di test. Specificare le estensioni del file di configurazione ca_ext nella riga di comando. Indica che il certificato è per una CA radice e può essere usato per firmare i certificati e gli elenchi di revoche di certificati (CRL). Firmare il certificato ed eseguirne il commit nel database.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Passaggio 4: creare la struttura della directory della CA subordinata

Creare una struttura di directory per la CA subordinata.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Passaggio 5: creare un file di configurazione dell'autorità di certificazione subordinata

Creare un file di configurazione e salvarlo come SubCA. conf nella `subca` Directory.

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

## <a name="step-6---create-a-subordinate-ca"></a>Passaggio 6: creare una CA subordinata

Creare un nuovo numero di serie nel `rootca/db/serial` file per il certificato della CA subordinata.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>È necessario creare un nuovo numero di serie per ogni certificato della CA subordinata e ogni certificato del dispositivo creato. I certificati diversi non possono avere lo stesso numero di serie.

Questo esempio illustra come creare una CA subordinata o di registrazione. Poiché è possibile usare la CA radice per firmare i certificati, la creazione di una CA subordinata non è strettamente necessaria. La presenza di una CA subordinata, tuttavia, simula le gerarchie di certificati reali in cui la CA radice viene mantenuta offline e le autorità di certificazione subordinate emettono certificati client.

Usare il file di configurazione per generare una chiave e una richiesta di firma del certificato (CSR).

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Inviare la richiesta CSR alla CA radice e usare la CA radice per emettere e firmare il certificato della CA subordinata. Specificare sub_ca_ext per l'opzione Extensions nella riga di comando. Le estensioni indicano che il certificato è per una CA che può firmare certificati e elenchi di revoche di certificati (CRL). Quando richiesto, firmare il certificato ed eseguirne il commit nel database.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Passaggio 7: dimostrare la prova del possesso

A questo punto si dispone di un certificato CA radice e di un certificato CA subordinato. È possibile usare uno dei due per firmare i certificati del dispositivo. Quello scelto deve essere caricato nell'hub Internet delle cose. I passaggi seguenti presuppongono che si usi il certificato della CA subordinata. Per caricare e registrare il certificato della CA subordinata nell'hub Internet delle cose:

1. Nella portale di Azure passare a IoTHub e selezionare **impostazioni > certificati**.

1. Selezionare **Aggiungi** per aggiungere il nuovo certificato della CA subordinata.

1. Immettere un nome visualizzato nel campo **nome certificato** e selezionare il file di certificato PEM creato in precedenza.

1. Selezionare **Salva**. Il certificato viene visualizzato nell'elenco dei certificati con lo stato non **verificato**. Il processo di verifica proverà a essere il proprietario del certificato.

   
1. Selezionare il certificato per visualizzare la finestra di dialogo **Dettagli certificato** .

1. Selezionare **genera codice di verifica**. Per altre informazioni, vedere [dimostrare il possesso di un certificato della CA](tutorial-x509-prove-possession.md).

1. Copiare il codice di verifica negli Appunti. È necessario impostare il codice di verifica come soggetto del certificato. Se, ad esempio, il codice di verifica è BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A, aggiungerlo come oggetto del certificato, come illustrato nel passaggio 9.

1. Generare una chiave privata.

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. Generare una richiesta di firma del certificato (CSR) dalla chiave privata. Aggiungere il codice di verifica come oggetto del certificato.

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

10. Creare un certificato usando il file di configurazione dell'autorità di certificazione radice e CSR per il certificato di prova del possesso.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. Selezionare il nuovo certificato nella visualizzazione dei **Dettagli del certificato** . Per trovare il file PEM, passare alla cartella certs.

12. Dopo il caricamento del certificato, selezionare **Verifica**. Lo stato del certificato della CA deve essere modificato in **verificato**.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Passaggio 8: creare un dispositivo nell'hub Internet delle cose

Passare all'hub Internet delle cose nel portale di Azure e creare una nuova identità del dispositivo Internet con i valori seguenti:

1. Fornire l'**ID dispositivo** corrispondente al nome soggetto dei certificati del dispositivo.

1. Selezionare il tipo di autenticazione **firmata CA X. 509** .

1. Selezionare **Salva**.

## <a name="step-9---create-a-client-device-certificate"></a>Passaggio 9: creare un certificato del dispositivo client

Per generare un certificato client, è innanzitutto necessario generare una chiave privata. Il comando seguente illustra come usare OpenSSL per creare una chiave privata. Creare la chiave nella directory SubCA

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Creare una richiesta di firma del certificato (CSR) per la chiave. Non è necessario immettere una password di verifica o un nome della società facoltativo. È necessario, tuttavia, immettere l'ID del dispositivo nel campo nome comune.

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

Verificare che il CSR sia quello previsto.

```bash
openssl req -text -in device.csr -noout
```

Inviare la richiesta CSR alla CA subordinata per l'accesso alla gerarchia del certificato. Specificare `client_ext` nell' `-extensions` opzione. Si noti che `Basic Constraints` nel certificato emesso indica che questo certificato non è per una CA. Se si firmano più certificati, assicurarsi di aggiornare il numero di serie prima di generare ogni certificato usando il `rand -hex 16 > db/serial` comando openssl.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Passaggi successivi

Passare a [test autenticazione certificati](tutorial-x509-test-certificate.md) per determinare se il certificato è in grado di autenticare il dispositivo nell'hub Internet.
