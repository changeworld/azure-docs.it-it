---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: f99db628b08084ca750816c00a6892e877e90efc
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080371"
---
## <a name="download-and-install-the-osm-client-binary"></a>Scaricare e installare il file binario del client OSM

In una shell basata su bash in Linux o nel [sottosistema Windows per Linux][install-wsl], usare `curl` per scaricare la versione OSM e quindi estrarla con `tar` come indicato di seguito:

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-linux-amd64.tar.gz" | tar -vxzf -
```

Il `osm` file binario client viene eseguito nel computer client e consente di gestire OSM nel cluster AKS. Usare i comandi seguenti per installare il `osm` file binario del client OSM in una shell basata su bash in un [sottosistema Linux o Windows per Linux][install-wsl]. Questi comandi copiano il file binario del client `osm` nel percorso del programma dell'utente standard in `PATH`.

```bash
sudo mv ./linux-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

È possibile verificare che la `osm` libreria client sia stata aggiunta correttamente al percorso e il numero di versione con il comando seguente.

```
osm version
```

<!-- LINKS - external -->

[install-wsl]: /windows/wsl/install-win10
