---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 12/01/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2385d07ccb81041bd340a8bec0412a8f14cef56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96476414"
---
## <a name="azure-security-benchmark"></a>Benchmark di sicurezza di Azure

[Azure Security Benchmark](../../../../articles/security/benchmarks/overview.md) offre consigli sulla protezione delle soluzioni cloud in Azure. Per informazioni sul mapping completo di questo servizio ad Azure Security Benchmark, vedere i [file di mapping di Azure Security Benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa incorporata di conformità con le normative per Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Protezione dei dati |4.8 |Crittografare le informazioni riservate inattive |[Le variabili dell'account di automazione devono essere crittografate](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |

> [!NOTE]
> Quando si crea una variabile dell'account di Automazione, è possibile specificare che venga crittografata e archiviata da Automazione di Azure come asset sicuro. Dopo aver creato la variabile, non è possibile cambiarne lo stato di crittografia senza crearla nuovamente. Se sono disponibili variabili dell'account di Automazione usate per archiviare dati sensibili che non sono già crittografati, è necessario eliminarle e ricrearle come variabili crittografate. Una raccomandazione del Centro sicurezza di Azure suggerisce di crittografare tutte le variabili di Automazione di Azure, come descritto in [Le variabili dell'account di Automazione devono essere crittografate](../../../../articles/security-center/recommendations-reference.md#recs-computeapp). Se si vogliono escludere alcune variabili non crittografate da questa raccomandazione di sicurezza, vedere [Esentare una risorsa dalle raccomandazioni e dal punteggio di sicurezza](../../../../articles/security-center/exempt-resource.md) per creare una regola di esenzione.