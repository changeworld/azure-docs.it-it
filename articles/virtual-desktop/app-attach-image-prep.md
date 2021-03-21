---
title: Windows Virtual Desktop prepara MSIX app alleghi immagine anteprima-Azure
description: Come creare un'immagine di alconnessione app MSIX per un pool host di desktop virtuali Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 204cc9a05d62caf62179100fa3496be422a3ec0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97425884"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Preparare un'immagine MSIX per desktop virtuale Windows

> [!IMPORTANT]
> La connessione all'app MSIX è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La connessione all'app MSIX (anteprima) è una soluzione di applicazione a più livelli che consente di alleghi dinamicamente le app da un pacchetto MSIX a una sessione utente. Il sistema di pacchetti MSIX separa le app dal sistema operativo, semplificando la creazione di immagini per le macchine virtuali. I pacchetti MSIX offrono inoltre un maggiore controllo sulle app a cui gli utenti possono accedere nelle proprie macchine virtuali. È anche possibile separare le app dall'immagine master e assegnarle agli utenti in un secondo momento.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>Creare un pacchetto VHD o VHDX per MSIX

I pacchetti MSIX devono trovarsi in un formato VHD o VHDX per funzionare correttamente. Ciò significa che, per iniziare, è necessario creare un pacchetto VHD o VHDX.

>[!NOTE]
>Se non è già stato fatto, assicurarsi di abilitare Hyper-V seguendo le istruzioni riportate in [installare Hyper-v in Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

Per creare un pacchetto VHD o VHDX per MSIX:

1. Aprire prima di tutto PowerShell.
2. Eseguire quindi il cmdlet seguente per creare un disco rigido virtuale:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Verificare che il disco rigido virtuale sia sufficientemente grande da poter disporre del pacchetto MSIX espanso.

3. Eseguire il cmdlet seguente per montare il disco rigido virtuale appena creato:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Eseguire quindi questo cmdlet per inizializzare il disco rigido virtuale montato:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Eseguire questo cmdlet per creare una nuova partizione per il disco rigido virtuale inizializzato:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Eseguire questo cmdlet per formattare la partizione:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Infine, creare una cartella padre nel disco rigido virtuale montato. Questo passaggio è necessario perché il pacchetto MSIX deve avere una cartella padre per il corretto funzionamento. Non è importante il nome della cartella padre, purché esista la cartella padre.

## <a name="expand-msix"></a>Espandere MSIX

Successivamente, sarà necessario espandere l'immagine MSIX "decomprimendo" i relativi file nel disco rigido virtuale.

Per espandere l'immagine MSIX:

1. [Scaricare lo strumento msixmgr](https://aka.ms/msixmgr) e salvare la cartella .zip in una cartella all'interno di una macchina virtuale dell'host di sessione.

2. Decomprimere la cartella .zip dello strumento msixmgr.

3. Inserire il pacchetto MSIX di origine nella stessa cartella in cui è stato decompresso lo strumento msixmgr.

4. Aprire un prompt dei comandi come amministratore e passare alla cartella in cui è stato scaricato e decompresso lo strumento msixmgr.

5. Eseguire il cmdlet seguente per decomprimere il MSIX nel disco rigido virtuale creato nella sezione precedente.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Il messaggio seguente dovrebbe essere visualizzato al termine dell'operazione di decompressione:

    > Gli ACL sono stati decompressi e applicati per il pacchetto: <package name> . msix

    >[!NOTE]
    > Se si usano pacchetti di Microsoft Store for business o Education nella rete o nei dispositivi non connessi a Internet, è necessario scaricare e installare le licenze dei pacchetti dalla Microsoft Store per eseguire le app. Per ottenere le licenze, vedere [usare i pacchetti offline](app-attach.md#use-packages-offline).

6. Passare al disco rigido virtuale montato e aprire la cartella dell'app per verificare che il contenuto del pacchetto sia presente.

7. Smontare il disco rigido virtuale.

## <a name="upload-msix-image-to-share"></a>Caricare l'immagine MSIX da condividere

Dopo aver creato il pacchetto MSIX, è necessario caricare il file VHD, VHDX o CIM risultante in una condivisione in cui le macchine virtuali degli utenti possono accedervi.

## <a name="next-steps"></a>Passaggi successivi

Porre le domande della community su questa funzionalità nel [desktop virtuale di Windows TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

È anche possibile lasciare feedback per Desktop virtuale Windows nell'[hub di commenti e suggerimenti per Desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Di seguito sono riportati alcuni altri articoli che possono risultare utili:

- [Glossario per il fissaggio dell'app MSIX](app-attach-glossary.md)
- [Domande frequenti sull'aggiunta di app MSIX](app-attach-faq.md)