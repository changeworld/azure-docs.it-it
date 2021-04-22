---
title: Prerequisiti dell'interfaccia della riga di comando di Azure per Cache HPC di Azure
description: Procedura di configurazione prima di poter usare l'interfaccia della riga di comando di Azure per creare o modificare un Cache HPC di Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2020
ms.author: v-erkel
ms.openlocfilehash: 0b8e1158bc60c4cceea508db988000fe952a90a4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864290"
---
# <a name="set-up-azure-cli-for-azure-hpc-cache"></a>Configurare l'interfaccia della riga di comando di Azure per Cache HPC di Azure

Seguire questa procedura per preparare l'ambiente prima di usare l'interfaccia della riga di comando di Azure per creare o gestire un Cache HPC di Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - Cache HPC di Azure richiede la versione 2.7 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="set-default-resource-group-optional"></a>Impostare il gruppo di risorse predefinito (facoltativo)

La maggior parte dei comandi hpc-cache richiede di passare il gruppo di risorse della cache. È possibile impostare il gruppo di risorse predefinito usando [az configure](/cli/azure/reference-index#az_configure).

## <a name="next-steps"></a>Passaggi successivi

Dopo l'installazione dell'estensione dell'interfaccia della riga di comando di Azure e l'accesso, è possibile usare l'interfaccia della riga di comando di Azure per creare e gestire Cache HPC di Azure sistema.

* [Creare una cache HPC di Azure](hpc-cache-create.md)
* [Documentazione dell'interfaccia della riga di comando di Azure hpc-cache](/cli/azure/hpc-cache)
