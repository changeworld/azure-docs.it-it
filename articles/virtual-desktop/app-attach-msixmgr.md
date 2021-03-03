---
title: Uso dello strumento MSIXMGR-Azure
description: Come usare lo strumento MSIXMGR per desktop virtuale di Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4b34fb0d3bb2d49255007b9722a0a636c1441b8c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745258"
---
# <a name="using-the-msixmgr-tool"></a>Uso dello strumento MSIXMGR

Lo strumento MSIXMGR è per espandere le applicazioni in pacchetto MSIX in immagini MSIX. Lo strumento accetta un'applicazione MSIX in pacchetto (. MSIX) e lo espande in un file VHD, VHDx o CIM. L'immagine MSIX risultante viene archiviata nell'account di archiviazione di Azure usato dalla distribuzione del desktop virtuale di Windows. In questo articolo viene illustrato come utilizzare lo strumento MSIXMGR.

>[!NOTE]
>Per garantire la compatibilità, assicurarsi che i CIMs che archiviano le immagini MSIX vengano generati nella versione del sistema operativo in esecuzione nei pool host del desktop virtuale di Windows. MSIXMGR può creare file CIM, ma è possibile usare solo i file con un pool host che esegue Windows 10 20H2.

## <a name="requirements"></a>Requisiti

Prima di poter seguire le istruzioni di questo articolo, è necessario eseguire le operazioni seguenti:

- [Scaricare lo strumento MSIXMGR](https://aka.ms/msixmgr)
- Ottenere un'applicazione MSIX in pacchetto (. File MSIX)
- Ottenere le autorizzazioni amministrative nel computer in cui verrà creata l'immagine MSIX

## <a name="create-an-msix-image"></a>Creare un'immagine MSIX

L'espansione è il processo di acquisizione di un'applicazione MSIX in pacchetto (. MSIX) e decomprimerlo in un'immagine MSIX (. VHD (x) o. File CIM).

Per espandere un file MSIX:

1. Se non è già stato fatto, [scaricare lo strumento MSIXMGR](https://aka.ms/msixmgr) .

2. Decomprimere MSIXMGR.zip in una cartella locale.

3. Aprire un prompt dei comandi in modalità con privilegi elevati.

4. Trovare la cartella locale nel passaggio 2.

5. Eseguire il comando seguente nel prompt dei comandi per creare un'immagine MSIX.

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    Ricordarsi di sostituire i valori segnaposto con i valori pertinenti. Ad esempio:

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. Ora che l'immagine è stata creata, passare alla cartella di destinazione e assicurarsi di aver creato correttamente l'immagine MSIX (. VHDX).

## <a name="create-an-msix-image-in-a-cim-file"></a>Creare un'immagine MSIX in un file CIM

È inoltre possibile utilizzare il comando nel [passaggio 5](#create-an-msix-image) per creare file CIM e VHDX sostituendo il tipo di file e il percorso di destinazione.

Ad esempio, di seguito viene illustrato come utilizzare il comando per creare un file CIM:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

Di seguito viene illustrato come usare il comando per creare un VHDX:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla connessione dell'app MSIX, vedere [che cos'è la connessione app MSIX?](what-is-app-attach.md)

Per informazioni su come configurare la connessione app, vedere questi articoli:

- [Configurare la connessione all'app MSIX con il portale di Azure](app-attach-azure-portal.md)
- [Configurare la connessione dell'app MSIX tramite PowerShell](app-attach-powershell.md)
- [Creare script di PowerShell per la connessione all'app MSIX](app-attach.md)
- [Preparare un'immagine MSIX per desktop virtuale Windows](app-attach-image-prep.md)
- [Configurare una condivisione file per la connessione all'app MSIX](app-attach-file-share.md)

Per domande sul problema di connessione delle app MSIX, vedere la pagina relativa alle [domande frequenti sull'aggiunta](app-attach-faq.md) di app e il glossario per il [fissaggio](app-attach-glossary.md)delle app.
