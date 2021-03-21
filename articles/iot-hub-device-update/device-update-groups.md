---
title: Informazioni sull'aggiornamento dei dispositivi per i gruppi di dispositivi dell'hub Azure Microsoft Docs
description: Informazioni sul modo in cui vengono usati i gruppi di dispositivi.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 18388f067ccb5b8a8876aeae685664694c207613
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679499"
---
# <a name="device-groups"></a>Gruppi di dispositivi

Un gruppo di dispositivi è una raccolta di dispositivi. I gruppi di dispositivi consentono di ridimensionare le distribuzioni a molti dispositivi. Ogni dispositivo appartiene esattamente a un gruppo di dispositivi alla volta.
È possibile scegliere di creare più gruppi di dispositivi per organizzare i dispositivi. Contoso, ad esempio, potrebbe usare il gruppo di dispositivi "flighting" per i dispositivi nel proprio laboratorio di test e il gruppo di dispositivi "Evaluation" per i dispositivi usati dal team di campo in Operations Center. Contoso può inoltre scegliere di raggruppare i dispositivi di produzione in base alle aree geografiche, in modo che possano aggiornare i dispositivi in base a una pianificazione che si allinea ai fusi orari regionali. 


## <a name="using-device-twin-tag-for-device-group-creation"></a>Uso del tag del dispositivo gemello per la creazione del gruppo di dispositivi

I tag del dispositivo gemello consentono agli utenti di raggruppare i dispositivi. I dispositivi devono avere una chiave ADUGroup e un valore nel dispositivo gemello per consentirne il raggruppamento.

### <a name="device-twin-tag-format"></a>Formato tag del dispositivo gemello

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>Gruppo di dispositivi senza categoria

Senza categoria è una parola riservata che viene usata per raggruppare i dispositivi:
- Non avere il tag del dispositivo gemello ADUGroup.
- Avere un tag del dispositivo gemello ADUGroup ma un gruppo non viene creato con questo nome di gruppo.

Si consideri ad esempio i dispositivi con i tag del dispositivo gemelli seguenti:

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

Di seguito sono riportati i dispositivi e i possibili gruppi che possono essere creati.

|Dispositivo |Group  |
|-----------|--------------|
|Device1    |Group1|
|Dispositivo2)    |Group1|
|3    |Group2|
|4    |Senza categoria|



## <a name="next-steps"></a>Passaggi successivi

[Creare un gruppo di dispositivi](./create-update-group.md)
