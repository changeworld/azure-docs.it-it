---
title: includere file
description: includere file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: f783245b17a880a67b62aa24a3feabb9b113053b
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122256"
---
## <a name="prepare-your-repository"></a>Preparare il repository

Per ottenere le compilazioni automatizzate dal server app Azure Service Build, assicurarsi che nella radice del repository siano presenti i file corretti nel progetto.

| Runtime | File della directory radice |
|-|-|
| ASP.NET (solo Windows) | file con estensione _\*.sln_, _\*.csproj_ o _default.aspx_ |
| ASP.NET Core | file con estensione _\*.sln_ o _\*.csproj_ |
| PHP | _index.php_ |
| Ruby (solo Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ o _package.json_ con uno script di avvio |
| Python | _\*.py_, _requirements.txt_ o _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ o _iisstart.htm_ |
| Processi Web | _\<job_name>/run.\<extension>_ in _App\_Data/jobs/continuous_ per Processi Web continui o _App\_Data/jobs/triggered_ per processi Web attivati. Per altre informazioni, vedere la [documentazione dei processi Web Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funzioni | Vedere [Distribuzione continua per Funzioni di Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Per personalizzare la distribuzione, includere un file *.deployment* nella radice del repository. Per altre informazioni, vedere [Personalizzare le distribuzioni](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) e [Script di distribuzione personalizzato](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Se si sviluppa in Visual Studio, lasciare che [Visual Studio crei automaticamente un repository](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio&preserve-view=true). Il progetto è immediatamente pronto per la distribuzione tramite Git.
>

