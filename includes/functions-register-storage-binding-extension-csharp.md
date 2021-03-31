---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "78201032"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Registrare le estensioni delle associazioni

Ad eccezione dei trigger HTTP e timer, i binding vengono implementati come pacchetti di estensione. Eseguire il comando [dotnet add package](/dotnet/core/tools/dotnet-add-package) seguente nella finestra del terminale per aggiungere il pacchetto di estensione di archiviazione nel progetto.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Ora è possibile aggiungere il binding di output di archiviazione nel progetto.  
::: zone-end  