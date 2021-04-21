---
title: Prerequisiti dell'interfaccia della riga di comando di Azure Cache HPC di Azure
description: Procedura di configurazione prima di poter usare l'interfaccia della riga di comando di Azure per creare o modificare un Cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 30621eceefd69cd3e08de137bb34f1079a17a406
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780483"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Configurare l'interfaccia della riga di comando di Azure per Cache HPC di Azure

Seguire questa procedura per preparare l'ambiente prima di usare l'interfaccia della riga di comando di Azure per creare o gestire un Cache HPC di Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Cache HPC di Azure richiede la versione 2.7 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="set-default-resource-group-optional"></a>Impostare il gruppo di risorse predefinito (facoltativo)

La maggior parte dei comandi hpc-cache richiede il passaggio del gruppo di risorse della cache. È possibile impostare il gruppo di risorse predefinito usando [az configure](/cli/azure/reference-index#az_configure).

## <a name="next-steps"></a>Passaggi successivi

Dopo l'installazione dell'estensione dell'interfaccia della riga di comando di Azure e l'accesso, è possibile usare l'interfaccia della riga di comando di Azure per creare e gestire Cache HPC di Azure virtuali.

* [Creare una cache HPC di Azure](hpc-cache-create.md)
* [Documentazione di hpc-cache dell'interfaccia della riga di comando di Azure](/cli/azure/ext/hpc-cache/hpc-cache)
