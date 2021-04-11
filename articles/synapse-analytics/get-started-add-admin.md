---
title: 'Esercitazione: iniziare ad aggiungere un amministratore'
description: In questa esercitazione si apprenderà come aggiungere un altro utente amministratore all'area di lavoro.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.openlocfilehash: 8b854dfcff7dfb4d03038b542308b6f3ebb6d491
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553499"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Aggiungere un amministratore all'area di lavoro sinapsi

In questa esercitazione si apprenderà come aggiungere un amministratore all'area di lavoro sinapsi. Questo utente avrà il controllo completo sull'area di lavoro.

## <a name="overview"></a>Panoramica

Fino a questo punto, nella Guida introduttiva, abbiamo dedicato le *attività svolte* nell'area di lavoro. Poiché l'area di lavoro è stata creata nel passaggio 1, l'utente è un amministratore dell'area di lavoro sinapsi. A questo punto, verrà creato un altro utente Ryan ( `ryan@contoso.com` ) come amministratore. Al termine, Ryan sarà in grado di eseguire tutte le operazioni che è possibile eseguire nell'area di lavoro.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC: ruolo proprietario per l'area di lavoro

Assegnare a `ryan@contoso.com` al ruolo di **proprietario** RBAC di Azure nell'area di lavoro.

1. Aprire il portale di Azure e aprire l'area di lavoro sinapsi.
1. Sul lato sinistro selezionare controllo di **accesso (IAM)**.
1. Aggiungere `ryan@contoso.com` al ruolo **proprietario** . 
1. Fare clic su **Salva**.
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Sinapsi RBAC: ruolo di amministratore della sinapsi per l'area di lavoro

Assegnare a `ryan@contoso.com` al ruolo di **amministratore sinapsi** RBAC RBAC nell'area di lavoro.

1. Aprire l'area di lavoro in sinapsi Studio.
1. Sul lato sinistro fare clic su **Gestisci** per aprire l'hub Gestisci.
1. In **sicurezza** fare clic su **controllo di accesso**.
1. Fare clic su **Aggiungi**.
1. Lasciare l' **ambito** impostato su **area di lavoro**.
1. Aggiungere `ryan@contoso.com` al ruolo di **amministratore sinapsi** . 
1. Quindi fare clic su **applica**.
 
## <a name="azure-rbac-role-assignments-on-the-primary-storage-account"></a>Controllo degli accessi in base al ruolo: assegnazioni di ruolo nell'account di archiviazione primario

Assegnare a `ryan@contoso.com` al ruolo **proprietario** nell'account di archiviazione primario dell'area di lavoro.
Assegnare a `ryan@contoso.com` BLOB del servizio di archiviazione di Azure ruolo di **collaboratore dati** nell'account di archiviazione primario dell'area di lavoro.

1. Aprire l'account di archiviazione primario dell'area di lavoro nella portale di Azure.
1. Sul lato sinistro fare clic su **controllo di accesso (IAM)**.
1. Aggiungere `ryan@contoso.com` al ruolo **proprietario** . 
1. Aggiungi `ryan@contoso.com` al ruolo **BLOB del servizio di archiviazione di Azure collaboratore dati**

## <a name="dedicated-sql-pools-db_owner-role"></a>Pool SQL dedicati: ruolo db_owner

Assegnare `ryan@contoso.com` al **db_owner** in ogni pool SQL dedicato nell'area di lavoro.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Synapse Analytics](get-started.md)
* [Creare un'area di lavoro](quickstart-create-workspace.md)
* [Usare i pool SQL serverless](quickstart-sql-on-demand.md)
