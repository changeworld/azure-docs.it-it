---
title: Domande frequenti su Desktop montaggio app MSIX Windows - Azure
description: Domande frequenti su montaggio app MSIX desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 6057f4a76f274e34b036ea352a3691b34d24b3a1
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835363"
---
# <a name="msix-app-attach-faq"></a>montaggio app MSIX domande frequenti

Questo articolo contiene le risposte alle domande frequenti montaggio app MSIX per Desktop virtuale Windows.

## <a name="whats-the-difference-between-msix-and-msix-app-attach"></a>Qual è la differenza tra MSIX e montaggio app MSIX?

MSIX è un formato di creazione pacchetti per le app, montaggio app MSIX è la funzionalità che fornisce pacchetti MSIX alla distribuzione.

## <a name="does-msix-app-attach-use-fslogix"></a>L montaggio app MSIX usa FSLogix?

montaggio app MSIX non usa FSLogix. Tuttavia, montaggio app MSIX e FSLogix sono progettati per funzionare insieme per offrire un'esperienza utente senza problemi.

## <a name="can-i-use-the-msix-app-attach-outside-of-windows-virtual-desktop"></a>È possibile usare il montaggio app MSIX all'esterno di Desktop virtuale Windows?

Le API che si montaggio app MSIX sono disponibili per l'Windows 10 Enterprise. Queste API possono essere usate all'esterno di Desktop virtuale Windows. Tuttavia, non esiste alcun piano di gestione per montaggio app MSIX all'esterno di Desktop virtuale Windows.

## <a name="how-do-i-get-an-msix-package"></a>Ricerca per categorie ottenere un pacchetto MSIX?

Il fornitore del software offrirà un pacchetto MSIX. È anche possibile convertire i pacchetti non MSIX in MSIX. Per altre [informazioni, vedere Come spostare i programmi di installazione esistenti in MSIX.](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix)

## <a name="which-operating-systems-support-msix-app-attach"></a>Quali sistemi operativi supportano montaggio app MSIX?

Windows 10 Enterprise e Windows 10 Enterprise multi-sessione, versione 2004 o successiva.

## <a name="is-msix-app-attach-currently-generally-available"></a>La montaggio app MSIX è attualmente disponibile a livello generale?

montaggio app MSIX fa parte di Windows 10 Enterprise e Windows 10 Enterprise multi-sessione, versione 2004 o successiva. Entrambi i sistemi operativi sono attualmente disponibili a livello generale. 

## <a name="can-i-use-msix-app-attach-outside-of-windows-virtual-desktop"></a>È possibile usare montaggio app MSIX al di fuori di Desktop virtuale Windows?

LE API MSIX e montaggio app MSIX sono parte di Windows 10 Enterprise e Windows 10 Enterprise multi-sessione, versione 2004 e successive. Attualmente non viene fornito software di gestione per montaggio app MSIX al di fuori di Desktop virtuale Windows.

## <a name="can-i-run-two-versions-of-the-same-application-at-the-same-time"></a>È possibile eseguire contemporaneamente due versioni della stessa applicazione?

Per l'esecuzione simultanea di due versioni delle stesse applicazioni MSIX, la famiglia di pacchetti MSIX definita nel file appxmanifest.xml deve essere diversa per ogni app.

## <a name="should-i-disable-auto-update-when-using-msix-app-attach"></a>È consigliabile disabilitare l'aggiornamento automatico quando si usa montaggio app MSIX?

Sì. montaggio app MSIX non supporta l'aggiornamento automatico per le applicazioni MSIX.

## <a name="how-do-permissions-work-with-msix-app-attach"></a>Come funzionano le autorizzazioni con montaggio app MSIX?

Tutte le macchine virtuali (VM) in un pool host che usa montaggio app MSIX devono disporre delle autorizzazioni di lettura per la condivisione file in cui sono archiviate le immagini MSIX. Se usa anche File di Azure, sarà necessario concedere sia le autorizzazioni di controllo degli accessi in base al ruolo (RBAC) che di New Technology File System (NTFS).

## <a name="can-i-use-msix-app-attach-for-http-or-https"></a>È possibile usare montaggio app MSIX per HTTP o HTTP?

L'montaggio app MSIX su HTTP o HTTP non è attualmente supportato.

## <a name="can-i-restage-the-same-msix-application"></a>È possibile ri-installare nuovamente la stessa applicazione MSIX?

Sì. È possibile ristageare le applicazioni già installate e questo non dovrebbe causare errori.

## <a name="does-msix-app-attach-support-self-signed-certificates"></a>La montaggio app MSIX supporta i certificati autofirmati?

Sì. È necessario installare il certificato autofirmato in tutte le macchine virtuali host di sessione in cui montaggio app MSIX viene usato per ospitare l'applicazione autofirmata.

## <a name="what-applications-can-i-repackage-to-msix"></a>Quali applicazioni è possibile ripacchetto in MSIX?

Ogni applicazione usa diverse funzionalità del sistema operativo, dei linguaggi di programmazione e dei framework. Per creare un nuovo pacchetto dell'applicazione, seguire le istruzioni riportate in [Come spostare i programmi di installazione esistenti in MSIX.](/windows/msix/packaging-tool/create-an-msix-overview#how-to-move-your-existing-installers-to-msix) È possibile trovare un elenco degli elementi necessari per creare un nuovo pacchetto di un'applicazione in Preparare il pacchetto di [un'applicazione desktop.](/windows/msix/desktop/desktop-to-uwp-prepare) 

Alcune applicazioni non possono essere a livelli dell'applicazione, ovvero non possono essere riassocate in un file MSIX. Ecco un elenco delle applicazioni che non possono essere riasfezionate:

- Driver 
- Active-X o Silverlight
- Client VPN
- Programmi antivirus

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui montaggio app MSIX, vedere la [](what-is-app-attach.md) panoramica e [il glossario.](app-attach-glossary.md) In caso contrario, iniziare [con Configurare montaggio app](app-attach.md).
