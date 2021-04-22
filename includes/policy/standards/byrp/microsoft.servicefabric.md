---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e721c25b3374e908c70fcec463625daeea0165a0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876125"
---
## <a name="azure-security-benchmark"></a>Benchmark di sicurezza di Azure

[Azure Security Benchmark](/security/benchmark/azure/introduction) offre consigli sulla protezione delle soluzioni cloud in Azure. Per informazioni sul mapping completo di questo servizio ad Azure Security Benchmark, vedere i [file di mapping di Azure Security Benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa incorporata di conformità con le normative per Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Identity Management |IM-1 |Standardizzare Azure Active Directory come sistema centrale di identità e autenticazione |[I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Protezione dei dati |DP-5 |Crittografare i dati sensibili in stato di inquieto |[La proprietà ClusterProtectionLevel dei cluster di Service Fabric dovrebbe essere impostata su EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="azure-security-benchmark-v1"></a>Azure Security Benchmark v1

[Azure Security Benchmark](/security/benchmark/azure/introduction) offre consigli sulla protezione delle soluzioni cloud in Azure. Per informazioni sul mapping completo di questo servizio ad Azure Security Benchmark, vedere i [file di mapping di Azure Security Benchmark](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa incorporata di conformità con le normative per Azure Security Benchmark](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Gestione delle identità e controllo di accesso |3.9 |Usare Azure Active Directory |[I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Protezione dei dati |4.8 |Crittografare le informazioni riservate inattive |[La proprietà ClusterProtectionLevel dei cluster di Service Fabric dovrebbe essere impostata su EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="cmmc-level-3"></a>CMMC Livello 3

Per esaminare il mapping delle Criteri di Azure disponibili per tutti i servizi di Azure a questo standard di conformità, vedere Criteri di Azure [Regulatory Compliance - CMMC Level 3](../../../../articles/governance/policy/samples/cmmc-l3.md).
Per altre informazioni su questo standard di conformità, vedere [Cybersecurity Maturity Model Certification (CMMC)](https://www.acq.osd.mil/cmmc/docs/CMMC_Model_Main_20200203.pdf).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Protezione del sistema e delle comunicazioni |SC.3.177 |Usare la crittografia convalidata da FIPS quando viene usata per proteggere la riservatezza di CUI. |[La proprietà ClusterProtectionLevel dei cluster di Service Fabric dovrebbe essere impostata su EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|Protezione del sistema e delle comunicazioni |SC.3.191 |Proteggere la riservatezza di CUI nello stato inattivo. |[La proprietà ClusterProtectionLevel dei cluster di Service Fabric dovrebbe essere impostata su EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="iso-270012013"></a>ISO 27001:2013

Per esaminare il mapping delle Criteri di Azure disponibili per tutti i servizi di Azure a questo standard di conformità, vedere [Criteri di Azure Regulatory Compliance - ISO 27001:2013](../../../../articles/governance/policy/samples/iso-27001.md).
Per altre informazioni su questo standard di conformità, vedere [ISO 27001:2013](https://www.iso.org/isoiec-27001-information-security.html).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controllo di accesso |9.2.3 |Gestione dei diritti di accesso con privilegi |[I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Crittografia |10.1.1 |Criteri per l'uso dei controlli crittografici |[La proprietà ClusterProtectionLevel dei cluster di Service Fabric dovrebbe essere impostata su EncryptAndSign](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="new-zealand-ism-restricted"></a>Nuova Zelanda - ISM con restrizioni

Per esaminare il mapping delle Criteri di Azure disponibili per tutti i servizi di Azure a questo standard di conformità, vedere Criteri di Azure [Regulatory Compliance - New Zealand ISM Restricted](../../../../articles/governance/policy/samples/new-zealand-ism.md).
Per altre informazioni su questo standard di conformità, vedere [New Zealand ISM Restricted](https://www.nzism.gcsb.govt.nz/).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controllo di accesso e password |AC-2 |16.1.32 Identificazione utente del sistema |[I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Per informazioni sul mapping delle definizioni predefinite di Criteri di Azure per tutti i servizi di Azure a questo standard di conformità, vedere [Dettagli dell'iniziativa predefinita di conformità alle normative per NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md).
Per altre informazioni su questo standard di conformità, vedere [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Dominio |ID controllo |Titolo controllo |Policy<br /><sub>(Portale di Azure)</sub> |Versione del criterio<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Controllo di accesso |AC-2 (7) |Gestione degli account \| Schemi basati sui ruoli |[I cluster di Service Fabric deve usare solo Azure Active Directory per l'autenticazione client](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

