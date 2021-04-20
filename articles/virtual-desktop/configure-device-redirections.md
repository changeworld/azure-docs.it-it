---
title: Configurare i reindirizzamenti dei dispositivi - Azure
description: Come configurare i reindirizzamenti dei dispositivi per Desktop virtuale Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 09/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 37ecd06c4e3e71234e8fb1b6bad0cd05482dd31b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727848"
---
# <a name="configure-device-redirections"></a>Configurare i reindirizzamenti dei dispositivi

La configurazione dei reindirizzamenti dei dispositivi per l'ambiente Desktop virtuale Windows consente di usare stampanti, dispositivi USB, microfoni e altri dispositivi periferici nella sessione remota. Alcuni reindirizzamenti dei dispositivi richiedono modifiche alle proprietà Remote Desktop Protocol (RDP) e alle Criteri di gruppo predefinite.

## <a name="supported-device-redirections"></a>Reindirizzamenti dei dispositivi supportati

Ogni client supporta reindirizzamenti di dispositivi diversi. Vedere [Confrontare i client per](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare) l'elenco completo dei reindirizzamenti dei dispositivi supportati per ogni client.

## <a name="customizing-rdp-properties-for-a-host-pool"></a>Personalizzazione delle proprietà RDP per un pool di host

Per altre informazioni sulla personalizzazione delle proprietà RDP per un pool di host tramite PowerShell o portale di Azure, vedere [Proprietà RDP](customize-rdp-properties.md). Per l'elenco completo delle proprietà RDP supportate, vedere [Impostazioni del file RDP supportate](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext).

## <a name="setup-device-redirections"></a>Configurare i reindirizzamenti dei dispositivi

Per configurare i reindirizzamenti dei dispositivi, è possibile usare le Criteri di gruppo RDP seguenti.

### <a name="audio-input-microphone-redirection"></a>Reindirizzamento dell'input audio (microfono)

Impostare la proprietà RDP seguente per configurare il reindirizzamento dell'input audio:

- `audiocapturemode:i:1` abilita il reindirizzamento dell'input audio.
- `audiocapturemode:i:0` disabilita il reindirizzamento dell'input audio.

### <a name="audio-output-speaker-redirection"></a>Reindirizzamento dell'output audio (altoparlante)

Impostare la proprietà RDP seguente per configurare il reindirizzamento dell'output audio:

- `audiomode:i:0` abilita il reindirizzamento dell'output audio.
- `audiomode:i:1` o `audiomode:i:2` disabilitare il reindirizzamento dell'output audio.

### <a name="camera-redirection"></a>Reindirizzamento della fotocamera

Impostare la proprietà RDP seguente per configurare il reindirizzamento della fotocamera:

- `camerastoredirect:s:*` reindirizza tutte le fotocamere.
- `camerastoredirect:s:` disabilita il reindirizzamento della fotocamera.

>[!NOTE]
>Anche se la `camerastoredirect:s:` proprietà è disabilitata, le fotocamere locali possono essere reindirizzate tramite la `devicestoredirect:s:` proprietà . Per disabilitare completamente il set di reindirizzamento della fotocamera e impostare o definire alcuni subset di dispositivi Plug and Play che `camerastoredirect:s:` `devicestoredirect:s:` non includono alcuna fotocamera.

È anche possibile reindirizzare fotocamere specifiche usando un elenco delimitato da punto e virgola di KSCATEGORY_VIDEO_CAMERA interfacce, ad esempio `camerastoredirect:s:\?\usb#vid_0bda&pid_58b0&mi` .

### <a name="clipboard-redirection"></a>Reindirizzamento degli Appunti

Impostare la proprietà RDP seguente per configurare il reindirizzamento degli Appunti:

- `redirectclipboard:i:1` abilita il reindirizzamento degli Appunti.
- `redirectclipboard:i:0` disabilita il reindirizzamento degli Appunti.

### <a name="com-port-redirections"></a>Reindirizzamenti delle porte COM

Impostare la proprietà RDP seguente per configurare il reindirizzamento delle porte COM:

- `redirectcomports:i:1` abilita il reindirizzamento delle porte COM.
- `redirectcomports:i:0` disabilita il reindirizzamento delle porte COM.

### <a name="usb-redirection"></a>Reindirizzamento USB

Impostare prima di tutto la proprietà RDP seguente per abilitare il reindirizzamento dei dispositivi USB:

- `usbdevicestoredirect:s:*` abilita il reindirizzamento dei dispositivi USB.
- `usbdevicestoredirect:s:` disabilita il reindirizzamento dei dispositivi USB.

In secondo momento, impostare le Criteri di gruppo seguenti nel dispositivo locale dell'utente:

- Passare a **Criteri di configurazione** computer  >   >  **Modelli amministrativi** componenti di  >  **Windows**  >  **Servizi Desktop remoto**  >  **Connessione Desktop remoto Client**  >  **RemoteFX USB Device Redirection (Reindirizzamento dispositivo USB RemoteFX client).**
- Selezionare **Consente il reindirizzamento RDP di altri dispositivi USB RemoteFX supportati da questo computer.**
- Selezionare **l'opzione Abilitato** e quindi selezionare la casella Administrators and Users in RemoteFX USB Redirection Access Rights (Amministratori e utenti **in RemoteFX USB Redirection Access Rights).**
- Selezionare **OK**.

### <a name="plug-and-play-device-redirection"></a>Reindirizzamento dei dispositivi Plug and Play

Impostare la proprietà RDP seguente per configurare il reindirizzamento dei dispositivi Plug and Play:

- `devicestoredirect:s:*` abilita il reindirizzamento di tutti i dispositivi Plug and Play.
- `devicestoredirect:s:` disabilita il reindirizzamento dei dispositivi Plug and Play.

È anche possibile selezionare dispositivi Plug and Play specifici usando un elenco delimitato da punto e virgola, ad esempio `devicestoredirect:s:root\*PNP0F08` .

### <a name="local-drive-redirection"></a>Reindirizzamento unità locale

Impostare la proprietà RDP seguente per configurare il reindirizzamento delle unità locali:

- `drivestoredirect:s:*` abilita il reindirizzamento di tutte le unità disco.
- `drivestoredirect:s:` disabilita il reindirizzamento delle unità locali.

È anche possibile selezionare unità specifiche usando un elenco delimitato da punto e virgola, ad esempio `drivestoredirect:s:C:;E:;` .

Per configurare il trasferimento di file del client Web, impostare `drivestoredirect:s:*` .

### <a name="printer-redirection"></a>Reindirizzamento della stampante

Impostare la proprietà RDP seguente per configurare il reindirizzamento della stampante:

- `redirectprinters:i:1` abilita il reindirizzamento della stampante.
- `redirectprinters:i:0` disabilita il reindirizzamento della stampante.

### <a name="smart-card-redirection"></a>Reindirizzamento delle smart card

Impostare la proprietà RDP seguente per configurare smart card reindirizzamento:

- `redirectsmartcards:i:1` abilita smart card reindirizzamento.
- `redirectsmartcards:i:0` disabilita smart card reindirizzamento.
