---
title: Creare un'applicazione di identità gestita Azure Active Directory dall'interfaccia della riga di comando di Azure
titleSuffix: An Azure Communication Services quickstart
description: Le identità gestite consentono di autorizzare l'accesso ai servizi di comunicazione di Azure da applicazioni in esecuzione in macchine virtuali di Azure, app per le funzioni e altre risorse. Questa Guida introduttiva si concentra sulla gestione delle identità con l'interfaccia della riga di comando di Azure
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: 2ef5a3b162d62fa79ed01a156345070ee12b4862
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110679"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>Autorizzare l'accesso con identità gestita alla risorsa di comunicazione nell'ambiente di sviluppo

Azure Identity SDK fornisce il supporto per l'autenticazione del token Azure Active Directory (Azure AD) per Azure SDK. Le versioni più recenti degli SDK di servizi di comunicazione di Azure per .NET, Java, Python e JavaScript si integrano con la libreria di identità di Azure per fornire un modo semplice e sicuro per acquisire un token OAuth 2,0 per l'autorizzazione delle richieste dei servizi di comunicazione di Azure.

Un vantaggio di Azure Identity SDK è che consente di usare lo stesso codice per l'autenticazione tra più servizi, indipendentemente dal fatto che l'applicazione sia in esecuzione nell'ambiente di sviluppo o in Azure. Azure Identity SDK autentica un'entità di sicurezza. Quando il codice è in esecuzione in Azure, l'entità di sicurezza è un'identità gestita per le risorse di Azure. Nell'ambiente di sviluppo, l'identità gestita non esiste, pertanto l'SDK autentica l'utente o un'applicazione registrata a scopo di test.

## <a name="prerequisites"></a>Prerequisiti

 - Interfaccia della riga di comando di Azure. [Guida all'installazione](/cli/azure/install-azure-cli)
 - Un account Azure con una sottoscrizione attiva. [Crea gratuitamente un account](https://azure.microsoft.com/free)

## <a name="setting-up"></a>Configurazione

Le identità gestite devono essere abilitate nelle risorse di Azure che si sta autorizzando. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Azure portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [SDK Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Servizi app](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>Autenticare un'applicazione registrata nell'ambiente di sviluppo

Se l'ambiente di sviluppo non supporta la Single Sign-On o l'accesso tramite un Web browser, è possibile usare un'applicazione registrata per l'autenticazione dall'ambiente di sviluppo.

### <a name="creating-an-azure-active-directory-registered-application"></a>Creazione di un Azure Active Directory applicazione registrata

Per creare un'applicazione registrata dall'interfaccia della riga di comando di Azure, è necessario essere connessi all'account Azure in cui si desidera eseguire le operazioni. A tale scopo, è possibile usare il `az login` comando e immettere le credenziali nel browser. Dopo aver effettuato l'accesso all'account Azure dall'interfaccia della riga di comando, è possibile chiamare il `az ad sp create-for-rbac` comando per creare l'applicazione registrata.

Gli esempi seguenti usano l'interfaccia della riga di comando di Azure per creare una nuova applicazione registrata

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

Il `az ad sp create-for-rbac` comando restituirà un elenco di proprietà dell'entità servizio in formato JSON. Copiare questi valori in modo che sia possibile usarli per creare le variabili di ambiente necessarie nel passaggio successivo.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> La propagazione delle assegnazioni dei ruoli può richiedere alcuni minuti.

#### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

Azure Identity SDK legge i valori da tre variabili di ambiente in fase di esecuzione per autenticare l'applicazione. Nella tabella seguente viene descritto il valore da impostare per ogni variabile di ambiente.

|Variabile di ambiente|Valore
|-|-
|`AZURE_CLIENT_ID`|`appId` valore dal JSON generato 
|`AZURE_TENANT_ID`|`tenant` valore dal JSON generato
|`AZURE_CLIENT_SECRET`|`password` valore dal JSON generato

> [!IMPORTANT]
> Dopo aver impostato le variabili di ambiente, chiudere e riaprire la finestra della console. Se si usa Visual Studio o un altro ambiente di sviluppo, potrebbe essere necessario riavviarlo per poter registrare le nuove variabili di ambiente.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'autenticazione](../concepts/authentication.md)

Può essere utile vedere anche gli articoli seguenti:

- [Altre informazioni su Azure Identity Library](/dotnet/api/overview/azure/identity-readme)