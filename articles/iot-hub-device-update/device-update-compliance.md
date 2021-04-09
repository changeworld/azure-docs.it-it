---
title: Informazioni sull'aggiornamento del dispositivo per la conformità dell'hub Azure Microsoft Docs
description: Informazioni sul modo in cui l'aggiornamento dei dispositivi per l'hub Azure è la misura della conformità degli aggiornamenti
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101663417"
---
# <a name="device-update-compliance"></a>Conformità aggiornamenti del dispositivo

Nell'aggiornamento del dispositivo per l'hub Internet, la conformità misura il numero di dispositivi che hanno installato l'aggiornamento compatibile con la versione più recente. Un dispositivo è conforme se è installato l'aggiornamento disponibile con la versione più recente compatibile. 

Si consideri, ad esempio, un'istanza dell'aggiornamento del dispositivo con i seguenti aggiornamenti:

|Nome aggiornamento|Aggiorna versione|Modello di dispositivo compatibile|
|-----------|--------------|-----------------------|
|Update1    |1.0    |Model1|
|Update2    |1.0    |Model2|
|Update3    |2.0    |Model1|

Supponiamo che siano state create le distribuzioni seguenti:

|Nome della distribuzione    |Nome aggiornamento    |Gruppo di destinazione|
|-----------|--------------|-------------------|
|Deployment1    |Update1    |Group1|
|Deployment2    |Update2    |Group2|
|Deployment3    |Update3    |Gruppo 3|

A questo punto, considerare i dispositivi seguenti, con le relative appartenenze a gruppi e le versioni installate:

|DeviceId   |Modello dispositivo   |Versione di aggiornamento installata|Group |Conformità|
|-----------|--------------|-----------------------|-----|---------|
|Device1    |Model1 |1.0    |Group1 |Nuovi aggiornamenti disponibili</span>|
|Dispositivo2)    |Model1 |2.0    |Gruppo 3 |Aggiornamento più recente|
|3    |Model2 |1.0    |Group2 |Aggiornamento più recente|
|4    |Model1 |1.0    |Gruppo 3 |Aggiornamento in corso|

Device1 e 4 non sono conformi perché la versione 1,0 è installata anche se è presente un aggiornamento della versione superiore, Update3, compatibile con il modello nell'istanza di aggiornamento del dispositivo. Dispositivo2) e 3 sono entrambi conformi perché gli aggiornamenti della versione più elevati sono compatibili per i modelli installati.

La conformità non considera se un aggiornamento viene distribuito nel gruppo di un dispositivo; esamina gli aggiornamenti pubblicati per l'aggiornamento del dispositivo. Nell'esempio precedente, anche se device1 ha installato l'aggiornamento distribuito, viene considerato non conforme. Device1 continuerà a essere considerato non conforme fino a quando non viene installato correttamente Update3. Lo stato di conformità può essere utile per identificare se sono necessarie nuove distribuzioni. 

Come illustrato in precedenza, sono disponibili tre stati di conformità nell'aggiornamento del dispositivo per l'hub Internet:

*   Nell' **Ultimo aggiornamento** : il dispositivo ha installato l'aggiornamento compatibile con la versione più recente pubblicato nell'aggiornamento del dispositivo.
*   **Aggiornamento in corso** : una distribuzione attiva è in fase di distribuzione dell'aggiornamento più recente compatibile con la versione del dispositivo.
*   **Nuovi aggiornamenti disponibili** : un dispositivo non ha ancora installato l'aggiornamento compatibile con la versione più recente e non è in una distribuzione attiva per tale aggiornamento.
