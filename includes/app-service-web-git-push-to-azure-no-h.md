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
ms.openlocfilehash: 08f4e76869f124d946566f64da394c895d0af308
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102205991"
---
Nella finestra del terminale locale aggiungere un'istanza remota di Azure al repository Git locale. Sostituire *\<deploymentLocalGitUrl-from-create-step>* con l'URL dell'istanza remota di Git salvata in [Creare un'app Web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Effettuare il push all'istanza remota di Azure per distribuire l'app con il comando seguente. Quando Git Credential Manager richiede le credenziali, assicurarsi di immettere quelle create in **Configurare un utente della distribuzione**, non quelle usate per accedere al portale di Azure.

```bash
git push azure main
```

L'esecuzione del comando può richiedere alcuni minuti. Durante l'esecuzione, il comando visualizza informazioni simili all'esempio seguente:
