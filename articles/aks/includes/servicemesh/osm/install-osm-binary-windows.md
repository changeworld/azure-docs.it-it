---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: pgibson
ms.openlocfilehash: 8ac70027f7483fbf0131c31a5ba3631ed1d4ff9a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080378"
---
## <a name="download-and-install-the-osm-client-binary"></a>Scaricare e installare il file binario del client OSM

In una shell basata su PowerShell in Windows, usare `Invoke-WebRequest` per scaricare la versione Istio e quindi estrarla con `Expand-Archive` come indicato di seguito:

```powershell
# Specify the OSM version that will be leveraged throughout these instructions
$OSM_VERSION="v0.8.2"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-windows-amd64.zip&quot; -OutFile &quot;osm-$OSM_VERSION.zip"
Expand-Archive -Path "osm-$OSM_VERSION.zip" -DestinationPath .
```

Il `osm` file binario client viene eseguito nel computer client e consente di gestire il controller OSM nel cluster AKS. Usare i comandi seguenti per installare il `osm` file binario del client OSM in una shell basata su PowerShell in Windows. Questi comandi copiano il `osm` file binario client in una cartella OSM, quindi li rende disponibili immediatamente (nella shell corrente) e in modo permanente (tra i riavvii della Shell) tramite il `PATH` . Non sono necessari privilegi elevati (amministratore) per eseguire questi comandi e non è necessario riavviare la Shell.

```powershell
# Copy osm.exe to C:\OSM
New-Item -ItemType Directory -Force -Path "C:\OSM"
Move-Item -Path .\windows-amd64\osm.exe -Destination "C:\OSM\"

# Add C:\OSM to PATH.
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\OSM\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\OSM\"
```
