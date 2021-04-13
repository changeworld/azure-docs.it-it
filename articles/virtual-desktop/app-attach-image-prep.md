---
title: Windows Virtual Desktop prepare montaggio app MSIX image - Azure
description: Come creare un'montaggio app MSIX per un pool di host di Desktop virtuale Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 443f117907381862639564dfbf9752562f4a3564
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363666"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Preparare un'immagine MSIX per Desktop virtuale Windows

montaggio app MSIX è una soluzione di applicazione a più livelli che consente di collegare dinamicamente le app da un pacchetto MSIX a una sessione utente. Il sistema di pacchetti MSIX separa le app dal sistema operativo, semplificando la creazione di immagini per le macchine virtuali. I pacchetti MSIX offrono anche un maggiore controllo sulle app a cui gli utenti possono accedere nelle proprie macchine virtuali. È anche possibile separare le app dall'immagine master e assegnarle agli utenti in un secondo momento.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>Creare un pacchetto VHD O VHDX per MSIX

I pacchetti MSIX devono essere in formato VHD o VHDX per funzionare correttamente. Questo significa che, per iniziare, è necessario creare un disco rigido virtuale o un pacchetto VHDX.

>[!NOTE]
>Se non è già stato fatto, assicurarsi di abilitare Hyper-V seguendo le istruzioni in [Installare Hyper-V in Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

Per creare un pacchetto VHD O VHDX per MSIX:

1. Aprire prima PowerShell.
2. Eseguire quindi il cmdlet seguente per creare un disco rigido virtuale:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Assicurarsi che il disco rigido virtuale sia sufficientemente grande da contenere il pacchetto MSIX espanso.

3. Eseguire il cmdlet seguente per montare il disco rigido virtuale appena creato:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Eseguire quindi questo cmdlet per inizializzare il disco rigido virtuale montato:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Eseguire quindi questo cmdlet per creare una nuova partizione per il disco rigido virtuale inizializzato:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Eseguire questo cmdlet per formattare la partizione:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Infine, creare una cartella padre nel disco rigido virtuale montato. Questo passaggio è necessario perché il pacchetto MSIX deve avere una cartella padre per funzionare correttamente. Non è importante quale sia il nome della cartella padre, purché la cartella padre esista.

## <a name="expand-msix"></a>Espandere MSIX

Successivamente, sarà necessario espandere l'immagine MSIX "decomprimendo" i file nel disco rigido virtuale.

Per espandere l'immagine MSIX:

1. [Scaricare lo strumento msixmgr](https://aka.ms/msixmgr) e salvare la cartella .zip in una cartella all'interno di una macchina virtuale dell'host di sessione.

2. Decomprimere la cartella .zip dello strumento msixmgr.

3. Inserire il pacchetto MSIX di origine nella stessa cartella in cui è stato decompresso lo strumento msixmgr.

4. Aprire un prompt dei comandi come amministratore e passare alla cartella in cui è stato scaricato e decompresso lo strumento msixmgr.

5. Eseguire il cmdlet seguente per decomprimere MSIX nel disco rigido virtuale creato nella sezione precedente.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    Al termine della decompressione verrà visualizzato il messaggio seguente:

    > Decompressione e applicazione degli elenchi di controllo di accesso per il pacchetto: <package name> msix

    >[!NOTE]
    > Se si usano pacchetti da Microsoft Store per le aziende o Education nella rete o nei dispositivi non connessi a Internet, è necessario scaricare e installare le licenze dei pacchetti dal Microsoft Store per eseguire le app. Per ottenere le licenze, vedere [Usare i pacchetti offline.](app-attach.md#use-packages-offline)

6. Passare al disco rigido virtuale montato e aprire la cartella dell'app per assicurarsi che il contenuto del pacchetto sia presente.

7. Smontare il disco rigido virtuale.

## <a name="upload-msix-image-to-share"></a>Caricare l'immagine MSIX da condividere

Dopo aver creato il pacchetto MSIX, è necessario caricare il file VHD, VHDX o CIM risultante in una condivisione a cui possono accedere le macchine virtuali degli utenti.

## <a name="next-steps"></a>Passaggi successivi

Porre le domande della community su questa funzionalità nella [techCommunity di Desktop virtuale Windows.](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)

È anche possibile lasciare feedback per Desktop virtuale Windows nell'[hub di commenti e suggerimenti per Desktop virtuale Windows](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Di seguito sono riportati altri articoli che possono risultare utili:

- [montaggio app MSIX glossario](app-attach-glossary.md)
- [montaggio app MSIX domande frequenti](app-attach-faq.md)