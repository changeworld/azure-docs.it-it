---
title: includere file
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 8539696f4521a1b4a2f56fe7d2936b45dec26ec9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "102205991"
---
Nella finestra del terminale locale aggiungere un'istanza remota di Azure al repository Git locale. Sostituire *\<deploymentLocalGitUrl-from-create-step>* con l'URL dell'istanza remota di Git salvata in [Creare un'app Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando Git Credential Manager richiede le credenziali, assicurarsi di immettere quelle create in **Configurare un utente della distribuzione**, non quelle usate per accedere al portale di Azure.

```bash
git push azure master
```

L'esecuzione del comando può richiedere alcuni minuti. Durante l'esecuzione, il comando visualizza informazioni simili all'esempio seguente:
