---
title: 'Esercitazione: Usare script Microsoft per creare certificati di test x.509 per hub IoT di Azure | Microsoft Docs'
description: 'Esercitazione: Usare script personalizzati per creare certificati della CA e del dispositivo per hub IoT di Azure'
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
ms.openlocfilehash: ff4b63f49a87dd9ca6b0ef458bdcf1c285a34a18
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378211"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Esercitazione: Uso di script forniti da Microsoft per creare certificati di test

Microsoft fornisce script di PowerShell e Bash che consentono di comprendere come creare i propri certificati X.509 e autenticarli in un hub IoT. Gli script si trovano in un [repository](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)GitHub. Vengono forniti solo a scopo dimostrativo. I certificati creati da essi non devono essere usati per la produzione. I certificati contengono password hard coded ("1234") e scadono dopo 30 giorni. Per un ambiente di produzione, è necessario usare le proprie procedure consigliate per la creazione di certificati e la gestione della durata.

## <a name="powershell-scripts"></a>Script PowerShell

### <a name="step-1---setup"></a>Passaggio 1: Configurazione

Ottenere OpenSSL per Windows. Vedere <https://www.openssl.org/docs/faq.html#MISC4> per le posizioni in cui scaricarlo o per eseguire la compilazione <https://www.openssl.org/source/> dall'origine. Eseguire quindi gli script preliminari:

1. Copiare gli script da [questo repository](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) GitHub nella directory locale in cui si vuole lavorare. Tutti i file verranno creati come elementi figlio di questa directory.

1. Avviare PowerShell come amministratore.

1. Passare alla directory in cui sono stati caricati gli script.

1. Nella riga di comando impostare la variabile di ambiente sulla directory in cui si trova il file di configurazione `$ENV:OPENSSL_CONF` openssl (openssl.cnf).

1. Eseguire `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` in modo che PowerShell possa eseguire gli script.

1. Eseguire `. .\ca-certs.ps1`. In questo modo le funzioni dello script vengono portate nello spazio dei nomi globale di PowerShell.

1. Eseguire `Test-CACertsPrerequisites`. PowerShell usa l'archivio certificati di Windows per gestire i certificati. Questo comando verifica che non si verificano conflitti di nomi in un secondo momento con i certificati esistenti e che OpenSSL sia configurato correttamente.

### <a name="step-2---create-certificates"></a>Passaggio 2: Creare certificati

Eseguire `New-CACertsCertChain [ecc|rsa]`. ECC è consigliato per i certificati CA, ma non è obbligatorio. Questo script aggiorna la directory e l'archivio certificati di Windows con la CA e i certificati intermedi seguenti:

* intermediate1.pem
* intermediate2.pem
* intermediate3.pem
* RootCA.cer
* RootCA.pem

Dopo aver eseguito lo script, aggiungere il nuovo certificato CA (RootCA.pem) all'hub IoT:

1. Passare all'hub IoT e passare a Certificati.

1. Selezionare **Aggiungi**.

1. Immettere un nome visualizzato per il certificato della CA.

1. Caricare il certificato della CA.

1. Selezionare **Salva**.

### <a name="step-3---prove-possession"></a>Passaggio 3- Dimostrare il possesso

Dopo aver caricato il certificato della CA nell'hub IoT, è necessario dimostrare di essere effettivamente proprietari:

1. Selezionare il nuovo certificato CA.

1. Selezionare **Genera codice di verifica** nella finestra di dialogo **Dettagli** certificato. Per altre informazioni, vedere [Dimostrare il possesso di un certificato ca.](tutorial-x509-prove-possession.md)

1. Creare un certificato contenente il codice di verifica. Ad esempio, se il codice di verifica è , eseguire quanto segue per creare un nuovo certificato nella `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` directory di lavoro contenente l'oggetto `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` . Lo script crea un certificato denominato `VerifyCert4.cer` .

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Caricare `VerifyCert4.cer` nell'hub IoT nella finestra **di dialogo Dettagli** certificato.

1. Selezionare **Verifica**.

### <a name="step-4---create-a-new-device"></a>Passaggio 4: Creare un nuovo dispositivo

Creare un dispositivo per l'hub IoT:

1. Nell'hub IoT passare alla sezione **Dispositivi IoT.**

1. Aggiungere un nuovo dispositivo con ID `mydevice` .

1. Per l'autenticazione, scegliere **X.509 CA Signed**.

1. Eseguire `New-CACertsDevice mydevice` per creare un nuovo certificato del dispositivo. Nella directory di lavoro vengono creati i file seguenti:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Passaggio 5: Testare il certificato del dispositivo

Passare a [Test dell'autenticazione del](tutorial-x509-test-certificate.md) certificato per determinare se il certificato del dispositivo può eseguire l'autenticazione all'hub IoT. Sarà necessaria la versione PFX del certificato, `mydevice.pfx` .

### <a name="step-6---cleanup"></a>Passaggio 6: Pulizia

Dal menu Start aprire Gestisci **certificati computer** e passare a Certificati - Computer locale **> personale.** Rimuovere i certificati rilasciati da "Azure IoT CA TestOnly*". Analogamente, rimuovere i certificati appropriati da>autorità di certificazione radice attendibile > certificati e >certificati intermedi **> certificati**.

## <a name="bash-scripts"></a>Script Bash

### <a name="step-1---setup"></a>Passaggio 1: Configurazione

1. Avviare Bash.

1. Passare alla directory in cui si vuole lavorare. Tutti i file verranno creati in questa directory.

1. Copiare `*.cnf` e nella directory di `*.sh` lavoro.

### <a name="step-2---create-certificates"></a>Passaggio 2: Creare certificati

1. Eseguire `./certGen.sh create_root_and_intermediate`. Nella directory **certs** vengono creati i file seguenti:

    * azure-iot-test-only.chain.ca.cert.pem
    * azure-iot-test-only.intermediate.cert.pem
    * azure-iot-test-only.root.ca.cert.pem

1. Passare all'hub IoT e passare a **Certificati**.

1. Selezionare **Aggiungi**.

1. Immettere un nome visualizzato per il certificato della CA.

1. Caricare solo il certificato della CA nell'hub IoT. Il nome del certificato è `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Selezionare **Salva**.

### <a name="step-3---prove-possession"></a>Passaggio 3 - Dimostrare il possesso

1. Selezionare il nuovo certificato CA creato nel passaggio precedente.

1. Selezionare **Genera codice di verifica** nella finestra di dialogo **Dettagli** certificato. Per altre informazioni, vedere [Dimostrare il possesso di un certificato CA.](tutorial-x509-prove-possession.md)

1. Creare un certificato contenente il codice di verifica. Ad esempio, se il codice di verifica è , eseguire quanto segue per creare un nuovo certificato nella directory di lavoro `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` denominata `verification-code.cert.pem` che contiene l'oggetto `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` .

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Caricare il certificato nell'hub IoT nella finestra **di dialogo Dettagli** certificato.

1. Selezionare **Verifica**.

### <a name="step-4---create-a-new-device"></a>Passaggio 4: Creare un nuovo dispositivo

Creare un dispositivo per l'hub IoT:

1. Nell'hub IoT passare alla sezione Dispositivi IoT.

1. Aggiungere un nuovo dispositivo con ID `mydevice` .

1. Per l'autenticazione, scegliere **X.509 CA Signed**.

1. Eseguire `./certGen.sh create_device_certificate mydevice` per creare un nuovo certificato del dispositivo. Vengono creati due file denominati `new-device.cert.pem` e nella directory di `new-device.cert.pfx` lavoro.

### <a name="step-5---test-your-device-certificate"></a>Passaggio 5: Testare il certificato del dispositivo

Passare a [Test dell'autenticazione del](tutorial-x509-test-certificate.md) certificato per determinare se il certificato del dispositivo può eseguire l'autenticazione all'hub IoT. Sarà necessaria la versione PFX del certificato, `new-device.cert.pfx` .

### <a name="step-6---cleanup"></a>Passaggio 6: Pulizia

Poiché lo script Bash crea semplicemente i certificati nella directory di lavoro, è sufficiente eliminarli al termine del test.

## <a name="next-steps"></a>Passaggi successivi

Per testare il certificato, passare a [Test dell'autenticazione del](tutorial-x509-test-certificate.md) certificato per determinare se il certificato può autenticare il dispositivo nell'hub IoT.
