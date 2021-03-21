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
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578754"
---
# <a name="logging-in-msal-for-python"></a>Registrazione in MSAL per Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL per la registrazione di Python

La registrazione in MSAL per Python sfrutta il [modulo di registrazione nella libreria standard di Python](https://docs.python.org/3/library/logging.html). È possibile configurare la registrazione di MSAL come indicato di seguito (e visualizzarla in azione nella [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Abilitare la registrazione di debug per tutti i moduli

Per impostazione predefinita, la registrazione in qualsiasi script Python è disattivata. Se si vuole abilitare la registrazione dettagliata per **tutti i** moduli Python nello script, usare `logging.basicConfig` con un livello di `logging.DEBUG` :

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Tutti i messaggi di log assegnati al modulo di registrazione vengono stampati nell'output standard.

### <a name="configure-msal-logging-level"></a>Configurare il livello di registrazione MSAL

È possibile configurare il livello di registrazione del provider di log MSAL per Python usando il `logging.getLogger()` metodo con il nome del logger `"msal"` :

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>Configurare la registrazione di MSAL con app Azure Insights

I log Python vengono assegnati a un gestore di log, che per impostazione predefinita è `StreamHandler` . Per inviare i log MSAL a un Application Insights con una chiave di strumentazione, usare l'oggetto `AzureLogHandler` fornito dalla `opencensus-ext-azure` libreria.

Per installare, `opencensus-ext-azure` aggiungere il `opencensus-ext-azure` pacchetto da PyPI alle dipendenze o all'installazione PIP:

```console
pip install opencensus-ext-azure
```

Modificare quindi il gestore predefinito del `"msal"` provider di log in un'istanza di `AzureLogHandler` con una chiave di strumentazione impostata nella `APP_INSIGHTS_KEY` variabile di ambiente:

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Dati personali e aziendali in Python

MSAL per Python non registra i dati personali o i dati dell'organizzazione. Non è disponibile alcuna proprietà per attivare o disattivare la registrazione dei dati personali o aziendali.

È possibile usare la registrazione standard di Python per registrare il contenuto desiderato, ma si è responsabili della gestione sicura dei dati sensibili e dei requisiti normativi.

Per ulteriori informazioni sulla registrazione in Python, vedere la pagina relativa alla  [procedura](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial)di registrazione di Python.

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di codice, vedere [esempi di codice della piattaforma Microsoft Identity](sample-v2-code.md).
