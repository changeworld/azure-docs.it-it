---
title: includere file
description: includere file
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 404fd10c3e3610671d2b6e5dbc6aba8bcaa70046
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100018983"
---
## <a name="push-image-to-registry"></a>Eseguire il push dell'immagine nel registro

Per eseguire il push di un'immagine in Registro Azure Container è necessario innanzitutto disporre di un'immagine. Se non sono ancora presenti immagini del contenitore locale, eseguire il comando seguente [Docker pull][docker-pull] per eseguire il pull di un'immagine pubblica esistente. Per questo esempio, estrarre l' `hello-world` immagine da Microsoft container Registry.

```
docker pull mcr.microsoft.com/hello-world
```

Prima di poter eseguire il push di un'immagine nel registro, è necessario contrassegnarla con il nome completo del server di accesso al registro. Il nome del server di accesso è in formato *\<registry-name\>.azurecr.io* (interamente in minuscolo), ad esempio *mycontainerregistry.azurecr.io*.

Contrassegnare l'immagine usando il comando [docker tag][docker-tag]. Sostituire `<login-server>` con il nome del server di accesso dell'istanza del record di controllo di accesso.

```
docker tag mcr.microsoft.com/hello-world <login-server>/hello-world:v1
```

Esempio:

```
docker tag mcr.microsoft.com/hello-world mycontainerregistry.azurecr.io/hello-world:v1
```


Usare infine [docker push][docker-push] per eseguire il push dell'immagine nell'istanza del registro. Sostituire `<login-server>` con il nome del server di accesso dell'istanza del registro. Questo esempio crea il repository **hello-world** che contiene l'immagine `hello-world:v1`.

```
docker push <login-server>/hello-world:v1
```

Dopo il push dell'immagine nel registro contenitori, rimuovere l'immagine `hello-world:v1` dall'ambiente Docker locale. Si noti che questo comando [docker rmi][docker-rmi] non rimuove l'immagine dal repository **hello-world** nel registro contenitori di Azure.

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

