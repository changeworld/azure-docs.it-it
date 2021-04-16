---
title: Glossario di Desktop montaggio app MSIX Windows - Azure
description: Glossario di montaggio app MSIX termini e concetti.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: c5c596735ad91f38d5ba4217135a9373d2856182
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538453"
---
# <a name="msix-app-attach-glossary"></a>montaggio app MSIX glossario

Questo articolo contiene un elenco di definizioni per i termini e i concetti chiave correlati montaggio app MSIX.

## <a name="msix-container"></a>Contenitore MSIX

Un contenitore MSIX è la posizione in cui vengono eseguite le app MSIX. Per altre informazioni, vedere [Contenitori MSIX.](/windows/msix/msix-container)

## <a name="msix-application"></a>Applicazione MSIX 

Applicazione archiviata in un oggetto . File MSIX.

## <a name="msix-package"></a>Pacchetto MSIX 

Un pacchetto MSIX è un file o un'applicazione MSIX.

## <a name="msix-share"></a>Condivisione MSIX

Una condivisione MSIX è una condivisione di rete che contiene pacchetti MSIX espansi. Le condivisioni MSIX devono supportare SMB 3 o versione successiva. Le condivisioni devono essere accessibili anche alle macchine virtuali nell'account di sistema del pool di host. I pacchetti MSIX vengono preparati dalla condivisione MSIX senza dover spostare i file dell'applicazione nell'unità di sistema. 

## <a name="msix-image"></a>Immagine MSIX

Un'immagine MSIX è un file VHD, VHDx o CIM che contiene una o più applicazioni in pacchetto MSIX. Ogni applicazione viene recapitata nell'immagine MSIX usando lo strumento MSIXMGR.

## <a name="repackage"></a>Riconfezionare

Il repackaging accetta un'applicazione non MSIX e la converte in MSIX usando MSIX Packaging Tool (MPT). Per altre informazioni, vedere Panoramica [di MSIX Packaging Tool.](/windows/msix/packaging-tool/tool-overview)

## <a name="expand-an-msix-package"></a>Espandere un pacchetto MSIX

L'espansione di un pacchetto MSIX è un processo in più passaggi. L'espansione accetta il file MSIX e ne inserisce il contenuto in un file VHD(x) o CIM. 

Per espandere un pacchetto MSIX:

1. Ottenere un pacchetto MSIX (file MSIX).
2. Rinominare il file MSIX in un file ZIP.
3. Decomprimere il file zip risultante in una cartella.
4. Creare un disco rigido virtuale con le stesse dimensioni della cartella.
5. Montare il disco rigido virtuale.
6. Inizializzare un disco.
7. Creare una partizione.
8. Formattare la partizione.
9. Copiare il contenuto decompresso nel disco rigido virtuale.
10. Usare lo strumento MSIXMGR per applicare gli ACL al contenuto del disco rigido virtuale.
11. Smontare il disco rigido virtuale(x) o [CIM.](#cim)

## <a name="upload-an-msix-package"></a>Caricare un pacchetto MSIX 

Il caricamento di un pacchetto MSIX comporta il caricamento del disco rigido virtuale (x) o [CIM](#cim) che contiene un pacchetto MSIX espanso nella condivisione MSIX.

In Desktop virtuale Windows i caricamenti vengono eseguono una sola volta per ogni condivisione MSIX. Dopo aver caricato un pacchetto, tutti i pool host nella stessa sottoscrizione possono fare riferimento a esso.

## <a name="add-an-msix-package"></a>Aggiungere un pacchetto MSIX

In Desktop virtuale Windows, l'aggiunta di un pacchetto MSIX lo collega a un pool di host.

## <a name="publish-an-msix-package"></a>Pubblicare un pacchetto MSIX 

In Desktop virtuale Windows un pacchetto MSIX pubblicato deve essere assegnato a un utente o a un gruppo di utenti Dominio di Active Directory Service (AD DS) o Azure Active Directory (Azure AD).

## <a name="staging"></a>Staging

La gestione temporanea comporta due aspetti:

- Montaggio del disco rigido virtuale (x) [o CIM](#cim) nella macchina virtuale.
- Notifica al sistema operativo che il pacchetto MSIX è disponibile per la registrazione.

## <a name="registration"></a>Registrazione

La registrazione rende disponibile un pacchetto MSIX a fasi per gli utenti. La registrazione è per utente. Se non è stata registrata in modo esplicito un'app per l'utente specifico, non sarà possibile eseguirlo.

Esistono due tipi di registrazione: normale e ritardata.

### <a name="regular-registration"></a>Registrazione regolare

Nella registrazione regolare, ogni applicazione assegnata a un utente è completamente registrata. La registrazione avviene durante il tempo in cui l'utente accede alla sessione, il che potrebbe influire sul tempo necessario per avviare l'uso di Desktop virtuale Windows.

### <a name="delayed-registration"></a>Registrazione posticipata

Nella registrazione ritardata, ogni applicazione assegnata all'utente è registrata solo parzialmente. La registrazione parziale indica che il riquadro menu Start e le associazioni di file con doppio clic vengono registrate. La registrazione avviene mentre l'utente accede alla sessione, quindi ha un impatto minimo sul tempo necessario per iniziare a usare Desktop virtuale Windows. La registrazione viene completata solo quando l'utente esegue l'applicazione nel pacchetto MSIX.

La registrazione ritardata è attualmente la configurazione predefinita per montaggio app MSIX.

## <a name="deregistration"></a>Annullamento della registrazione

L'annullamento della registrazione rimuove un pacchetto MSIX registrato ma non in esecuzione per un utente. La annullamento della registrazione viene eseguita mentre l'utente si esce dalla sessione. Durante la deserializzazione, montaggio app MSIX push dei dati dell'applicazione specifici per l'utente nel profilo utente locale.

## <a name="destage"></a>Rimozione gestione temporanea

La destaging notifica al sistema operativo che è possibile smontare un pacchetto MSIX o un'applicazione che attualmente non è in esecuzione e non è in esecuzione per nessun utente. In questo modo vengono rimossi tutti i riferimenti a esso nel sistema operativo.

## <a name="cim"></a>Cim

. CIM è una nuova estensione di file associata a Composite Image Files System (CimFS). Il montaggio e lo smontaggio dei file CIM sono più veloci rispetto ai file VHD. CIM utilizza anche meno CPU e memoria rispetto al disco rigido virtuale.

Un file CIM è un file con . Estensione CIM che contiene metadati e almeno sei file aggiuntivi che contengono dati effettivi. I file all'interno del file CIM non hanno estensioni. La tabella seguente è un elenco di file di esempio che si trovano all'interno di un CIM:

| Nome file | Estensione | Dimensione |
|-----------|-----------|------|
| Vsc | Cim | 1 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | N/D | 27 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | N/D | 20 KB |
| objectid_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | N/D | 42 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_0 | N/D | 428 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_1 | N/D | 217 KB |
| region_b5742e0b-1b98-40b3-94a6-9cb96f497e56_2 | N/D | 264.132 KB |

La tabella seguente è un confronto delle prestazioni tra VHD e CimFS. Questi numeri sono il risultato di un'esecuzione di test con cinquecento file da 300 MB in ogni formato eseguiti in un computer DSv4.

|  Specifiche                          | VHD                     | CimFS   |
|---------------------------------|--------------------------|-----------|
| Tempo medio di montaggio     | 356 ms                     | 255 ms      |
| Tempo medio di smontare   | 1615 ms                    | 36 ms       |
| Memory consumption | 6% (di 8 GB)                      | 2% (di 8 GB)       |
| CPU (picco di conteggio)          | Maxed out multiple times (Maxed out più volte) | Nessun impatto |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul montaggio app MSIX, vedere la panoramica e le [domande](what-is-app-attach.md) [frequenti.](app-attach-faq.md) In caso contrario, iniziare [con Configurare montaggio app](app-attach.md).
