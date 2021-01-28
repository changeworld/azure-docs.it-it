---
title: Registrazione di errori ed eccezioni in MSAL per Python
titleSuffix: Microsoft identity platform
description: Informazioni su come registrare gli errori e le eccezioni in MSAL per Python
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 560caa7e29ce12b58e151a1362aaf2c662646f13
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954801"
---
# <a name="logging-in-msal-for-python"></a>Registrazione in MSAL per Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL per la registrazione di Python

La registrazione in MSAL Python usa il meccanismo di registrazione standard di Python. ad esempio, `logging.info("msg")` è possibile configurare la registrazione MSAL come indicato di seguito (e visualizzarla in azione nella [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Abilitare la registrazione di debug per tutti i moduli

Per impostazione predefinita, la registrazione in qualsiasi script Python è disattivata. Se si vuole abilitare la registrazione del debug per tutti i moduli nell'intero script Python, usare:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Tacita solo registrazione MSAL

Per tacitare solo la registrazione della libreria MSAL, abilitando la registrazione del debug in tutti gli altri moduli nello script Python, disattivare il logger usato da MSAL Python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Dati personali e aziendali in Python

MSAL per Python non registra i dati personali o i dati dell'organizzazione. Non è disponibile alcuna proprietà per attivare o disattivare la registrazione dei dati personali o aziendali.

È possibile usare la registrazione standard di Python per registrare il contenuto desiderato, ma si è responsabili della gestione sicura dei dati sensibili e dei requisiti normativi.

Per ulteriori informazioni sulla registrazione in Python, vedere la pagina relativa alla  [procedura](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)di registrazione di Python.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice, vedere [esempi di codice della piattaforma Microsoft Identity](sample-v2-code.md).

---