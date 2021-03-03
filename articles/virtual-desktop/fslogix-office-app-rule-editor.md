---
title: Installare Microsoft Office contenitori di applicazioni FSLogix in desktop virtuale di Windows-Azure
description: Come usare l'editor delle regole dell'app per creare un contenitore di applicazioni FSLogix con Office nel desktop virtuale di Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7d66d370f321323ec1aeb45ad0fe628904b14fe6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746038"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>Installare Microsoft Office usando i contenitori di applicazioni FSLogix

È possibile installare Microsoft Office in modo rapido ed efficiente usando un contenitore dell'applicazione FSLogix come modello per le altre macchine virtuali (VM) nel pool host.

Ecco perché l'uso di un contenitore di app FSLogix può contribuire a rendere più veloce l'installazione:

- L'offload delle app di Office in un contenitore di app riduce i requisiti per le dimensioni dell'unità C.
- Gli snapshot o i backup della macchina virtuale richiedono meno risorse.
- La creazione di una pipeline automatizzata tramite l'aggiornamento di una singola immagine rende più semplice l'aggiornamento delle macchine virtuali.
- È necessaria una sola immagine per installare Office (e altre app) in tutte le macchine virtuali nella distribuzione di desktop virtuali Windows.

In questo articolo viene illustrato come configurare un contenitore di applicazioni FSLogix con Office.

## <a name="requirements"></a>Requisiti

Per configurare l'Editor regole sono necessari gli elementi seguenti:

- una macchina virtuale che esegue Windows senza Office installato
- una copia di Office
- una copia di FSLogix installata nella distribuzione
- una condivisione di rete a cui tutte le macchine virtuali nel pool host hanno accesso in sola lettura

## <a name="install-office"></a>Installare Office

Per installare Office nel disco rigido virtuale o in VHDX, abilitare il Remote Desktop Protocol nella macchina virtuale, quindi seguire le istruzioni in [installare Office in un'immagine master del disco rigido virtuale](install-office-on-wvd-master-image.md). Quando si installa, assicurarsi [di usare le licenze corrette](overview.md#requirements).

>[!NOTE]
>Il desktop virtuale Windows richiede l'attivazione di condivisione computer (SCA).

## <a name="install-fslogix"></a>Installare FSLogix

Per installare FSLogix e l'Editor regole, seguire le istruzioni riportate in [scaricare e installare FSLogix](/fslogix/install-ht).

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Creare e preparare un disco rigido virtuale per archiviare Office

Successivamente, sarà necessario creare e preparare un'immagine del disco rigido virtuale per l'uso dell'Editor regole in:

1. Aprire un prompt dei comandi come amministratore. ed eseguire il comando seguente:

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > Assicurarsi di lasciare gli spazi vuoti visualizzati in questo comando.

2. Eseguire quindi il comando seguente:

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   Se il servizio viene trovato, riavviare la macchina virtuale prima di continuare con il passaggio 3.

    ```cmd
    net stop ClickToRunSvc
    ```

3. Successivamente, passare a **Program Files**  >  **FSLogix**  >  **Apps** ed eseguire il comando seguente per creare il disco rigido virtuale di destinazione:

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    Il disco rigido virtuale creato con questo comando deve contenere la cartella C: \\ Program Files \\ Microsoft Office.

    >[!NOTE]
    >Se vengono visualizzati errori, disinstallare Office e iniziare dal passaggio 1.

## <a name="configure-the-rule-editor"></a>Configurare l'Editor regole

Ora che è stata preparata l'immagine, è necessario configurare l'Editor regole e creare un file in cui archiviare le regole.

1. Passare a **programmi**  >  **FSLogix**  >  **app** ed eseguire **RuleEditor.exe**.

2. Selezionare **file**  >  **nuovo**  >  **Crea** per creare un nuovo set di regole, quindi salvare il set di regole in una cartella locale.

3. Selezionare **set di regole vuoto** e quindi fare clic su **OK**.

4. Selezionare il pulsante **+**. Verrà visualizzata la finestra **Aggiungi regola** . In questo modo le opzioni vengono modificate nella finestra di dialogo **Aggiungi regola** .

5. Scegliere **regola contenitore app (VHD)** dal menu a discesa.

6. Immettere **C: \\ Program Files \\ Microsoft Office** nel campo **cartella** .

7. Per il campo **file su disco** , selezionare **\<path\> \\ Office. vhd** dalla sezione **Crea VHD di destinazione** .

8. Selezionare **OK**.

9. Passare alla cartella di lavoro in **C: \\ Users \\ \<username\> \\ Documents \\ FSLogix Rule sets** e cercare i file con estensione FRx e FXA. È necessario spostare questi file nella cartella Rules in **C: \\ Program Files \\ FSLogix \\ Apps \\ Rules** affinché le regole inizino a funzionare.

10. Selezionare **Applica regole a sistema** per rendere effettive le regole.

     >[!NOTE]
     > È necessario applicare i file delle regole dell'app necessari a tutti gli host di sessione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su FSLogix, vedere la [documentazione di FSLogix](/fslogix/).