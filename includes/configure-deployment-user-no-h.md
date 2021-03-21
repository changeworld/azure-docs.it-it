---
title: includere file
description: includere file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: a972b4738ce5646a1ee9eed6495bdc43a40826fd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102219038"
---
FTP e l'istanza Git locale possono essere usati per la distribuzione in un'app Web tramite un *utente della distribuzione*. Dopo aver configurato l'utente della distribuzione, è possibile usarlo per tutte le distribuzioni di Azure. Il nome utente e la password della distribuzione a livello di account sono diversi dalle credenziali della sottoscrizione di Azure. 

Per configurare l'utente della distribuzione, eseguire il comando [az webapp deployment user set](/cli/azure/webapp/deployment/user#az-webapp-deployment-user-set) in Azure Cloud Shell. Sostituire \<username> e \<password> con il nome utente e la password di un utente della distribuzione. 

- Il nome utente deve essere univoco in Azure e per i push Git locali non deve contenere il simbolo '\@'. 
- La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

L'output JSON mostra la password come `null`. Se viene visualizzato un errore `'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore `'Bad Request'. Details: 400`, usare una password più complessa. 

Registrare il nome utente e la password da usare per distribuire le app Web.
