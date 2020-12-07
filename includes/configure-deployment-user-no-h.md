---
title: File di inclusione
description: File di inclusione
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/14/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 4ceae4e7e2d10c80a929a4a822c877da8d8478f0
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96748223"
---
FTP e l'istanza Git locale possono essere usati per la distribuzione in un'app Web tramite un *utente della distribuzione*. Dopo aver configurato l'utente della distribuzione, è possibile usarlo per tutte le distribuzioni di Azure. Il nome utente e la password della distribuzione a livello di account sono diversi dalle credenziali della sottoscrizione di Azure. 

Per configurare l'utente della distribuzione, eseguire il comando [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) in Azure Cloud Shell. Sostituire \<username> e \<password> con il nome utente e la password di un utente della distribuzione. 

- Il nome utente deve essere univoco in Azure e per i push Git locali non deve contenere il simbolo '\@'. 
- La password deve essere composta da almeno otto caratteri, con due dei tre elementi seguenti: lettere, numeri e simboli. 

```azurecli-interactive
az webapp deployment user set --user-name <username> --password <password>
```

L'output JSON mostra la password come `null`. Se viene visualizzato un errore `'Conflict'. Details: 409`, modificare il nome utente. Se viene visualizzato un errore `'Bad Request'. Details: 400`, usare una password più complessa. 

Registrare il nome utente e la password da usare per distribuire le app Web.
