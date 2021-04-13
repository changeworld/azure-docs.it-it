---
title: Avvia connessione macchina virtuale-Azure
description: Come configurare la funzionalità Avvia macchina virtuale in connessione.
author: Heidilohr
ms.topic: how-to
ms.date: 04/10/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: d3ef8e3656051c4a99ab52a7b52a0d623fdf9ce2
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303960"
---
# <a name="start-virtual-machine-on-connect-preview"></a>Avvia macchina virtuale in connessione (anteprima)

> [!IMPORTANT]
> La funzionalità avvia VM on Connect è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La funzionalità Avvia macchina virtuale (VM) in fase di connessione (anteprima) consente di ridurre i costi consentendo di deallocare le macchine virtuali quando non vengono usate. Quando è necessario usare di nuovo la macchina virtuale, è sufficiente riattivare le macchine virtuali.

>[!NOTE]
>Desktop virtuale Windows (classico) non supporta questa funzionalità.

## <a name="requirements-and-limitations"></a>Requisiti e limitazioni

È possibile abilitare solo la funzionalità Avvia macchina virtuale in connessione per i pool host personali. Per ulteriori informazioni sui pool di host personali, vedere [ambiente desktop virtuale di Windows](environment-setup.md#host-pools).

I client desktop remoto seguenti supportano la funzionalità Avvia macchina virtuale in connessione:

- [Client Web](connect-web.md)
- [Il client Windows (versione 1,2748 o successiva)](connect-windows-7-10.md)

È possibile verificare la presenza di annunci sugli aggiornamenti e sul supporto client nel [Forum della community della tecnologia](https://aka.ms/wvdtc).

Il cloud di Azure per enti pubblici attualmente non supporta l'avvio di una macchina virtuale in Connect.

## <a name="create-a-custom-role-for-start-vm-on-connect"></a>Creare un ruolo personalizzato per avviare una macchina virtuale in Connect

Prima di poter configurare la funzionalità di avvio della macchina virtuale in Connect, è necessario assegnare alla VM un ruolo personalizzato di controllo degli accessi in base al ruolo. Questo ruolo consente al desktop virtuale di Windows di gestire le macchine virtuali nella sottoscrizione. È anche possibile usare questo ruolo per attivare le macchine virtuali, controllarne lo stato e segnalare le informazioni di diagnostica. Per altre informazioni sulle operazioni svolte da ogni ruolo, vedere [ruoli personalizzati di Azure](../role-based-access-control/custom-roles.md).

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Per usare il portale di Azure per assegnare un ruolo personalizzato per l'avvio della macchina virtuale in Connect:

1. Aprire il portale di Azure e passare a **sottoscrizioni**.

2. Passare a **controllo di accesso (IAM)** e selezionare **Aggiungi un ruolo personalizzato**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot di un menu a discesa del pulsante Aggiungi in controllo di accesso (IAM). "Aggiungi un ruolo personalizzato" è evidenziato in rosso.](media/add-custom-role.png)

3. Assegnare quindi un nome al ruolo personalizzato e aggiungere una descrizione. Si consiglia di denominarlo "Avvia macchina virtuale in connessione".

4. Nella scheda **autorizzazioni** aggiungere le seguenti autorizzazioni alla sottoscrizione a cui si sta assegnando il ruolo: 
 
   - Microsoft.Compute/virtualMachines/start/action
   - Microsoft.Compute/virtualMachines/read

5. Al termine, selezionare **OK**.

Successivamente, sarà necessario assegnare il ruolo per concedere l'accesso al desktop virtuale di Windows.

Per assegnare il ruolo personalizzato:

1. Nella **scheda controllo di accesso (IAM)** selezionare **Aggiungi assegnazioni di ruolo**.

2. Selezionare il ruolo appena creato.

3. Nella barra di ricerca immettere e selezionare **desktop virtuale di Windows**.

      >[!NOTE]
      >Se è stato distribuito desktop virtuale di Windows (classico), potrebbero essere visualizzate due app. Assegnare il ruolo a entrambe le app visualizzate.
      >
      > [!div class="mx-imgBorder"]
      > ![Screenshot della scheda controllo di accesso (IAM). Nella barra di ricerca, il desktop virtuale Windows e il desktop virtuale Windows (classico) sono evidenziati in rosso.](media/add-role-assignment.png)

### <a name="create-a-custom-role-with-a-json-file-template"></a>Creare un ruolo personalizzato con un modello di file JSON

Se si usa un file JSON per creare il ruolo personalizzato, l'esempio seguente mostra un modello di base che è possibile usare. Assicurarsi di sostituire il valore di ID sottoscrizione con l'ID sottoscrizione a cui si vuole assegnare il ruolo.

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

## <a name="configure-the-start-vm-on-connect-feature"></a>Configurare la funzionalità Avvia macchina virtuale in connessione

Ora che la sottoscrizione è stata assegnata al ruolo, è possibile configurare la funzionalità di avvio della macchina virtuale in fase di connessione.

### <a name="deployment-considerations"></a>Considerazioni sulla distribuzione 

Avvio della macchina virtuale in Connect è un'impostazione del pool host. Se si desidera utilizzare questa funzionalità solo per un gruppo selezionato di utenti, assicurarsi di assegnare solo il ruolo richiesto agli utenti che si desidera aggiungere.

>[!IMPORTANT]
> Questa funzionalità può essere configurata solo nei pool host esistenti. Questa funzionalità non è disponibile quando si crea un nuovo pool host.

### <a name="use-the-azure-portal"></a>Usare il portale di Azure

Per usare il portale di Azure per configurare la macchina virtuale di avvio in Connect:

1. Aprire il browser e passare al [portale di Azure](https://portal.azure.com/?feature.startVMonConnect=true#home). Si consiglia di aprire il portale di Azure in una finestra InPrivate.

2. Nella portale di Azure passare a **desktop virtuale di Windows**.

3. Selezionare **pool host**, quindi individuare il pool host che contiene i desktop personali a cui è stato assegnato il ruolo.

   >[!NOTE]
   > Il pool host in cui si configura questa funzionalità deve avere desktop personali con assegnazioni di ruolo dirette. Se i desktop nel pool host non sono configurati correttamente, il processo di configurazione non funzionerà.

4. Nel pool host selezionare **Proprietà**. In **Avvia macchina virtuale in connessione** selezionare **Sì**, quindi selezionare **Salva** per applicare immediatamente l'impostazione.

    > [!div class="mx-imgBorder"]
    > ![Screenshot del Finestra Proprietà. L'opzione avvia VM on Connect è evidenziata in rosso.](media/properties-start-vm-on-connect.png)

### <a name="use-powershell"></a>Usare PowerShell

Per configurare questa impostazione con PowerShell, è necessario assicurarsi di avere i nomi del gruppo di risorse e dei pool host che si desidera configurare. Sarà inoltre necessario installare [il modulo Azure PowerShell (versione 2.1.0 o successiva)](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.1.0).

Per configurare la macchina virtuale di avvio durante la connessione tramite PowerShell:

1. Aprire una finestra di comando di PowerShell.

2. Eseguire il cmdlet seguente per abilitare l'avvio della macchina virtuale alla connessione:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$true
    ```

3. Eseguire il cmdlet seguente per disabilitare l'avvio della macchina virtuale in Connect:

    ```powershell
    Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -StartVMOnConnect:$false
    ```

## <a name="user-experience"></a>Esperienza utente

Nelle sessioni tipiche, il tempo necessario per la connessione di un utente a una macchina virtuale deallocata aumenta perché la macchina virtuale richiede tempo per riattivarsi, molto simile all'attivazione di un computer fisico. Il client Desktop remoto dispone di un indicatore che informa l'utente che il PC è acceso durante la connessione.

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verificano problemi durante la funzionalità, è consigliabile utilizzare la [funzionalità diagnostica](diagnostics-log-analytics.md) desktop virtuali di Windows per verificare la presenza di problemi. Se viene visualizzato un messaggio di errore, assicurarsi di prestare attenzione al contenuto del messaggio e di copiare il nome dell'errore in un punto qualsiasi per riferimento.

È anche possibile usare [monitoraggio di Azure per desktop virtuale Windows](azure-monitor.md) per ottenere suggerimenti su come risolvere i problemi.

## <a name="next-steps"></a>Passaggi successivi

Se si verificano problemi che la documentazione o la funzionalità di diagnostica per la risoluzione dei problemi non è stata in grado di risolvere, consultare le [domande frequenti su come avviare una macchina virtuale](start-virtual-machine-connect-faq.md).