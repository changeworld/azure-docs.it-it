---
title: Informazioni sull'aggiornamento del dispositivo per l'importazione di hub Microsoft Docs
description: Concetti chiave per l'importazione di un nuovo aggiornamento nell'aggiornamento del dispositivo per l'hub Internet.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 4cd5e0c016b98a3dc9336237a5c1b14e6b0f5789
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040584"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>Importazione degli aggiornamenti nell'aggiornamento del dispositivo per l'hub Internet
Per distribuire un aggiornamento ai dispositivi dall'aggiornamento del dispositivo per l'hub Internet, è necessario innanzitutto _importare_ tale aggiornamento nel servizio di aggiornamento del dispositivo. Ecco una panoramica di alcuni concetti importanti da comprendere quando si tratta di importare gli aggiornamenti.

## <a name="import-manifest"></a>Importa manifesto

Un manifesto di importazione è un file JSON che definisce importanti informazioni sull'aggiornamento che si sta importando. Come parte del processo di importazione, si invieranno sia il manifesto di importazione sia il file o i file di aggiornamento associati, ad esempio un pacchetto di aggiornamento del firmware. I metadati definiti nel manifesto di importazione vengono utilizzati per inserire l'aggiornamento. Alcuni dei metadati vengono usati anche in fase di distribuzione, ad esempio per verificare se un aggiornamento è stato installato correttamente.

Il manifesto di importazione contiene diversi elementi che rappresentano un importante aggiornamento dei dispositivi per i concetti relativi all'hub. Questi concetti sono descritti di seguito.

### <a name="update-identity-update-id"></a>Aggiorna identità (ID aggiornamento)

L'identità dell'aggiornamento rappresenta il identificatore univoco di un aggiornamento. Definisce importanti proprietà relative a un aggiornamento importato. L'identità dell'aggiornamento è costituita da tre parti:
* Provider: si tratta dell'entità che sta creando o direttamente responsabile dell'aggiornamento. Si tratta spesso di un nome della società.
* Nome: identificatore per una classe di aggiornamenti. La classe può essere qualsiasi cosa scelta. Si tratta spesso di un nome di dispositivo o modello.
* Version: numero di versione che distingue questo aggiornamento da altri che hanno lo stesso provider e lo stesso nome. Questa versione viene usata dall'aggiornamento del dispositivo per il servizio hub Internet e può corrispondere o meno a una versione di un singolo componente software nel dispositivo. 

### <a name="compatibility"></a>Compatibilità

Per semplificare le distribuzioni degli aggiornamenti, l'aggiornamento del dispositivo per l'hub Internet Confronta le proprietà di compatibilità per un aggiornamento, definite nel manifesto di importazione, con le proprietà corrispondenti del dispositivo. Verranno restituiti e resi disponibili per la distribuzione solo gli aggiornamenti con proprietà corrispondenti.

### <a name="installedcriteria"></a>InstalledCriteria

InstalledCriteria viene usato dall'agente di aggiornamento in un dispositivo per determinare se un aggiornamento è stato installato correttamente.


## <a name="next-steps"></a>Passaggi successivi

Se si è pronti, provare la [Guida all'importazione How-To](./import-update.md), che illustra in modo dettagliato il processo di importazione.


