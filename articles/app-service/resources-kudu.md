---
title: Cenni preliminari sul servizio Kudu
description: Informazioni sul motore che alimenta la distribuzione continua nel servizio app e sulle relative funzionalità.
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609630"
---
# <a name="kudu-service-overview"></a>Cenni preliminari sul servizio Kudu

Kudu è il motore dietro una serie di funzionalità in [app Azure servizio](overview.md) correlate alla distribuzione basata sul controllo del codice sorgente e altri metodi di distribuzione come Dropbox e OneDrive Sync. 

## <a name="access-kudu-for-your-app"></a>Accedere a Kudu per l'app
Ogni volta che si crea un'app, il servizio app crea un'app complementare che è protetta da HTTPS. Questa app Kudu è accessibile all'indirizzo:

- App non nel livello isolato: `https://<app-name>.scm.azurewebsites.net`
- App in un livello isolato (ambiente del servizio app): `https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

Per ulteriori informazioni, vedere [accesso al servizio Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).

## <a name="kudu-features"></a>Funzionalità Kudu

Kudu fornisce informazioni utili sull'app del servizio app, ad esempio:

- Impostazioni app
- Stringhe di connessione
- Variabili di ambiente
- Variabili del server
- Intestazioni HTTP

Fornisce anche altre funzionalità, ad esempio:

- Eseguire i comandi nella [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console).
- Scaricare i dump di diagnostica IIS o i log di Docker.
- Gestione dei processi e delle estensioni del sito IIS.
- Aggiungere webhook di distribuzione per Windows APS.
- Consentire l'interfaccia utente di distribuzione ZIP con `/ZipDeploy` .
- Genera [script di distribuzione personalizzati](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
- Consente l'accesso con l' [API REST](https://github.com/projectkudu/kudu/wiki/REST-API).

## <a name="more-resources"></a>Altre risorse

Kudu è un [progetto open source](https://github.com/projectkudu/kudu)e presenta la relativa documentazione in [Kudu wiki](https://github.com/projectkudu/kudu/wiki).

