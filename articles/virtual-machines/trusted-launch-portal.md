---
title: 'Anteprima: distribuire una VM di avvio attendibile'
description: Distribuire una VM che usa l'avvio attendibile.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 03/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 6499e4b3404a255025f88488e73da1efb6449296
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075952"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled-preview"></a>Distribuire una macchina virtuale con avvio attendibile abilitato (anteprima)

Il [lancio attendibile](trusted-launch.md) è un modo per migliorare la sicurezza delle macchine virtuali di [seconda generazione](generation-2.md) . Il lancio attendibile protegge da tecniche di attacco avanzate e persistenti combinando tecnologie di infrastruttura quali vTPM e avvio protetto.

> [!IMPORTANT]
> L'avvio attendibile è attualmente disponibile in anteprima pubblica.
> 
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
>
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="deploy-using-the-portal"></a>Distribuire tramite il portale

Creare una macchina virtuale con avvio attendibile abilitato.

1. Accedere al [portale](https://aka.ms/TL_preview) di Azure.
   > [!NOTE] 
   > Il collegamento del portale è univoco per l'anteprima di avvio attendibile.
   >  
2. Cercare le **macchine virtuali**.
3. In **Servizi** selezionare **Macchine virtuali**.
4. Nella pagina **macchine virtuali** selezionare **Aggiungi**, quindi selezionare **macchina virtuale**.
5. In **Dettagli progetto** verificare che sia selezionata la sottoscrizione corretta.
6. In **gruppo di risorse** selezionare **Crea nuovo** e digitare un nome per il gruppo di risorse oppure selezionare un gruppo di risorse esistente nell'elenco a discesa.
7. In **Dettagli istanza** Digitare un nome per il nome della macchina virtuale e scegliere un'area che supporti l' [avvio attendibile](trusted-launch.md#public-preview-limitations).
8. In **immagine** selezionare un' [immagine che supporti l'avvio attendibile](trusted-launch.md#public-preview-limitations). È possibile che venga visualizzata solo la versione di generazione 1 dell'immagine, ovvero OK, procedere al passaggio successivo.
9. Passare alla scheda **Avanzate** selezionandola nella parte superiore della pagina.
10. Scorrere verso il basso fino alla sezione **generazione VM** e quindi selezionare **generazione 2**.
11. Sempre nella scheda **Avanzate** scorrere fino a **avvio attendibile**, quindi selezionare la casella di controllo **avvio attendibile** . Verranno visualizzate altre due opzioni: avvio protetto e vTPM. Selezionare le opzioni appropriate per la distribuzione.

    :::image type="content" source="media/trusted-launch/trusted-launch-portal.png" alt-text="Screenshot che mostra le opzioni per l'avvio attendibile.":::

12. Tornare alla scheda informazioni di **base** , in **immagine** e verificare che venga visualizzato il messaggio seguente: **questa immagine supporta l'anteprima di avvio attendibile. Configurare nella scheda Avanzate**. È ora possibile selezionare l'immagine di generazione 2.

    :::image type="content" source="media/trusted-launch/gen-2-image.png" alt-text="Screenshot che mostra il messaggio per confermare che si tratta di un'immagine Gen2 che supporta l'avvio attendibile.":::

13. Selezionare le dimensioni della macchina virtuale che supportano l'avvio attendibile. Vedere l'elenco delle [dimensioni supportate](trusted-launch.md#public-preview-limitations).
14. Immettere le informazioni sull' **account amministratore** e quindi le regole per le **porte in ingresso**.
15. Nella parte inferiore della pagina selezionare **Verifica + crea**
16. Nella pagina **creare una macchina virtuale** è possibile visualizzare i dettagli relativi alla VM che si sta per distribuire. Quando si è pronti, selezionare **Crea**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Sceenshot della pagina convalida, che mostra le opzioni di avvio attendibili, sono incluse.":::


La distribuzione della macchina virtuale richiederà alcuni minuti. 

## <a name="deploy-using-a-template"></a>Eseguire la distribuzione usando un modello

È possibile distribuire VM con avvio attendibile usando un modello di avvio rapido:

**Linux**:    
[![Distribuzione in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**:    
[![Distribuzione in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-vm-trustedlaunch-windows%2FcreateUiDefinition.json)

## <a name="view-and-update"></a>Visualizza e aggiorna

È possibile visualizzare la configurazione di avvio attendibile per una macchina virtuale esistente visitando la pagina **Panoramica** relativa alla macchina virtuale nel portale.

Per modificare la configurazione di avvio attendibile, nel menu a sinistra selezionare **configurazione** nella sezione **Impostazioni** . È possibile abilitare o disabilitare l'avvio protetto e vTPM dalla sezione **avvio attendibile** . Al termine, selezionare **Save (Salva** ) nella parte superiore della pagina. 

:::image type="content" source="media/trusted-launch/configuration.png" alt-text="Screenshot di come modificare la configurazione di avvio attendibile.":::

Se la macchina virtuale è in esecuzione, verrà visualizzato un messaggio che indica che la macchina virtuale verrà riavviata per applicare la configurazione di avvio attendibile modificata. Selezionare **Sì** e attendere che la macchina virtuale venga riavviata per rendere effettive le modifiche.


## <a name="verify-secure-boot-and-vtpm"></a>Verificare l'avvio protetto e vTPM

È possibile verificare che l'avvio protetto e vTPM siano abilitati nella macchina virtuale.
    
### <a name="linux-validate-if-secure-boot-is-running"></a>Linux: verificare se l'avvio protetto è in esecuzione

Connettersi tramite SSH alla macchina virtuale ed eseguire il comando seguente: 

```bash
mokutil --sb-state
```

Se l'avvio protetto è abilitato, il comando restituirà:
 
```bash
SecureBoot enabled 
```

### <a name="linux-validate-if-vtpm-is-enabled"></a>Linux: verificare se vTPM è abilitato

Stabilire una connessione SSH alla VM. Controllare se è presente il dispositivo tpm0: 

```bash
ls /dev/tpm0
```

Se vTPM è abilitata, il comando restituirà:

```output
/dev/tpm0
```

Se vTPM è disabilitato, il comando restituirà:

```output
ls: cannot access '/dev/tpm0': No such file or directory
```

### <a name="windows-validate-that-secure-boot-is-running"></a>Windows: verificare che l'avvio protetto sia in esecuzione

Connettersi alla macchina virtuale usando desktop remoto e quindi eseguire `msinfo32.exe` .

Nel riquadro di destra verificare che lo stato di avvio protetto sia **on**.

## <a name="enable-the-azure-security-center-experience"></a>Abilitare l'esperienza del Centro sicurezza di Azure

Per abilitare il Centro sicurezza di Azure per visualizzare informazioni sulle macchine virtuali di avvio attendibili, è necessario abilitare diversi criteri. Il modo più semplice per abilitare i criteri consiste nel distribuire questo [modello di gestione risorse](https://github.com/prash200/azure-quickstart-templates/tree/master/101-asc-trustedlaunch-policies) alla sottoscrizione. 

Selezionare il pulsante seguente per distribuire i criteri alla sottoscrizione:

[![Distribuzione in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fprash200%2Fazure-quickstart-templates%2Fmaster%2F101-asc-trustedlaunch-policies%2Fazuredeploy.json)

Il modello deve essere distribuito una sola volta per ogni sottoscrizione. Installa automaticamente le `GuestAttestation` `AzureSecurity` estensioni e in tutte le macchine virtuali supportate. Se si verificano errori, provare a ridistribuire il modello.

Per ottenere vTPM e consigli di avvio protetti per le macchine virtuali con avvio attendibile, vedere [aggiungere un'iniziativa personalizzata alla sottoscrizione](../security-center/custom-security-policies.md#to-add-a-custom-initiative-to-your-subscription).
 
## <a name="sign-things-for-secure-boot-on-linux"></a>Firmare gli elementi per l'avvio protetto in Linux

In alcuni casi, potrebbe essere necessario firmare gli elementi per l'avvio protetto UEFI.  Ad esempio, potrebbe essere necessario esaminare [come firmare gli elementi per l'avvio protetto](https://ubuntu.com/blog/how-to-sign-things-for-secure-boot) per Ubuntu. In questi casi, è necessario immettere le chiavi di registrazione dell'utilità per la macchina virtuale. Per eseguire questa operazione, è necessario usare la console seriale di Azure per accedere all'utilità di.

1. Abilitare la console seriale di Azure per Linux. Per altre informazioni, vedere [console seriale per Linux](/troubleshoot/azure/virtual-machines/serial-console-linux).
1. Accedere al [Portale di Azure](https://portal.azure.com).
1. Cercare le **macchine virtuali** e selezionare la VM dall'elenco.
1. Nel menu a sinistra, in **supporto e risoluzione dei problemi**, selezionare **console seriale**. Viene visualizzata una pagina a destra, con la console seriale.
1. Accedere alla VM usando la console seriale di Azure. Per **login** immettere il nome utente usato durante la creazione della macchina virtuale. Ad esempio *azureuser*. Quando richiesto, immettere la password associata al nome utente.
1. Una volta effettuato l'accesso, utilizzare `mokutil` per importare il file di chiave pubblica `.der` .

    ```bash
    sudo mokutil –import <path to public key.der> 
    ```
1. Riavviare il computer dalla console seriale di Azure digitando `sudo reboot` . Inizierà un conto alla rovescia di 10 secondi.
1. Premere il tasto freccia su o giù per interrompere il conto alla rovescia e attendere in modalità console UEFI. Se il timer non viene interrotto, il processo di avvio continua e tutte le modifiche di di questo oggetto vengono perse.
1. Selezionare l'azione appropriata dal menu dell'utilità di.

    :::image type="content" source="media/trusted-launch/mok-mangement.png" alt-text="Screenshot che mostra le opzioni disponibili nel menu di gestione di gestione dei giochi nella console seriale.":::


## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [lancio attendibile](trusted-launch.md) e sulle VM di [seconda generazione](generation-2.md) .
