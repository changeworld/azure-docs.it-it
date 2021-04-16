---
title: 'Anteprima: Distribuire una macchina virtuale di avvio attendibile'
description: Distribuire una macchina virtuale che usa l'avvio attendibile.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 04/06/2021
ms.custom: template-how-to
ms.openlocfilehash: 295579d17f3b24adcf43f6907cc4b1aca01dcae2
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565917"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Distribuire una macchina virtuale con avvio attendibile abilitato (anteprima)

[L'avvio](trusted-launch.md) attendibile è un modo per migliorare la sicurezza delle [macchine virtuali](generation-2.md) di seconda generazione. L'avvio attendibile protegge da tecniche di attacco avanzate e persistenti combinando tecnologie di infrastruttura come vTPM e avvio sicuro.

> [!IMPORTANT]
> L'avvio attendibile è attualmente in anteprima pubblica.
> 
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
>
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Distribuire tramite il portale

Creare una macchina virtuale con avvio attendibile abilitato.

1. Accedere al [portale](https://aka.ms/TL_preview) di Azure.
   > [!NOTE] 
   > Il collegamento al portale è univoco per l'anteprima di avvio attendibile.
   >  
2. Cercare **Macchine virtuali**.
3. In **Servizi** selezionare **Macchine virtuali**.
4. Nella pagina **Macchine virtuali** selezionare **Aggiungi** e quindi **Macchina virtuale**.
5. In **Dettagli progetto** assicurarsi che sia selezionata la sottoscrizione corretta.
6. In **Gruppo di risorse** selezionare Crea **nuovo** e digitare un nome per il gruppo di risorse oppure selezionare un gruppo di risorse esistente nell'elenco a discesa.
7. In **Dettagli istanza** digitare un nome per il nome della macchina virtuale e scegliere un'area che supporti l'avvio [attendibile.](trusted-launch.md#public-preview-limitations)
8. In **Immagine** selezionare un'immagine di generazione 2 [che supporti l'avvio attendibile.](trusted-launch.md#public-preview-limitations) Assicurarsi che sia visualizzato il messaggio seguente: **Questa immagine supporta l'anteprima di avvio attendibile. Configurare nella scheda Avanzate**.
   > [!TIP]
   > Se la versione Gen 2 dell'immagine desiderata non è visualizzata nell'elenco a discesa, selezionare **Visualizza** tutte le immagini e quindi modificare il filtro Generazione vm per visualizzare solo le immagini di seconda generazione.  Trovare l'immagine nell'elenco  e quindi usare l'elenco a discesa Seleziona per selezionare la versione di generazione 2.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Screenshot che mostra il messaggio che conferma che si tratta di un'immagine di generazione 2 che supporta l'avvio attendibile.":::

13. Selezionare una dimensione della macchina virtuale che supporti l'avvio attendibile. Vedere l'elenco [delle dimensioni supportate.](trusted-launch.md#public-preview-limitations)
14. Compilare le informazioni **dell'account** amministratore e quindi **Regole porta in ingresso**. 
1. Passare alla **scheda Avanzate** selezionandola nella parte superiore della pagina.
1. Scorrere verso il basso fino alla **sezione Generazione vm.** Assicurarsi che **sia selezionata la generazione 2.**
1. Sempre nella scheda **Avanzate scorrere** verso il basso fino a **Avvio** attendibile e quindi selezionare la casella di **controllo Avvio** attendibile. Verranno visualizzate altre due opzioni: Avvio protetto e vTPM. Selezionare le opzioni appropriate per la distribuzione.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Screenshot che mostra le opzioni per l'avvio attendibile.":::

15. Nella parte inferiore della pagina selezionare **Rivedi e crea**
16. Nella pagina **Crea una macchina virtuale** è possibile visualizzare i dettagli sulla macchina virtuale che si sta per distribuire. Quando si è pronti, selezionare **Crea**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Sceenshot della pagina di convalida, che mostra le opzioni di avvio attendibili.":::


La distribuzione della macchina virtuale richiederà alcuni minuti. 

## <a name="deploy-using-a-template"></a>Eseguire la distribuzione usando un modello

È possibile distribuire macchine virtuali di avvio attendibili usando un modello di avvio rapido:

**Linux**:    
[![Distribuzione in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Distribuzione in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Visualizzare e aggiornare

È possibile visualizzare la configurazione di avvio attendibile per una macchina virtuale esistente visitando la **pagina** Panoramica per la macchina virtuale nel portale.

Per modificare la configurazione di avvio attendibile, nel menu a sinistra selezionare **Configurazione** nella **sezione** Impostazioni. È possibile abilitare o disabilitare l'avvio protetto e vTPM dalla **sezione Avvio** attendibile. Al **termine,** selezionare Salva nella parte superiore della pagina. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Screenshot che mostra come modificare la configurazione di avvio attendibile.":::

Se la macchina virtuale è in esecuzione, verrà visualizzato un messaggio che indica che la macchina virtuale verrà riavviata per applicare la configurazione di avvio attendibile modificata. Selezionare **Sì** e quindi attendere il riavvio della macchina virtuale per l'applicazione delle modifiche.


## <a name="verify-secure-boot-and-vtpm"></a>Verificare l'avvio sicuro e vTPM

È possibile verificare che l'avvio sicuro e vTPM siano abilitati nella macchina virtuale.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: convalidare se l'avvio protetto è in esecuzione

Eseguire SSH nella macchina virtuale e quindi eseguire il comando seguente: 

```bash
mokutil --sb-state
```

Se l'avvio protetto è abilitato, il comando restituirà:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: convalidare se vTPM è abilitato

Stabilire una connessione SSH alla VM. Controllare se il dispositivo tpm0 è presente: 

```bash
ls /dev/tpm0
```

Se vTPM è abilitato, il comando restituirà:

```output
/dev/tpm0
```

Se vTPM è disabilitato, il comando restituirà:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: verificare che l'avvio protetto sia in esecuzione

Connettersi alla macchina virtuale usando desktop remoto e quindi eseguire `msinfo32.exe` .

Nel riquadro destro verificare che lo stato di avvio protetto sia **ON**.

## <a name="enable-the-azure-security-center-experience"></a>Abilitare l'Centro sicurezza di Azure di lavoro

Per consentire Centro sicurezza di Azure visualizzare informazioni sulle macchine virtuali di avvio attendibili, è necessario abilitare diversi criteri. Il modo più semplice per abilitare i criteri è distribuire questo modello Resource Manager [nella](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) sottoscrizione. 

Selezionare il pulsante seguente per distribuire i criteri nella sottoscrizione:

[![Distribuzione in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

Il modello deve essere distribuito una sola volta per ogni sottoscrizione. Installa automaticamente `GuestAttestation` ed estensioni in tutte le macchine virtuali `AzureSecurity` supportate. Se si verificano errori, provare a ridistribuire il modello.

Per ottenere vTPM e raccomandazioni di avvio sicuro per le macchine virtuali di avvio attendibili, vedere [Aggiungere un'iniziativa personalizzata alla sottoscrizione.](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription)
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Firmare elementi per l'avvio protetto in Linux

In alcuni casi, potrebbe essere necessario firmare elementi per l'avvio protetto UEFI.  Ad esempio, potrebbe essere necessario vedere How to sign things for Secure Boot for Ubuntu (Come firmare gli elementi per [l'avvio](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) protetto per Ubuntu). In questi casi, è necessario immettere le chiavi di registrazione dell'utilità MOK per la macchina virtuale. A tale scopo, è necessario usare la console seriale di Azure per accedere all'utilità MOK.

1. Abilitare la console seriale di Azure per Linux. Per altre informazioni, vedere [Console seriale per Linux.](/troubleshoot/azure/virtual-machines/serial-console-linux)
1. Accedere al [Portale di Azure](https://portal.azure.com).
1. Cercare Macchine **virtuali e** selezionare la macchina virtuale dall'elenco.
1. Nel menu a sinistra, in **Supporto e risoluzione dei problemi,** selezionare **console seriale**. Si aprirà una pagina a destra, con la console seriale.
1. Accedere alla macchina virtuale usando la console seriale di Azure. Per **l'account** di accesso immettere il nome utente usato durante la creazione della macchina virtuale. Ad esempio, *azureuser*. Quando richiesto, immettere la password associata al nome utente.
1. Dopo aver eseguito l'accesso, usare `mokutil` per importare il file di chiave `.der` pubblica.

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Riavviare il computer dalla console seriale di Azure digitando `sudo reboot` . Inizierà un conto alla rovescia di 10 secondi.
1. Premere il tasto su o giù per interrompere il conto alla rovescia e attendere in modalità console UEFI. Se il timer non viene interrotto, il processo di avvio continua e tutte le modifiche MOK vengono perse.
1. Selezionare l'azione appropriata dal menu dell'utilità MOK.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Screenshot che mostra le opzioni disponibili nel menu di gestione MOK nella console seriale.":::


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni [sull'avvio attendibile](trusted-launch.md) e sulle [macchine virtuali di seconda](generation-2.md) generazione.
