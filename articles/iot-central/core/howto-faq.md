---
title: Domande frequenti su Azure IoT Central | Microsoft Docs
description: Domande frequenti e risposte su Azure IoT Central domande frequenti
author: dominicbetts
ms.author: dobett
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: acabaf843f8acfe7bc0b5e9456dee09bde74bef7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796041"
---
# <a name="frequently-asked-questions-for-iot-central"></a>Domande frequenti per IoT Central

**Ricerca per categorie verificare la presenza di problemi relativi alle credenziali se un dispositivo non si connette all'applicazione IoT Central?**

La [risoluzione dei problemi relativi ai motivi per cui i dati dei dispositivi non vengono visualizzati in Azure IOT Central](troubleshoot-connection.md) include i passaggi per diagnosticare i problemi di connettività per i dispositivi.

**Ricerca per categorie file di un ticket con supporto tecnico?**

Per richiedere assistenza, è possibile archiviare un [ticket di supporto di Azure](https://portal.azure.com/#create/Microsoft.Support).

Per altre informazioni, incluse altre opzioni di supporto, vedere [supporto di Azure e opzioni di guida](../../iot-fundamentals/iot-support-help.md).

**Ricerca per categorie sbloccare un dispositivo?**

Quando un dispositivo è bloccato, non può inviare dati all'applicazione IoT Central. I dispositivi bloccati hanno lo stato **bloccato** nella pagina **dispositivi** nell'applicazione. Un operatore deve sbloccare il dispositivo prima di poter riprendere l'invio dei dati:

:::image type="content" source="media/howto-faq/blocked.png" alt-text="Screenshot che mostra il dispositivo bloccato":::

Quando un operatore sblocca un dispositivo, lo stato torna al valore precedente: **Registrato** o **Provisioning**.

**Ricerca per categorie approva un dispositivo?**

Se lo stato del dispositivo è **in attesa di approvazione** nella pagina **dispositivi** , significa che l'opzione **approvazione automatica** è disabilitata:

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="Screenshot che mostra l'applicazione con l'opzione di approvazione automatica disabilitata.":::

Un operatore deve approvare esplicitamente un dispositivo prima di iniziare a inviare dati. I dispositivi non registrati manualmente nella pagina **Dispositivi**, ma connessi con credenziali valide, avranno lo stato del dispositivo **In attesa di approvazione**. Gli operatori possono approvare questi dispositivi dalla pagina **dispositivi** usando il pulsante **approva** :

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="Screenshot che illustra come approvare un dispositivo":::

**Ricerca per categorie associare un dispositivo a un modello di dispositivo?**

Se lo stato del dispositivo è **Non associato**, significa che il dispositivo che si connette a IoT Central non ha un modello di dispositivo associato. Questa situazione si verifica in genere negli scenari seguenti:

- Viene aggiunto un set di dispositivi usando **Importa** nella pagina **Dispositivi** senza specificare il modello di dispositivo.
- Un dispositivo è stato registrato manualmente nella pagina **Dispositivi** senza specificare il modello di dispositivo. Il dispositivo è quindi connesso con credenziali valide.  

L'operatore può associare un dispositivo a un modello di dispositivo dalla pagina **dispositivi** usando il pulsante **migra** . Per altre informazioni, vedere [gestire i dispositivi nell'applicazione Azure IoT Central > migrazione di dispositivi a un modello](howto-manage-devices.md).

**Dove è possibile reperire altre informazioni sull'hub Internet?**

Azure IoT Central usa l'hub IoT come gateway del cloud che permette la connettività dei dispositivi. L'hub IoT consente:

- Inserimento dati su larga scala nel cloud.
- Gestione di dispositivi.
- Connettività dei dispositivi sicura.

Per altre informazioni sull'hub IoT, vedere [Documentazione sull'hub IoT](../../iot-hub/index.yml).

**Dove è possibile reperire altre informazioni sul servizio Device provisioning (DPS)?**

Azure IoT Central USA DPS per consentire ai dispositivi di connettersi all'applicazione. Per altre informazioni sul ruolo di DPS per i dispositivi che si connettono ai IoT Central, vedere [Get connected to Azure IOT Central](concepts-get-connected.md). Per altre informazioni su DPS, vedere [provisioning di dispositivi con il servizio Device provisioning in hub Azure](../../iot-dps/about-iot-dps.md).