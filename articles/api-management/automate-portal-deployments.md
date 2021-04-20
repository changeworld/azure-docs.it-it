---
title: Automatizzare le distribuzioni del portale per sviluppatori
titleSuffix: Azure API Management
description: Informazioni su come eseguire automaticamente la migrazione del contenuto del portale per sviluppatori self-hosted tra due API Management servizi.
author: erikadoyle
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: e189a9339f6ca3bc81148b86206ddd052392074f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741740"
---
# <a name="automate-developer-portal-deployments"></a>Automatizzare le distribuzioni del portale per sviluppatori

Il API Management per sviluppatori supporta l'accesso a livello di codice al contenuto. Consente di importare o esportare dati da un servizio API Management tramite [l'API REST di gestione del contenuto](/rest/api/apimanagement/). L'accesso all'API REST funziona sia per i portali gestiti che per i portali self-hosted.

## <a name="automated-migration-script"></a>Script di migrazione automatizzato

È possibile usare l'API per automatizzare la migrazione del contenuto tra due servizi API Management, ad esempio un servizio nell'ambiente di test e un servizio nell'ambiente di produzione. Lo script nel repository GitHub API Management portale per sviluppatori semplifica `scripts.v3/migrate.js` questo processo di automazione. [](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v3/migrate.js)

> [!WARNING]
> Lo script rimuove il contenuto del portale per sviluppatori nel servizio di API Management destinazione. In caso di preoccupazione, assicurarsi di eseguire un backup.

> [!NOTE]
> Se si usa un portale self-hosted con un account di archiviazione personalizzato definito in modo esplicito per ospitare file multimediali( ad esempio, si definisce l'impostazione nel file di configurazione), è necessario usare lo `blobStorageUrl` `config.design.json` script `scripts/migrate.js` [originale](https://github.com/Azure/api-management-developer-portal/blob/master/scripts.v2/migrate.js). Lo script originale non funziona per i portali gestiti o self-hosted con l'account di archiviazione multimediale gestito da API Management. In tal caso, usare invece lo script `/scripts.v3` dalla cartella .

Lo script esegue le operazioni seguenti:

1. Acquisire il contenuto e i file multimediali del portale dal servizio di API Management origine.
1. Rimuovere il contenuto del portale e i supporti dal servizio di API Management destinazione.
1. Caricare il contenuto del portale e i supporti nel servizio di API Management destinazione.
1. Facoltativamente e solo per i portali gestiti, pubblicare automaticamente il portale.

Al termine dell'esecuzione dello script, il servizio API Management di destinazione deve contenere lo stesso contenuto del portale del servizio di origine e sarà possibile vederlo come amministratore.

* Se si usa un portale gestito, è possibile impostare lo script per pubblicare automaticamente il portale di destinazione per rendere la versione migrata automaticamente disponibile ai visitatori. 
* Se si usa un portale self-hosted, è necessario pubblicare manualmente il portale di destinazione. Seguire le istruzioni di pubblicazione e hosting nell'esercitazione [per configurare un portale per sviluppatori self-hosted.](developer-portal-self-host.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul portale per sviluppatori:

- [Panoramica del portale per sviluppatori di Gestione API](api-management-howto-developer-portal.md)
- [Ospitare in modo self-host il portale per sviluppatori](developer-portal-self-host.md)