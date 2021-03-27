---
title: "Esercitazione: usare gli script Microsoft per creare certificati di test x. 509 per l'hub Azure. Microsoft Docs"
description: "Esercitazione: usare script personalizzati per creare certificati di autorità di certificazione e dispositivi per l'hub Azure."
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
ms.openlocfilehash: f11aec770818cd4ceeeda1ae7decf30acb9ca92b
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630711"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Esercitazione: uso di script forniti da Microsoft per creare certificati di test

Microsoft fornisce script di PowerShell e bash che consentono di comprendere come creare certificati X. 509 personalizzati e autenticarli in un hub Internet. Gli script si trovano in [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates). Vengono fornite solo a scopo dimostrativo. I certificati creati da tali certificati non devono essere usati per la produzione. I certificati contengono password hardcoded ("1234") e scadono dopo 30 giorni. Per un ambiente di produzione, è necessario usare le procedure consigliate per la creazione di certificati e la gestione della durata.

## <a name="powershell-scripts"></a>Script PowerShell

### <a name="step-1---setup"></a>Passaggio 1-installazione

Ottenere OpenSSL per Windows. Vedere <https://www.openssl.org/docs/faq.html#MISC4> per informazioni su come scaricarlo o <https://www.openssl.org/source/> per crearlo dall'origine. Eseguire quindi gli script preliminari:

1. Copiare gli script da [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) nella directory locale in cui si desidera lavorare. Tutti i file verranno creati come elementi figlio di questa directory.

1. Avviare PowerShell come amministratore.

1. Passare alla directory in cui sono stati caricati gli script.

1. Nella riga di comando impostare la variabile di ambiente sulla `$ENV:OPENSSL_CONF` directory in cui si trova il file di configurazione OpenSSL (OpenSSL. cnf).

1. Eseguire in `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` modo che PowerShell sia in grado di eseguire gli script.

1. Eseguire `. .\ca-certs.ps1`. In questo modo le funzioni dello script vengono riportate nello spazio dei nomi globale di PowerShell.

1. Eseguire `Test-CACertsPrerequisites`. PowerShell usa l'archivio certificati di Windows per gestire i certificati. Questo comando verifica che in seguito non siano presenti conflitti di nomi con i certificati esistenti e che OpenSSL sia configurato correttamente.

### <a name="step-2---create-certificates"></a>Passaggio 2: creare i certificati

Eseguire `New-CACertsCertChain [ecc|rsa]`. ECC è consigliato per i certificati della CA, ma non obbligatorio. Questo script aggiorna la directory e l'archivio certificati di Windows con la CA e i certificati intermedi seguenti:

* intermediate1. pem
* intermediate2. pem
* intermediate3. pem
* RootCA. cer
* RootCA. pem

Al termine dell'esecuzione dello script, aggiungere il nuovo certificato CA (RootCA. PEM) all'hub Internet:

1. Passare all'hub Internet e passare a certificati.

1. Selezionare **Aggiungi**.

1. Immettere un nome visualizzato per il certificato della CA.

1. Caricare il certificato della CA.

1. Selezionare **Salva**.

### <a name="step-3---prove-possession"></a>Passaggio 3: dimostrare il possesso

Ora che il certificato della CA è stato caricato nell'hub Internet, sarà necessario dimostrare di essere effettivamente il proprietario:

1. Selezionare il nuovo certificato CA.

1. Selezionare **genera codice di verifica** nella finestra di dialogo **Dettagli certificato** . Per altre informazioni, vedere [dimostrare il possesso di un certificato della CA](tutorial-x509-prove-possession.md).

1. Creare un certificato che contenga il codice di verifica. Se, ad esempio, il codice di verifica è `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` , eseguire il comando seguente per creare un nuovo certificato nella directory di lavoro contenente l'oggetto `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` . Lo script crea un certificato denominato `VerifyCert4.cer` .

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Caricare `VerifyCert4.cer` nell'hub Internet delle cose nella finestra di dialogo **Dettagli certificato** .

1. Selezionare **Verifica**.

### <a name="step-4---create-a-new-device"></a>Passaggio 4: creare un nuovo dispositivo

Creare un dispositivo per l'hub Internet delle cose:

1. Nell'hub Internet delle cose passare alla sezione **dispositivi** Internet.

1. Aggiungere un nuovo dispositivo con ID `mydevice` .

1. Per l'autenticazione, scegliere la **CA X. 509 firmata**.

1. Eseguire `New-CACertsDevice mydevice` per creare un nuovo certificato del dispositivo. I file seguenti vengono creati nella directory di lavoro:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Passaggio 5: testare il certificato del dispositivo

Passare a [test autenticazione certificati](tutorial-x509-test-certificate.md) per determinare se il certificato del dispositivo è in grado di autenticarsi nell'hub Internet. Sarà necessaria la versione PFX del certificato, `mydevice.pfx` .

### <a name="step-6---cleanup"></a>Passaggio 6: pulizia

Dal menu Start aprire **Gestisci certificati computer** e passare a  **certificati-computer locale > Personal**. Rimuovere i certificati rilasciati da "Azure TestOnly CA *". In modo analogo, rimuovere i certificati appropriati da **>autorità di certificazione radice attendibile > certificati e >autorità di certificazione intermedie > i certificati**.

## <a name="bash-scripts"></a>Script bash

### <a name="step-1---setup"></a>Passaggio 1-installazione

1. Avviare bash.

1. Passare alla directory in cui si desidera lavorare. Tutti i file verranno creati in questa directory.

1. Copiare `*.cnf` e `*.sh` nella directory di lavoro.

### <a name="step-2---create-certificates"></a>Passaggio 2: creare i certificati

1. Eseguire `./certGen.sh create_root_and_intermediate`. Vengono creati i file seguenti nella directory **certs** :

    * Azure-IOT-Test-only. chain. ca. cert. pem
    * Azure-IOT-Test-only. Intermediate. cert. pem
    * Azure-IOT-Test-only. root. ca. cert. pem

1. Passare all'hub Internet e passare a **certificati**.

1. Selezionare **Aggiungi**.

1. Immettere un nome visualizzato per il certificato della CA.

1. Caricare solo il certificato della CA nell'hub Internet. Il nome del certificato è `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Selezionare **Salva**.

### <a name="step-3---prove-possession"></a>Passaggio 3: dimostrare il possesso

1. Selezionare il nuovo certificato CA creato nel passaggio precedente.

1. Selezionare **genera codice di verifica** nella finestra di dialogo **Dettagli certificato** . Per altre informazioni, vedere [dimostrare il possesso di un certificato della CA](tutorial-x509-prove-possession.md).

1. Creare un certificato che contenga il codice di verifica. Se, ad esempio, il codice di verifica è `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` , eseguire il comando seguente per creare un nuovo certificato nella directory di lavoro denominata `verification-code.cert.pem` che contiene l'oggetto `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` .

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Caricare il certificato nell'hub tutto nella finestra di dialogo **Dettagli certificato** .

1. Selezionare **Verifica**.

### <a name="step-4---create-a-new-device"></a>Passaggio 4: creare un nuovo dispositivo

Creare un dispositivo per l'hub Internet delle cose:

1. Nell'hub Internet delle cose passare alla sezione dispositivi Internet.

1. Aggiungere un nuovo dispositivo con ID `mydevice` .

1. Per l'autenticazione, scegliere la **CA X. 509 firmata**.

1. Eseguire `./certGen.sh create_device_certificate mydevice` per creare un nuovo certificato del dispositivo. Vengono creati due file denominati `new-device.cert.pem` e `new-device.cert.pfx` i file nella directory di lavoro.

### <a name="step-5---test-your-device-certificate"></a>Passaggio 5: testare il certificato del dispositivo

Passare a [test autenticazione certificati](tutorial-x509-test-certificate.md) per determinare se il certificato del dispositivo è in grado di autenticarsi nell'hub Internet. Sarà necessaria la versione PFX del certificato, `new-device.cert.pfx` .

### <a name="step-6---cleanup"></a>Passaggio 6: pulizia

Poiché lo script bash crea semplicemente i certificati nella directory di lavoro, è sufficiente eliminarli al termine del test.

## <a name="next-steps"></a>Passaggi successivi

Per testare il certificato, passare a [test autenticazione certificato](tutorial-x509-test-certificate.md) per determinare se il certificato è in grado di autenticare il dispositivo nell'hub Internet.
