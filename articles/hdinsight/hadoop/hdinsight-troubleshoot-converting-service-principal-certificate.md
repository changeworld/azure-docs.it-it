---
title: Conversione del contenuto del certificato in base-64-Azure HDInsight
description: Conversione del contenuto del certificato dell'entità servizio nel formato stringa con codifica base 64 in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ee2e783a7a9443db4c96817cf611272667c675a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944416"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Conversione del contenuto del certificato dell'entità servizio nel formato stringa con codifica base 64 in HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Viene visualizzato un messaggio di errore che informa che l'input non è una stringa base 64 valida perché contiene un carattere non base 64, più di due caratteri di riempimento o uno spazio non vuoto tra i caratteri di riempimento.

## <a name="cause"></a>Causa

Quando si usa PowerShell o la distribuzione di modelli di Azure per creare cluster con Data Lake come risorsa di archiviazione primaria o aggiuntiva, il contenuto del certificato dell'entità servizio fornito per accedere al Data Lake account di archiviazione è nel formato base 64. La conversione non corretta del contenuto del certificato pfx in una stringa con codifica base 64 può causare questo errore.

## <a name="resolution"></a>Soluzione

Una volta ottenuto il certificato dell'entità servizio in formato pfx (vedere [qui](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) per i passaggi di esempio per la creazione dell'entità servizio), usare il comando di PowerShell seguente o il frammento di codice C# per convertire il contenuto del certificato in formato base-64.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]