---
title: Crea l'agente Defender micro dal codice sorgente (anteprima)
titleSuffix: Azure Defender for IoT
description: Micro Agent include un'infrastruttura che può essere usata per personalizzare la distribuzione.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/18/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 782c14c6feee56c4b19d5a234cb41186d51decb2
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99810145"
---
# <a name="build-the-defender-micro-agent-from-source-code"></a>Crea l'agente Defender micro dal codice sorgente

L'agente micro include un'infrastruttura, che può essere usata per personalizzare la distribuzione. Per visualizzare un elenco dei parametri di configurazione disponibili, esaminare il `configs/LINUX_BASE.conf` file.

Per una singola distribuzione, modificare il file di base `.conf` . 

Se è necessaria più di una distribuzione, è possibile ereditare dalla configurazione di base ed eseguire l'override dei relativi valori. 

Per eseguire l'override dei valori:

1. Creare un nuovo `.dist` file.

1. Aggiungere `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)` alla parte superiore.
 
1. Definire nuovi valori per qualsiasi esigenza, ad esempio: 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. Assegnare al `.dist` file un riferimento durante la compilazione. Ad esempio, 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="baseline-configuration-signing"></a>Firma della configurazione di base 

Per impostazione predefinita, l'agente verifica l'autenticità dei file di configurazione posizionati sul disco per attenuare la manomissione.

È possibile arrestare questo processo definendo il flag del preprocessore `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE` .

Non è consigliabile disattivare il controllo della firma per gli ambienti di produzione. 

Se è necessaria una configurazione diversa per gli scenari di produzione, contattare il team Defender per l'it. 

## <a name="prerequisites"></a>Prerequisiti 

1. Rivolgersi all'account Manager per richiedere l'accesso a Defender per il codice sorgente.
 
1. Clonare o estrarre il codice sorgente in una cartella sul disco.

1. Passare a tale directory.

1. Effettuare il pull dei moduli secondari usando il codice seguente:

    ```bash
    git submodule update --init
    ```
    
1. A questo punto, effettuare il pull dei moduli secondari per Azure Internets SDK con il codice seguente: 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. Aggiungere un'autorizzazione di esecuzione ed eseguire lo script di installazione dell'ambiente di sviluppo:

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. Creare una directory per gli output di compilazione: 

    ```bash
    mkdir cmake 
    ```

1. Opzionale Scaricare e installare [VSCode](https://code.visualstudio.com/download ) 

1. Opzionale Installare l' [estensione C/C++](https://code.visualstudio.com/docs/languages/cpp ) per VSCode.

## <a name="building-the-defender-iot-micro-agent"></a>Creazione di Defender micro Agent 

1. Aprire la directory con VSCode 

1. Passare alla directory `cmake`. 

1. Eseguire il comando seguente: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    Ad esempio: 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>Passaggi successivi

[Configurare la soluzione Azure Defender per l'](quickstart-configure-your-solution.md)it.
