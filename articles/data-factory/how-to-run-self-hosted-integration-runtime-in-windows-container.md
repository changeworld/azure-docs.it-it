---
title: Come eseguire Self-Hosted Integration Runtime nel contenitore Windows
description: Informazioni su come eseguire Self-Hosted Integration Runtime contenitore Windows.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 2423d6bd29d893f9a27749dcc2b6d2af8a12e941
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478132"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Come eseguire Self-Hosted Integration Runtime nel contenitore Windows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo illustra come eseguire le Self-Hosted Integration Runtime nel contenitore Windows.
Azure Data Factory fornisce il supporto ufficiale dei contenitori windows Self-Hosted Integration Runtime. È possibile scaricare il codice sorgente della compilazione docker e combinare il processo di compilazione ed esecuzione nella propria pipeline di recapito continuo. 

## <a name="prerequisites"></a>Prerequisiti 
- [Requisiti dei contenitori di Windows](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker versione 2.3 e successive 
- Self-Hosted Integration Runtime versione 5.2.7713.1 e successive 
## <a name="get-started"></a>Introduzione 
1.  Installare Docker e abilitare il contenitore Windows 
2.  Scaricare il codice sorgente da https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container
3.  Scaricare la versione più recente di SHIR nella cartella 'SHIR' 
4.  Aprire la cartella nella shell: 
```console
cd "yourFolderPath"
```

5.  Compilare l'immagine Docker di Windows: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Eseguire il contenitore Docker: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true -e HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY obbligatorio per questo comando. NODE_NAME, ENABLE_HA e HA_PORT facoltative. Se non si imposta il valore, il comando userà i valori predefiniti. Il valore predefinito di ENABLE_HA è false e HA_PORT è 8060.

## <a name="container-health-check"></a>Controllo integrità contenitore 
Dopo un periodo di avvio di 120 secondi, il controllo integrità verrà eseguito periodicamente ogni 30 secondi. Fornirà lo stato di integrità del runtime di IR al motore del contenitore. 

## <a name="limitations"></a>Limitazioni
Attualmente non sono supportate le funzionalità seguenti quando si eseguono Self-Hosted Integration Runtime nel contenitore Windows:
- Proxy HTTP 
- Comunicazione tra nodi crittografati con il certificato TLS/SSL 
- Generare e importare il backup 
- Servizio Daemon 
- Aggiornamento automatico 

### <a name="next-steps"></a>Passaggi successivi
- Vedere [Concetti relativi al runtime di integrazione in Azure Data Factory](./concepts-integration-runtime.md).
- Informazioni su come [creare un runtime di integrazione self-hosted nel portale di Azure](./create-self-hosted-integration-runtime.md).