---
title: Librerie client per Azure Key Vault | Microsoft Docs
description: Librerie client per Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/14/2020
ms.author: mbaldwin
ms.openlocfilehash: c5b07c1059e146aaf3c3c2a26038514eb614a3cc
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505920"
---
# <a name="client-libraries-for-azure-key-vault"></a>Librerie client per Azure Key Vault

Le librerie client per Azure Key Vault consentono l'accesso a livello di codice alle funzionalità di Key Vault da un'ampia gamma di linguaggi, tra cui .NET, Python, Java e JavaScript.

## <a name="client-libraries-per-language-and-object"></a>Librerie client per linguaggio e oggetto

Ogni SDK dispone di librerie client distinte per l'insieme di credenziali delle chiavi, i segreti, le chiavi e i certificati, in base alla tabella riportata di seguito.

| Linguaggio | Segreti | Chiavi | Certificati | Key Vault (piano di gestione) |
|--|--|--|--|--|
| .NET | - [Informazioni di riferimento sulle API](/dotnet/api/azure.security.keyvault.secrets)<br>- [Pacchetto NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Secrets)<br>- [Avvio rapido](../secrets/quick-create-net.md) | - [Informazioni di riferimento sulle API](/dotnet/api/azure.security.keyvault.keys)<br>- [Pacchetto NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Keys/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Keys)<br>- [Avvio rapido](../keys/quick-create-net.md) | - [Informazioni di riferimento sulle API](/dotnet/api/azure.security.keyvault.certificates)<br>- [Pacchetto NuGet](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Azure.Security.KeyVault.Certificates)<br>- [Avvio rapido](../certificates/quick-create-net.md) | - [Informazioni di riferimento sulle API](/dotnet/api/microsoft.azure.management.keyvault)<br>- [Pacchetto NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.KeyVault/)<br> - [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.Management.KeyVault)|
| Python| - [Informazioni di riferimento sulle API](/python/api/overview/azure/keyvault-secrets-readme)<br>- [Pacchetto PyPi](https://pypi.org/project/azure-keyvault-secrets/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)<br>- [Avvio rapido](../secrets/quick-create-python.md) |- [Informazioni di riferimento sulle API](/python/api/overview/azure/keyvault-keys-readme)<br>- [Pacchetto PyPi](https://pypi.org/project/azure-keyvault-keys/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)<br>- [Avvio rapido](../keys/quick-create-python.md) | - [Informazioni di riferimento sulle API](/python/api/overview/azure/keyvault-certificates-readme)<br>- [Pacchetto PyPi](https://pypi.org/project/azure-keyvault-certificates/)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)<br>- [Avvio rapido](../certificates/quick-create-python.md) | - [Informazioni di riferimento sulle API](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br> - [Pacchetto PyPi](https://pypi.org/project/azure-mgmt-keyvault/)<br> - [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-mgmt-keyvault)|
| Java | - [Informazioni di riferimento sulle API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-secrets)<br>- [Avvio rapido](../secrets/quick-create-java.md) |- [Informazioni di riferimento sulle API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-keys)<br>- [Avvio rapido](../keys/quick-create-java.md) | - [Informazioni di riferimento sulle API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/azure-security-keyvault-certificates)<br>- [Avvio rapido](../certificates/quick-create-java.md) |- [Informazioni di riferimento sulle API](/java/api/com.microsoft.azure.management.keyvault)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/keyvault/mgmt-v2016_10_01)|
| Node.js | - [Informazioni di riferimento sulle API](/javascript/api/@azure/keyvault-secrets/)<br>- [Pacchetto npm](https://www.npmjs.com/package/@azure/keyvault-secrets)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-secrets)<br>- [Avvio rapido](../secrets/quick-create-node.md) |- [Informazioni di riferimento sulle API](/javascript/api/@azure/keyvault-keys/)<br>- [Pacchetto npm](https://www.npmjs.com/package/@azure/keyvault-keys)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-keys)<br>- [Avvio rapido](../keys/quick-create-node.md)| - [Informazioni di riferimento sulle API](/javascript/api/@azure/keyvault-certificates/)<br>- [Pacchetto npm](https://www.npmjs.com/package/@azure/keyvault-certificates)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/keyvault-certificates)<br>- [Avvio rapido](../certificates/quick-create-node.md) |  - [Informazioni di riferimento sulle API](/javascript/api/@azure/arm-keyvault/)<br>- [Pacchetto npm](https://www.npmjs.com/package/@azure/arm-keyvault)<br>- [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/keyvault/arm-keyvault)

## <a name="next-steps"></a>Passaggi successivi

- Vedere la [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)
- Altre informazioni su come [Eseguire l'autenticazione a un insieme di credenziali delle chiavi](authentication.md)
- Altre informazioni su come [Proteggere l'accesso a un insieme di credenziali delle chiavi](secure-your-key-vault.md)
