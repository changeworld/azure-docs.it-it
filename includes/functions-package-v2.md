---
title: includere file
description: includere file
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: bcc55156ca1d03614a4ff9767d6cf3f2603c06ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028247"
---
Per aggiungere il supporto nell'ambiente di sviluppo preferito, usare i metodi seguenti.

| Ambiente di sviluppo  | Tipo di applicazione      | Per aggiungere il supporto |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Libreria di classi C#      | [Installare il pacchetto NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Basato su [Core Tools](../articles/azure-functions/functions-run-local.md) | [Registrare il bundle di estensioni](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>È consigliabile installare l'[estensione Strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack). |
| Qualsiasi altro editor/IDE     | Basato su [Core Tools](../articles/azure-functions/functions-run-local.md) | [Registrare il bundle di estensioni](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Portale di Azure             | Online solo nel portale | Viene installato quando si aggiunge un'associazione<br /><br /> Per aggiornare le estensioni delle associazioni esistenti senza dover ripubblicare l'app per le funzioni, vedere [Aggiornare le estensioni](../articles/azure-functions/functions-bindings-register.md). |