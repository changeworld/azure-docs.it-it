---
title: Aggiornare le credenziali dell'entità servizio
description: Aggiornare le credenziali per un'entità servizio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: mikeray
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 1c7ccec6228a6dcfb457bda8f6ecd384775d4b27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669543"
---
# <a name="update-service-principal-credentials"></a>Aggiornare le credenziali dell'entità servizio

Quando si modificano le credenziali dell'entità servizio, è necessario aggiornare i segreti nel controller di dati.

Se, ad esempio, il controller dati è stato distribuito usando un set specifico di valori per l'ID tenant dell'entità servizio, l'ID client e il segreto client, quindi si modificano uno o più di questi valori, è necessario aggiornare i segreti nel controller dei dati.  Di seguito sono riportate le istruzioni per aggiornare l'ID tenant, l'ID client o il segreto client. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="background"></a>Sfondo

L'entità servizio è stata creata in [creare un'entità servizio](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal). 

## <a name="steps"></a>Passaggi

1. Accedere al segreto dell'entità servizio nell'editor predefinito.

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   Ad esempio, per modificare il segreto dell'entità servizio in un controller dati nello `arc` spazio dei nomi, eseguire il comando seguente:

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   Il `kubecl edit` comando apre il file Credentials. yml nell'editor predefinito. 


1. Modificare il segreto dell'entità servizio. 

   Nell'editor predefinito sostituire i valori nella sezione dati con le informazioni aggiornate sulle credenziali.

   Ad esempio:

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   Modificare i valori per `clientID` `clientSecret` e/o `tenantID` in base alle esigenze. 

> [!NOTE]
>I valori devono essere codificati in base 64. Non modificare altre proprietà.

Se viene fornito un valore non corretto per `clientId` , `clientSecret` o `tenantID` verrà visualizzato un messaggio di errore come indicato di seguito nei `control-xxxx` log del contenitore Pod/controller:

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>Passaggi successivi

[Recuperare il nome utente e la password per la connessione al controller di dati Arc](retrieve-the-username-password-for-data-controller.md)

[Creare un'entità servizio](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
