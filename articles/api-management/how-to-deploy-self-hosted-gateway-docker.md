---
title: Distribuire un gateway self-hosted in Docker | Microsoft Docs
description: Informazioni su come distribuire un componente gateway self-hosted di Azure API Management in Docker
services: api-management
documentationcenter: ''
author: vladvino
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/19/2021
ms.author: apimpm
ms.openlocfilehash: 531421726bc1e081d85eca9d535267520d3fea5f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725608"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Distribuire un gateway API Management azure self-hosted in Docker

Questo articolo illustra i passaggi per la distribuzione del componente gateway self-hosted di Azure API Management in un ambiente Docker.

> [!NOTE]
> L'hosting di gateway self-hosted in Docker è ideale per i casi d'uso di valutazione e sviluppo. Kubernetes è consigliato per l'uso in produzione. Vedere [questo](how-to-deploy-self-hosted-gateway-kubernetes.md) documento per informazioni su come distribuire un gateway self-hosted in Kubernetes.

## <a name="prerequisites"></a>Prerequisiti

- Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md)
- Creare un ambiente Docker. [Docker per desktop è](https://www.docker.com/products/docker-desktop) un'opzione valida per scopi di sviluppo e valutazione. Vedere [la documentazione di Docker](https://docs.docker.com) per informazioni su tutte le edizioni di Docker, le relative funzionalità e la documentazione completa su Docker stesso.
- [Effettuare il provisioning di una risorsa gateway nell'istanza API Management servizio](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Il gateway self-hosted viene in pacchetto come contenitore Docker basato su Linux x86-64.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Distribuire il gateway self-hosted in Docker

1. Selezionare **Gateway in** Distribuzione e **infrastruttura.**
2. Selezionare la risorsa gateway che si intende distribuire.
3. Selezionare **Distribuzione.**
4. Si noti che un token di accesso nella casella di  testo **Token** è stato automaticamente rigenerato usando i valori predefiniti Scadenza e **Chiave** privata. Se necessario, selezionare i valori desiderati in uno o entrambi i controlli per generare un nuovo token.
5. Assicurarsi che **Docker sia** selezionato in **Script di distribuzione**.
6. Selezionare il collegamento al file **env.conf** accanto **all'ambiente** per scaricare il file.
7. Selezionare **l'icona** di copia all'estremità destra della **casella di** testo Esegui per copiare il comando Docker negli Appunti.
8. Incollare il comando nella finestra del terminale (o comando). Modificare i mapping delle porte e il nome del contenitore in base alle esigenze. Si noti che il comando presuppone che il file di ambiente scaricato sia presente nella directory corrente.
   ```
       docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
   ```
9. Eseguire il comando. Il comando indica all'ambiente Docker di [](https://aka.ms/apim/sputnik/dhub) eseguire il contenitore usando l'immagine del contenitore scaricata dal Microsoft Container Registry e di eseguire il mapping delle porte HTTP (8080) e HTTPS (8081) del contenitore alle porte 80 e 443 nell'host.
10. Eseguire il comando seguente per verificare se il contenitore gateway è in esecuzione:
    ```console
    docker ps
    CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
    895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
    ```
10. Tornare a portale di Azure, fare clic  su Panoramica e verificare che il contenitore gateway self-hosted appena distribuito segnala uno stato integro.

    ![stato del gateway](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Usare <code>console docker container logs <gateway-name></code> il comando per visualizzare uno snapshot del log del gateway self-hosted.
>
> Usare <code>docker container logs --help</code> il comando per visualizzare tutte le opzioni di visualizzazione dei log.

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere Panoramica del gateway [self-hosted API Management Azure.](self-hosted-gateway-overview.md)
* [Configurare il nome di dominio personalizzato per il gateway self-hosted.](api-management-howto-configure-custom-domain-gateway.md)
