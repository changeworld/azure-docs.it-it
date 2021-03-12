---
title: Ottenere il token di accesso usando l'interfaccia della riga di comando di Azure per FHIR
description: Questo articolo illustra come ottenere un token di accesso per l'API di Azure per FHIR usando l'interfaccia della riga di comando di Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: matjazl
ms.openlocfilehash: b7bdb7f4a22d080f382fea984e710980428067ff
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019149"
---
# <a name="get-access-token-for-azure-api-for-fhir-using-azure-cli"></a>Ottenere il token di accesso per l'API Azure per FHIR usando l'interfaccia della riga di comando

In questo articolo si apprenderà come ottenere un token di accesso per l'API di Azure per FHIR usando l'interfaccia della riga di comando di Azure. Quando si esegue [il provisioning dell'API di Azure per FHIR](fhir-paas-portal-quickstart.md), si configura un set di utenti o entità servizio che possono accedere al servizio. Se l'ID oggetto utente è nell'elenco degli ID oggetto consentiti, è possibile accedere al servizio usando un token ottenuto usando l'interfaccia della riga di comando di Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="obtain-a-token"></a>Ottenere un token

L'API di Azure per FHIR usa un oggetto `resource`  o `Audience` con URI uguale all'URI del server FHIR `https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com` . È possibile ottenere un token e archiviarlo in una variabile (denominata `$token` ) con il comando seguente:

```azurecli-interactive
token=$(az account get-access-token --resource=https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com --query accessToken --output tsv)
```

## <a name="use-with-azure-api-for-fhir"></a>Usare con l'API di Azure per FHIR

```azurecli-interactive
curl -X GET --header "Authorization: Bearer $token" https://<FHIR ACCOUNT NAME>.azurehealthcareapis.com/Patient
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come ottenere un token di accesso per l'API di Azure per FHIR usando l'interfaccia della riga di comando di Azure. Per informazioni su come accedere all'API FHIR tramite Postman, passare all'esercitazione su Postman.

>[!div class="nextstepaction"]
>[Accedere all'API FHIR con Postman](access-fhir-postman-tutorial.md)
