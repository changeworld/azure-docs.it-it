---
title: Avviare la connessione della macchina virtuale - Azure
description: Come configurare la funzionalità Avvia macchina virtuale alla connessione.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 161a4d578509a7752f9438ce8f05d599bdb54e93
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832285"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Avviare la macchina virtuale alla connessione (anteprima)

> [!IMPORTANT]
> La funzionalità Start VM on Connect è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La funzionalità Avvia macchina virtuale (VM) in Connessione (anteprima) consente di risparmiare sui costi consentendo agli utenti finali di attivare le macchine virtuali solo quando ne hanno bisogno. È quindi possibile disattivare le macchine virtuali quando non sono necessarie.

>[!NOTE]
>Desktop virtuale Windows (versione classica) non supporta questa funzionalità.

## <a name="requirements-and-limitations"></a>Requisiti e limitazioni

È possibile abilitare la funzionalità Start VM on Connect solo per i pool di host personali. Per altre informazioni sui pool di host personali, vedere [Ambiente Desktop virtuale Windows.](environment-setup.md#host-pools)

I client Desktop remoto seguenti supportano la funzionalità Avvia macchina virtuale in Connessione:

- [Client Web](connect-web.md)
- [Client Windows (versione 1.2748 o successiva)](connect-windows-7-10.md)

È possibile controllare la disponibilità di annunci sugli aggiornamenti e sul supporto client nel [forum della community tecnica](https://aka.ms/wvdtc).

Il Azure per enti pubblici cloud non supporta attualmente Avvia macchina virtuale in Connect.

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Creare un ruolo personalizzato per Avviare una macchina virtuale in Connect

Prima di configurare la funzionalità Avvia macchina virtuale in Connect, è necessario assegnare alla macchina virtuale un ruolo controllo degli accessi in base al ruolo personalizzato (controllo degli accessi in base al ruolo). Questo ruolo consente a Desktop virtuale Windows di gestire le macchine virtuali nella sottoscrizione. È anche possibile usare questo ruolo per attivare le macchine virtuali, verificarne lo stato e segnalare informazioni di diagnostica. Per altre informazioni sulle operazioni di ogni ruolo, vedere Ruoli personalizzati [di Azure](../role-based-access-control/custom-roles.md).

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Per usare il portale di Azure per assegnare un ruolo personalizzato per Avviare la macchina virtuale in Connect:

1. Aprire il portale di Azure e passare a **Sottoscrizioni.**

2. Passare a **Controllo di accesso (IAM)** e selezionare **Aggiungi un ruolo personalizzato.**

    > [!div class="mx-imgBorder"]
    > ![Screenshot di un menu a discesa dal pulsante Aggiungi in Controllo di accesso (IAM). "Aggiungi un ruolo personalizzato" è evidenziato in rosso.](media/add-custom-role.png)

3. Assegnare quindi un nome al ruolo personalizzato e aggiungere una descrizione. È consigliabile assegnare il nome "start VM on connect".

4. Nella scheda **Autorizzazioni** aggiungere le autorizzazioni seguenti alla sottoscrizione a cui si sta assegnando il ruolo: 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read

5. Al termine, selezionare **OK.**

Successivamente, sarà necessario assegnare il ruolo per concedere l'accesso a Desktop virtuale Windows.

Per assegnare il ruolo personalizzato:

1. Nella scheda **Controllo di accesso (IAM)** selezionare **Aggiungi assegnazioni di ruolo**.

2. Selezionare il ruolo appena creato.

3. Nella barra di ricerca immettere e selezionare **Desktop virtuale Windows.**

      >[!NOTE]
      >Se Desktop virtuale Windows (versione classica) è stato distribuito, potrebbero essere disponibili due app. Assegnare il ruolo a entrambe le app che vengono visualizzati.
      >
      > [!div class="mx-imgBorder"]
      > ![Screenshot della scheda Controllo di accesso (IAM). Nella barra di ricerca sia Desktop virtuale Windows che Desktop virtuale Windows (versione classica) sono evidenziati in rosso.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Creare un ruolo personalizzato con un modello di file JSON

Se si usa un file JSON per creare il ruolo personalizzato, l'esempio seguente mostra un modello di base che è possibile usare. Assicurarsi di sostituire il valore dell'ID sottoscrizione con l'ID sottoscrizione a cui si vuole assegnare il ruolo.

```json
{
    "properties": {
        "roleName": "start VM on connect",
        "description": "Friendly description.",
        "assignableScopes": [
            "/subscriptions/<SubscriptionID>"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/virtualMachines/start/action",
                    "Microsoft.Compute/virtualMachines/read"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

## <a name="configure-the-start-vm-on-connect-feature"></a>Configurare la funzionalità Start VM on Connect (Avvia macchina virtuale alla connessione)

Dopo aver assegnato il ruolo alla sottoscrizione, è possibile configurare la funzionalità Avvia macchina virtuale in Connessione.

### <a name="deployment-considerations"></a>Considerazioni sulla distribuzione 

Avviare la macchina virtuale in Connect è un'impostazione del pool di host. Se si desidera che questa funzionalità sia utilizzata solo da un gruppo selezionato di utenti, assicurarsi di assegnare solo il ruolo richiesto agli utenti da aggiungere.

>[!IMPORTANT]
> È possibile configurare questa funzionalità solo nei pool host esistenti. Questa funzionalità non è disponibile quando si crea un nuovo pool di host.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Per usare il portale di Azure per configurare Start VM on Connect(Avvia macchina virtuale in Connect):

1. Aprire il browser e passare al [portale di Azure](https://portal.azure.com).

2. Nel portale di Azure passare a **Desktop virtuale Windows.**

3. Selezionare **Pool di host** e quindi trovare il pool di host che contiene i desktop personali a cui è stato assegnato il ruolo.

   >[!NOTE]
   > Il pool di host in cui si configura questa funzionalità deve avere desktop personali con assegnazioni di ruolo dirette. Se i desktop nel pool di host non sono configurati correttamente, il processo di configurazione non funzionerà.

4. Nel pool di host selezionare **Proprietà**. In **Avvia macchina virtuale alla connessione** selezionare **Sì,** quindi selezionare **Salva** per applicare immediatamente l'impostazione.

    > [!div class="mx-imgBorder"]
    > ![Screenshot del Finestra Proprietà. L'opzione Avvia macchina virtuale alla connessione è evidenziata in rosso.](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>Usare PowerShell

Per configurare questa impostazione con PowerShell, è necessario assicurarsi di avere i nomi del gruppo di risorse e dei pool host da configurare. È anche necessario installare il modulo Azure PowerShell [(versione 2.1.0 o successiva).](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0)

Per configurare Start VM on Connect using PowerShell :To configure Start VM on Connect using PowerShell:To configure Start VM on Connect using PowerShell:

1. Aprire una finestra di comando di PowerShell.

2. Eseguire il cmdlet seguente per abilitare Avvia macchina virtuale in Connect:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Eseguire il cmdlet seguente per disabilitare Avvia macchina virtuale in Connect:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Esperienza utente

Nelle sessioni tipiche, il tempo necessario per la connessione di un utente a una macchina virtuale deallocata aumenta perché la macchina virtuale necessita di tempo per riattivarla, in modo molto simile all'attivazione di un computer fisico. Il Desktop remoto client ha un indicatore che consente all'utente di sapere che il PC è acceso durante la connessione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se la funzionalità si verifica in caso di problemi, è consigliabile usare la funzionalità di [diagnostica](diagnostics-log-analytics.md) di Desktop virtuale Windows per verificare la presenza di problemi. Se viene visualizzato un messaggio di errore, assicurarsi di prestare particolare attenzione al contenuto del messaggio e copiare il nome dell'errore in un punto di riferimento.

È anche possibile usare [Monitoraggio di Azure desktop virtuale Windows](azure-monitor.md) per ottenere suggerimenti su come risolvere i problemi.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi che la documentazione per la risoluzione dei problemi o la funzionalità di diagnostica non è stata in grado di risolvere, vedere le domande frequenti sull'avvio della macchina [virtuale in Connect](start-virtual-machine-connect-faq.md).
