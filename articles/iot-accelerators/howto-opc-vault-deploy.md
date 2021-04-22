---
title: Come distribuire il servizio di gestione certificati di OPC Vault - Azure | Microsoft Docs
description: Come distribuire il servizio di gestione dei certificati di OPC Vault da zero.
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b20f6318c2e6be701446e29ab93598752e93d287
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870284"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>Compilare e distribuire il servizio di gestione certificati di OPC Vault

> [!IMPORTANT]
> Mentre questo articolo viene aggiornato, vedere [Azure IoT industriale](https://azure.github.io/Industrial-IoT/) per il contenuto più aggiornato.

Questo articolo illustra come distribuire il servizio di gestione dei certificati di OPC Vault in Azure.

> [!NOTE]
> Per altre informazioni, vedere il repository gitHub [OPC Vault](https://github.com/Azure/azure-iiot-opc-vault-service).

## <a name="prerequisites"></a>Prerequisiti

### <a name="install-required-software"></a>Installare il software necessario

Attualmente l'operazione di compilazione e distribuzione è limitata a Windows.
Gli esempi sono tutti scritti per .NET Standard C#, che è necessario compilare il servizio e gli esempi per la distribuzione.
Tutti gli strumenti necessari per .NET Standard con gli strumenti .NET Core. Vedere [Introduzione a .NET Core.](/dotnet/articles/core/getting-started)

1. [Installare .NET Core 2.1+][dotnet-install].
2. [Installare Docker][docker-url] (facoltativo, solo se è necessaria la build Docker locale).
4. Installare gli [strumenti da riga di comando di Azure per PowerShell.][powershell-install]
5. Iscriversi per una [sottoscrizione di Azure.][azure-free]

### <a name="clone-the-repository"></a>Clonare il repository

Se non è ancora stato fatto, clonare questo repository GitHub. Aprire un prompt dei comandi o un terminale ed eseguire quanto segue:

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

In alternativa, è possibile clonare il repo direttamente in Visual Studio 2017.

### <a name="build-and-deploy-the-azure-service-on-windows"></a>Compilare e distribuire il servizio di Azure in Windows

Uno script di PowerShell offre un modo semplice per distribuire il microservizio OPC Vault e l'applicazione.

1. Aprire una finestra di PowerShell nella radice del repo. 
3. Passare alla cartella deploy `cd deploy` .
3. Scegliere un nome per `myResourceGroup` che probabilmente causerà un conflitto con altre pagine Web distribuite. Vedere la sezione "Nome del sito Web già in uso" più avanti in questo articolo.
5. Avviare la distribuzione con `.\deploy.ps1` per l'installazione interattiva o immettere una riga di comando completa:  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. Se si prevede di sviluppare con questa distribuzione, aggiungere per `-development 1` abilitare l'interfaccia utente di Swagger e distribuire le compilazioni di debug.
6. Seguire le istruzioni nello script per accedere alla sottoscrizione e per fornire informazioni aggiuntive.
9. Al termine di un'operazione di compilazione e distribuzione, verrà visualizzato il messaggio seguente:
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > In caso di problemi, vedere la sezione "Risoluzione degli errori di distribuzione" più avanti nell'articolo.

8. Aprire il browser preferito e aprire la pagina dell'applicazione: `https://myResourceGroup.azurewebsites.net`
8. Concedere all'app Web e al microservizio OPC Vault alcuni minuti per il riscaldamento dopo la distribuzione. L'home page Web potrebbe smettere di rispondere al primo utilizzo, per un massimo di un minuto, fino a quando non si ottengono le prime risposte.
11. Per esaminare l'API Swagger, aprire: `https://myResourceGroup-service.azurewebsites.net`
13. Per avviare un server GDS locale con dotnet, avviare `.\myResourceGroup-gds.cmd` . Con Docker, avviare `.\myResourceGroup-dockergds.cmd` .

È possibile ridistribuire una compilazione con esattamente le stesse impostazioni. Tenere presente che tale operazione rinnova tutti i segreti dell'applicazione e potrebbe reimpostare alcune impostazioni nelle registrazioni Azure Active Directory (Azure AD) dell'applicazione.

È anche possibile ridistribuire solo i file binari dell'app Web. Con il parametro `-onlyBuild 1` , i nuovi pacchetti ZIP del servizio e dell'app vengono distribuiti nelle applicazioni Web.

Al termine della distribuzione, è possibile iniziare a usare i servizi. Vedere [Gestire il servizio di gestione dei certificati OPC Vault.](howto-opc-vault-manage.md)

## <a name="delete-the-services-from-the-subscription"></a>Eliminare i servizi dalla sottoscrizione

Ecco come:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare al gruppo di risorse in cui è stato distribuito il servizio.
3. Scegliere **Elimina gruppo di risorse** e confermare.
4. Dopo un breve periodo di tempo, tutti i componenti del servizio distribuiti vengono eliminati.
5. Passare a **Azure Active Directory** > **Registrazioni per l'app**.
6. Dovrebbero essere elencate tre registrazioni per ogni gruppo di risorse distribuito. Le registrazioni hanno i nomi seguenti: `resourcegroup-client` , `resourcegroup-module` , `resourcegroup-service` . Eliminare ogni registrazione separatamente.

A questo punto tutti i componenti distribuiti vengono rimossi.

## <a name="troubleshooting-deployment-failures"></a>Risoluzione dei problemi relativi agli errori di distribuzione

### <a name="resource-group-name"></a>Nome del gruppo di risorse

Usare un nome di gruppo di risorse breve e semplice. Il nome viene usato anche per assegnare un nome alle risorse e al prefisso dell'URL del servizio. Di conseguenza, deve essere conforme ai requisiti di denominazione delle risorse.  

### <a name="website-name-already-in-use"></a>Nome del sito Web già in uso

È possibile che il nome del sito Web sia già in uso. È necessario usare un nome di gruppo di risorse diverso. I nomi host utilizzati dallo script di distribuzione sono: https: \/ /resourcegroupname.azurewebsites.net e https: \/ /resourgroupname-service.azurewebsites.net.
Altri nomi di servizi sono compilati dalla combinazione di hash dei nomi brevi e sono improbabile che siano in conflitto con altri servizi.

### <a name="azure-ad-registration"></a>Registrazione di Azure AD 

Lo script di distribuzione tenta di registrare tre Azure AD applicazioni in Azure AD. A seconda delle autorizzazioni nel tenant Azure AD, questa operazione potrebbe non riuscire. Sono disponibili due opzioni:

- Se si sceglie un tenant Azure AD da un elenco di tenant, riavviare lo script e sceglierne uno diverso dall'elenco.
- In alternativa, distribuire un tenant Azure AD privato in un'altra sottoscrizione. Riavviare lo script e scegliere di usarlo.

## <a name="deployment-script-options"></a>Opzioni dello script di distribuzione

Lo script accetta i parametri seguenti:


```
-resourceGroupName
```

Può trattarsi del nome di un gruppo di risorse esistente o nuovo.

```
-subscriptionId
```


Si tratta dell'ID sottoscrizione in cui verranno distribuite le risorse. È facoltativo.

```
-subscriptionName
```


In alternativa, è possibile usare il nome della sottoscrizione.

```
-resourceGroupLocation
```


Si tratta di una località del gruppo di risorse. Se specificato, questo parametro tenta di creare un nuovo gruppo di risorse in questa posizione. Questo parametro è anche facoltativo.


```
-tenantId
```


Questo è il Azure AD tenant da usare. 

```
-development 0|1
```

Questa operazione viene distribuita per lo sviluppo. Usare la build di debug e impostare l'ambiente ASP.NET su Sviluppo. Creare `.publishsettings` per l'importazione Visual Studio 2017, per consentire la distribuzione diretta dell'app e del servizio. Questo parametro è anche facoltativo.

```
-onlyBuild 0|1
```

Questo consente di ricompilare e ridistribuire solo le app Web e di ricompilare i contenitori Docker. Questo parametro è anche facoltativo.

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://dotnet.microsoft.com/download

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come distribuire OPC Vault da zero, è possibile:

> [!div class="nextstepaction"]
> [Gestire OPC Vault](howto-opc-vault-manage.md)