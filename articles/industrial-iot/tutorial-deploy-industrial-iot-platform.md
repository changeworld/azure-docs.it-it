---
title: Distribuire la piattaforma Azure Industrial Internet
description: In questa esercitazione si apprenderà come distribuire la piattaforma IIoT.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 87a7295c785c08fcf3faffc20d34ceef45144848
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787871"
---
# <a name="tutorial-deploy-the-azure-industrial-iot-platform"></a>Esercitazione: distribuire la piattaforma Azure Industrial Internet

In questa esercitazione si apprenderà:

> [!div class="checklist"]
> * Informazioni sui componenti principali della piattaforma IIoT
> * Informazioni sui diversi tipi di installazione
> * Come distribuire la piattaforma Internet delle cose

## <a name="prerequisites"></a>Prerequisiti

- È necessario creare una sottoscrizione di Azure
- Scaricare [git](https://git-scm.com/downloads)
- Per le registrazioni dell'app Azure Active Directory (AAD) usate per l'autenticazione, è necessario disporre dei diritti di amministratore globale, dell'amministratore dell'applicazione o dell'applicazione cloud per fornire il consenso dell'amministratore a livello di tenant (vedere di seguito per altre opzioni).
- I sistemi operativi supportati per la distribuzione sono Windows, Linux e Mac
- IoT Edge supporta Windows 10 LTSC Enterprise e Ubuntu Linux 16.08/18.04 LTS Linux

## <a name="main-components"></a>Componenti principali

- Dipendenze minime: Hub delle cose, Cosmos DB, bus di servizio, Hub eventi, Key Vault, archiviazione
- Dipendenze standard: servizio minimo + SignalR, registrazioni di app AAD, servizio Device provisioning, Time Series Insights, cartella di lavoro, Log Analytics, Application Insights
- Microservizi: piano di servizio app, servizio app
- Interfaccia utente (app Web): piano di servizio app (condiviso con microservizi), servizio app
- Simulazione: macchina virtuale, rete virtuale, IoT Edge
- Servizio Azure Kubernetes

## <a name="installation-types"></a>Tipi di installazione

- Minimo: dipendenze minime
- Locale: dipendenze minime e standard
- Servizi: locale e microservizi
- Simulazione: dipendenze minime e componenti di simulazione
- App: Servizi e interfaccia utente
- All (impostazione predefinita): app e simulazione

## <a name="deployment"></a>Distribuzione

1. Per iniziare la distribuzione della piattaforma IIoT, clonare il repository dal prompt dei comandi o dal terminale.

    Industrial-IoT CD di git clone https://github.com/Azure/Industrial-IoT

2. Avviare la distribuzione guidata. lo script raccoglierà le informazioni necessarie, ad esempio l'account Azure, la sottoscrizione, la risorsa di destinazione e il nome del gruppo e dell'applicazione.

In Windows:
    ```
    .\deploy
    ```

In Linux o Mac:
    ```
    ./deploy.sh
    ```

3. I microservizi e l'interfaccia utente sono applicazioni Web che richiedono l'autenticazione, per cui sono necessarie tre registrazioni per l'app in AAD. Se i diritti richiesti sono mancanti, esistono due possibili soluzioni:

- Chiedere all'amministratore di AAD di concedere il consenso dell'amministratore a livello di tenant per l'applicazione
- Un amministratore di AAD può creare le applicazioni di AAD. La cartella Deploy/scripts contiene lo script AAD-register.ps1 per eseguire la registrazione di AAD separatamente dalla distribuzione. L'output dello script è un file che contiene le informazioni rilevanti da usare come parte della distribuzione e deve essere passato allo script deploy.ps1 nella stessa cartella usando l'argomento-aadConfig.
    ```bash
    cd deploy/scripts
    ./aad-register.ps1 -Name <application-name> -Output aad.json
    ./deploy.ps1 -aadConfig aad.json
    ```

Per le distribuzioni di produzione che richiedono la gestione temporanea, il rollback, il ridimensionamento e la resilienza, la piattaforma può essere distribuita in [Azure Kubernetes Service (AKS)](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)

Riferimenti:
- [Distribuzione della piattaforma Azure Industrial Internet](https://github.com/Azure/Industrial-IoT/tree/master/docs/deploy)
- [Come distribuire all-in-One](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-all-in-one.md)
- [Come distribuire la piattaforma in AKS](https://github.com/Azure/Industrial-IoT/blob/master/docs/deploy/howto-deploy-aks.md)


## <a name="next-steps"></a>Passaggi successivi
Ora che la piattaforma IIoT è stata distribuita, è possibile apprendere come personalizzare la configurazione dei componenti:

> [!div class="nextstepaction"]
> [Personalizzare la configurazione dei componenti](tutorial-configure-industrial-iot-components.md)
