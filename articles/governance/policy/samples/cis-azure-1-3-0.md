---
title: Dettagli sulla conformità alle normative per CIS Microsoft Azure Foundations Benchmark 1.3.0
description: Dettagli dell'iniziativa incorporata cis Microsoft Azure Foundations Benchmark 1.3.0 sulla conformità alle normative. Ogni controllo viene mappato a una o più definizioni di Criteri di Azure che assistono nella valutazione.
ms.date: 04/14/2021
ms.topic: sample
ms.custom: generated
ms.openlocfilehash: 3dc7578af93f36ba8f0400099bb21cdabdcffb70
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497728"
---
# <a name="details-of-the-cis-microsoft-azure-foundations-benchmark-130-regulatory-compliance-built-in-initiative"></a>Dettagli dell'iniziativa incorporata di conformità alle normative CIS Microsoft Azure Foundations Benchmark 1.3.0

L'articolo seguente illustra in dettaglio il mapping della definizione  dell'iniziativa incorporata di conformità alle normative di Criteri di Azure ai domini e ai controlli di conformità **in** CIS Microsoft Azure Foundations Benchmark 1.3.0.
Per altre informazioni su questo standard di conformità, vedere [CIS Microsoft Azure Foundations Benchmark 1.3.0.](https://www.cisecurity.org/benchmark/azure/) Per informazioni sulle _Proprietà_, vedere [Struttura delle definizioni di criteri in Criteri di Azure](../concepts/definition-structure.md#type) e [Responsabilità condivisa nel cloud](../../../security/fundamentals/shared-responsibility.md).

I mapping seguenti sono ai controlli **CIS Microsoft Azure Foundations Benchmark 1.3.0.** Usare la barra di spostamento a destra per passare direttamente a un **dominio di conformità** specifico. Molti controlli vengono implementati con una definizione dell'iniziativa [Criteri di Azure](../overview.md). Per esaminare la definizione dell'iniziativa completa, aprire **Criteri** nel portale di Azure e selezionare la pagina **Definizioni**.
Trovare e selezionare quindi la definizione di **iniziativa incorporata CIS Microsoft Azure Foundations Benchmark v1.3.0** Conformità alle normative.

> [!IMPORTANT]
> Ogni controllo tra quelli riportati di seguito è associato a una o più definizioni di [Criteri di Azure](../overview.md).
> Questi criteri possono aiutare a [valutare la conformità](../how-to/get-compliance-data.md) con il controllo. In molti casi tuttavia non si tratta di una corrispondenza uno-a-uno o completa tra un controllo e uno o più criteri. Di per sé, **Conforme** in Criteri di Azure si riferisce solo alle definizioni dei criteri e non garantisce che l'utente sia completamente conforme a tutti i requisiti di un controllo. Inoltre, in questo momento lo standard di conformità include controlli che non vengono gestiti da alcuna definizione di Criteri di Azure. La conformità in Criteri di Azure è quindi solo una visualizzazione parziale dello stato di conformità generale. Le associazioni tra domini di conformità, controlli e definizioni di Criteri di Azure per questo standard di conformità possono cambiare nel tempo. Per visualizzare la cronologia delle modifiche, vedere la [cronologia dei commit di GitHub](https://github.com/Azure/azure-policy/commits/master/built-in-policies/policySetDefinitions/Regulatory%20Compliance/CISv1_3_0.json).

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>Assicurarsi che la funzionalità di autenticazione a più fattori sia abilitata per tutti gli utenti con privilegi

**ID**: CIS Azure 1.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'autenticazione MFA deve essere abilitata per gli account con autorizzazioni di scrittura per la sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9297c21d-2ed6-4474-b48f-163f75654ce3) |È necessario abilitare Multi-Factor Authentication (MFA) per tutti gli account della sottoscrizione con privilegi di scrittura per evitare una violazione di account o risorse. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForWritePermissions_Audit.json) |
|[L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di proprietario per la sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faa633080-8b72-40c4-a2d7-d00c03e80bed) |È necessario abilitare Multi-Factor Authentication (MFA) per tutti gli account della sottoscrizione con autorizzazioni di proprietario per evitare una violazione di account o risorse. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForOwnerPermissions_Audit.json) |

### <a name="ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>Assicurarsi che la funzionalità di autenticazione a più fattori sia abilitata per tutti gli utenti senza privilegi

**ID**: CIS Azure 1.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'autenticazione MFA deve essere abilitata negli account con autorizzazioni di lettura per la sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe3576e28-8b17-4677-84c3-db2990658d64) |È necessario abilitare Multi-Factor Authentication (MFA) per tutti gli account della sottoscrizione con privilegi di lettura per evitare una violazione di account o risorse. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableMFAForReadPermissions_Audit.json) |

### <a name="ensure-guest-users-are-reviewed-on-a-monthly-basis"></a>Verificare che gli utenti guest siano esaminati su base mensile

**ID**: CIS Azure 1.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gli account esterni con autorizzazioni di proprietario devono essere rimossi dalla sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8456c1c-aa66-4dfb-861a-25d127b775c9) |È necessario rimuovere dalla sottoscrizione gli account esterni con autorizzazioni di proprietario in modo da evitare l'accesso non monitorato. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWithOwnerPermissions_Audit.json) |
|[Gli account esterni con autorizzazioni di lettura devono essere rimossi dalla sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5f76cf89-fbf2-47fd-a3f4-b891fa780b60) |È necessario rimuovere dalla sottoscrizione gli account esterni con privilegi di lettura in modo da evitare l'accesso non monitorato. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsReadPermissions_Audit.json) |
|[Gli account esterni con autorizzazioni di scrittura devono essere rimossi dalla sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5c607a2e-c700-4744-8254-d77e7c9eb5e4) |È necessario rimuovere dalla sottoscrizione gli account esterni con privilegi di scrittura in modo da evitare l'accesso non monitorato. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_RemoveExternalAccountsWritePermissions_Audit.json) |

### <a name="ensure-that-no-custom-subscription-owner-roles-are-created"></a>Assicurarsi che non siano stati creati ruoli di proprietario della sottoscrizione personalizzati

**ID:** CIS Azure 1.21 **Proprietà:** Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Non devono esistere ruoli di proprietario della sottoscrizione personalizzati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F10ee2ea2-fb4d-45b8-a7e9-a2e770044cd9) |Questo criterio garantisce che non esista alcun ruolo di proprietario della sottoscrizione personalizzato. |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |

## <a name="security-center"></a>Centro sicurezza

### <a name="ensure-that-azure-defender-is-set-to-on-for-servers"></a>Assicurarsi che Azure Defender sia impostato su Sì per i server

**ID**: CIS Azure 2.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender per i server deve essere abilitato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4da35fc9-c9e7-4960-aec9-797fe7d9051d) |Azure Defender per i server fornisce protezione dalle minacce in tempo reale per i carichi di lavoro dei server e genera raccomandazioni per la protezione avanzata e avvisi sulle attività sospette. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnVM_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-app-service"></a>Assicurarsi che Azure Defender sia impostato su Sì per il servizio app

**ID**: CIS Azure 2.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender per il Servizio app deve essere abilitato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2913021d-f2fd-4f3d-b958-22354e2bdbcb) |Azure Defender per il Servizio app sfrutta la portata del cloud e la visibilità di Azure come provider di servizi cloud per monitorare gli attacchi alle app Web più comuni. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnAppServices_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-azure-sql-database-servers"></a>Assicurarsi che Azure Defender sia impostato su Sì per i server Azure SQL database

**ID**: CIS Azure 2.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender per i server di database SQL di Azure deve essere abilitato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7fe3b40f-802b-4cdd-8bd4-fd799c948cc2) |Azure Defender per SQL fornisce funzionalità per l'individuazione e la mitigazione di potenziali vulnerabilità del database, il rilevamento di attività anomale che possono indicare minacce ai database SQL e l'individuazione e la classificazione di dati sensibili. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServers_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-sql-servers-on-machines"></a>Assicurarsi che Azure Defender sia impostato su Sì per i server SQL nei computer

**ID**: CIS Azure 2.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender per i server SQL nelle macchine virtuali deve essere abilitato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6581d072-105e-4418-827f-bd446d56421b) |Azure Defender per SQL fornisce funzionalità per l'individuazione e la mitigazione di potenziali vulnerabilità del database, il rilevamento di attività anomale che possono indicare minacce ai database SQL e l'individuazione e la classificazione di dati sensibili. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedDataSecurityOnSqlServerVirtualMachines_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-storage"></a>Assicurarsi che Azure Defender sia impostato su Sì per l'archiviazione

**ID**: CIS Azure 2.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender per l'archiviazione deve essere abilitato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F308fbb08-4ab8-4e67-9b29-592e93fb94fa) |Azure Defender per l'archiviazione offre il rilevamento di tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di archiviazione. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnStorageAccounts_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-kubernetes"></a>Assicurarsi che Azure Defender sia impostato su Sì per Kubernetes

**ID**: CIS Azure 2.6 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender per Kubernetes deve essere abilitato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F523b5cd1-3e23-492f-a539-13118b6d1e3a) |Azure Defender per Kubernetes fornisce protezione dalle minacce in tempo reale per gli ambienti containerizzati e genera avvisi per le attività sospette. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKubernetesService_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-container-registries"></a>Assicurarsi che Azure Defender sia impostato su Sì per i registri contenitori

**ID**: CIS Azure 2.7 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender per i registri contenitori deve essere abilitato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4) |Azure Defender per i registri contenitori fornisce l'analisi delle vulnerabilità delle immagini di cui è stato eseguito il pull negli ultimi 30 giorni oppure di cui è stato eseguito il push o l'importazione nel registro ed espone i risultati dettagliati per immagine. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnContainerRegistry_Audit.json) |

### <a name="ensure-that-azure-defender-is-set-to-on-for-key-vault"></a>Assicurarsi che Azure Defender sia impostato su Sì per Key Vault

**ID:** CIS Azure 2.8 **Proprietà:** Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Defender per Key Vault deve essere abilitato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0e6763cc-5078-4e64-889d-ff4d9a839047) |Azure Defender per Key Vault fornisce un ulteriore livello di protezione e di intelligence per sulla sicurezza grazie al rilevamento di tentativi insoliti e potenzialmente dannosi di accesso o sfruttamento degli account di Key Vault. |AuditIfNotExists, Disabled |[1.0.3](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableAdvancedThreatProtectionOnKeyVaults_Audit.json) |

### <a name="ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>Assicurarsi che "Provisioning automatico dell'agente di monitoraggio" sia impostata su "Sì"

**ID:** CIS Azure 2.11 **Proprietà:** Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare il provisioning automatico dell'agente di Log Analytics nella sottoscrizione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F475aae12-b88a-4572-8b36-9b712b2b3a17) |Per monitorare le minacce e le vulnerabilità della sicurezza, Centro sicurezza di Azure raccoglie i dati dalle macchine virtuali di Azure. I dati vengono raccolti dall'agente di Log Analytics, precedentemente noto come Microsoft Monitoring Agent (MMA), che esegue la lettura di varie configurazioni relative alla sicurezza e log eventi dalla macchina virtuale e copia i dati nell'area di lavoro Log Analytics per l'analisi. È consigliabile abilitare il provisioning automatico per distribuire automaticamente l'agente in tutte le macchine virtuali di Azure supportate e in tutte le nuove macchine virtuali che vengono create. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Automatic_provisioning_log_analytics_monitoring_agent.json) |

### <a name="ensure-additional-email-addresses-is-configured-with-a-security-contact-email"></a>Assicurarsi che "Indirizzi di posta elettronica aggiuntivi" sia configurato con un indirizzo di posta elettronica del contatto di sicurezza

**ID**: CIS Azure 2.13 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per le sottoscrizioni deve essere impostato un indirizzo di posta elettronica di contatto per i problemi relativi alla sicurezza](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4f4f78b8-e367-4b10-a341-d9a4ad5cf1c7) |Per garantire che le persone pertinenti all'interno dell'organizzazione ricevano una notifica in caso di potenziale violazione della sicurezza in una delle sottoscrizioni, impostare un contatto di sicurezza per la ricezione delle notifiche tramite posta elettronica dal Centro sicurezza. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Security_contact_email.json) |

### <a name="ensure-that-notify-about-alerts-with-the-following-severity-is-set-to-high"></a>Assicurarsi che l'opzione "Notifica degli avvisi con la gravità seguente" sia impostata su "Alta"

**ID**: CIS Azure 2.14 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare le notifiche di posta elettronica per gli avvisi con gravità alta](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6e2593d9-add6-4083-9c9b-4b7d2188c899) |Per garantire che le persone pertinenti all'interno dell'organizzazione ricevano una notifica in caso di potenziale violazione della sicurezza in una delle sottoscrizioni, abilitare l'invio delle notifiche tramite posta elettronica per gli avvisi con gravità elevata nel Centro sicurezza. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_Email_notification.json) |

## <a name="storage-accounts"></a>Account di archiviazione

### <a name="ensure-that-secure-transfer-required-is-set-to-enabled"></a>Assicurarsi che l'opzione "Trasferimento sicuro obbligatorio" sia impostata su "Abilitato".

**ID**: CIS Azure 3.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare il trasferimento sicuro agli account di archiviazione](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F404c3081-a854-4457-ae30-26a93ef643f9) |Requisito di controllo del trasferimento sicuro nell'account di archiviazione. Il trasferimento sicuro è un'opzione che impone all'account di archiviazione di accettare richieste solo da connessioni sicure (HTTPS). L'uso di HTTPS garantisce l'autenticazione tra il server e il servizio e protegge i dati in transito dagli attacchi a livello rete, come attacchi man-in-the-middle, eavesdropping e hijack della sessione |Audit, Deny, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_AuditForHTTPSEnabled_Audit.json) |

### <a name="ensure-that-public-access-level-is-set-to-private-for-blob-containers"></a>Assicurarsi che 'Livello di accesso pubblico' sia impostato su Privato per i contenitori BLOB

**ID:** CIS Azure 3.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'accesso pubblico agli account di archiviazione non deve essere consentito](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |L'accesso in lettura pubblico anonimo a contenitori e BLOB in Archiviazione di Azure è un metodo pratico per condividere i dati, ma potrebbe comportare rischi per la sicurezza. Per evitare violazioni dei dati provocate da accesso anonimo indesiderato, Microsoft consiglia di impedire l'accesso pubblico a un account di archiviazione, a meno che non sia richiesto dallo scenario. |Audit, Deny, Disabled |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>Assicurarsi che la regola di accesso alla rete predefinita per gli account di archiviazione sia impostata su Nega

**ID:** CIS Azure 3.6 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gli account di archiviazione devono limitare l'accesso alla rete](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34c877ad-507e-4c82-993e-3452a6e0ad3c) |L'accesso di rete agli account di archiviazione deve essere limitato. Configurare regole di rete in modo che l'account di archiviazione sia accessibile solo alle applicazioni provenienti da reti consentite. Per consentire le connessioni da client Internet o locali specifici, è possibile concedere l'accesso al traffico da reti virtuali di Azure specifiche o a intervalli di indirizzi IP Internet pubblici |Audit, Deny, Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/Storage_NetworkAcls_Audit.json) |
|[Gli account di archiviazione devono limitare l'accesso alla rete usando regole di rete virtuale](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2a1a9cdf-e04d-429a-8416-3bfb72a1b26f) |Protegge gli account di archiviazione da potenziali minacce usando le regole di rete virtuale come metodo preferito anziché il filtro basato su IP. La disabilitazione del filtro basato su IP, impedisce agli indirizzi IP pubblici di accedere agli account di archiviazione. |Audit, Deny, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountOnlyVnetRulesEnabled_Audit.json) |

### <a name="ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>Assicurarsi che il criterio "Servizi Microsoft attendibili" sia abilitato per l'accesso all'account di archiviazione

**ID**: CIS Azure 3.7 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gli account di archiviazione devono consentire l'accesso da servizi Microsoft attendibili](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc9d007d0-c057-4772-b18c-01e546713bcd) |Alcuni servizi Microsoft che interagiscono con gli account di archiviazione vengono eseguiti da reti alle quali non è possibile concedere l'accesso tramite le regole di rete. Per far sì che questo tipo di servizi funzioni come previsto, consentire al set di servizi Microsoft attendibili di ignorare le regole di rete. Questi servizi usano quindi l'autenticazione avanzata per accedere all'account di archiviazione. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccess_TrustedMicrosoftServices_Audit.json) |

### <a name="ensure-storage-for-critical-data-are-encrypted-with-customer-managed-key"></a>Assicurarsi che l'archiviazione per i dati critici sia crittografata con la chiave gestita dal cliente

**ID:** CIS Azure 3.9 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gli account di archiviazione devono usare la chiave gestita dal cliente per la crittografia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6fac406b-40ca-413b-bf8e-0bf964659c25) |Proteggere l'account di archiviazione con maggiore flessibilità usando chiavi gestite dal cliente. Quando si specifica una chiave gestita dal cliente, tale chiave viene usata per proteggere e controllare l'accesso alla chiave che crittografa i dati. L'uso di chiavi gestite dal cliente offre funzionalità aggiuntive per controllare la rotazione della chiave di crittografia della chiave o cancellare i dati crittograficamente. |Audit, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/StorageAccountCustomerManagedKeyEnabled_Audit.json) |

## <a name="database-services"></a>Servizi di database

### <a name="ensure-that-auditing-is-set-to-on"></a>Assicurarsi che "Controllo" sia impostato su "Attivato"

**ID:** CIS Azure 4.1.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare il controllo in SQL Server](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa6fb4358-5bf4-4ad7-ba82-2cd2f41ce5e9) |Abilitare il controllo in SQL Server per verificare le attività del database in tutti i database sul server e salvarle in un log di controllo. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditing_Audit.json) |

### <a name="ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>Assicurarsi che "Crittografia dati" sia impostato su "Attivato" in un database SQL

**ID:** CIS Azure 4.1.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare Transparent Data Encryption nei database SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17k78e20-9358-41c9-923c-fb736d382a12) |Abilitare Transparent Data Encryption per proteggere i dati inattivi e rispettare i requisiti relativi alla conformità |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlDBEncryption_Audit.json) |

### <a name="ensure-that-auditing-retention-is-greater-than-90-days"></a>Assicurarsi che il periodo di conservazione dei dati di controllo sia "superiore a 90 giorni"

**ID:** CIS Azure 4.1.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I server SQL con controllo sulla destinazione dell'account di archiviazione devono essere configurati con una conservazione di 90 giorni o superiore](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F89099bee-89e0-4b26-a5f4-165451757743) |Ai fini dell'analisi degli eventi imprevisti, è consigliabile impostare la conservazione dei dati per il controllo SQL Server'account di archiviazione sulla destinazione dell'account di archiviazione su almeno 90 giorni. Verificare di soddisfare le regole di conservazione necessarie per le aree in cui si opera. Questa operazione è talvolta necessaria per la conformità agli standard normativi. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServerAuditingRetentionDays_Audit.json) |

### <a name="ensure-that-advanced-threat-protection-atp-on-a-sql-server-is-set-to-enabled"></a>Assicurarsi che Advanced Threat Protection (ATP) in un server SQL sia impostato su "Abilitato"

**ID:** CIS Azure 4.2.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La soluzione Sicurezza dei dati avanzata deve essere abilitata nell'istanza gestita di SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb7388-5bf4-4ad7-ba99-2cd2f41cebb9) |Controlla ogni istanza gestita di SQL senza Sicurezza dei dati avanzata. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_AdvancedDataSecurity_Audit.json) |
|[È consigliabile abilitare la sicurezza avanzata dei dati nei server SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fabfb4388-5bf4-4ad7-ba82-2cd2f41ceae9) |Controlla server SQL senza Sicurezza dei dati avanzata |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_AdvancedDataSecurity_Audit.json) |

### <a name="ensure-that-vulnerability-assessment-va-is-enabled-on-a-sql-server-by-setting-a-storage-account"></a>Assicurarsi che La valutazione della vulnerabilità (VA) sia abilitata in un server SQL impostando un account di archiviazione

**ID:** CIS Azure 4.2.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La valutazione della vulnerabilità deve essere abilitata nell'istanza gestita di SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1b7aa243-30e4-4c9e-bca8-d0d3022b634a) |Controlla ogni istanza gestita di SQL in cui non sono abilitate analisi di valutazione della vulnerabilità ricorrenti. La valutazione della vulnerabilità consente di individuare, monitorare e risolvere le potenziali vulnerabilità del database. |AuditIfNotExists, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnManagedInstance_Audit.json) |
|[È consigliabile abilitare la valutazione della vulnerabilità nei server SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fef2a8f2a-b3d9-49cd-a8a8-9a3aaaf647d9) |Controlla i server di Azure SQL in cui non sono abilitate analisi di valutazione della vulnerabilità ricorrenti. La valutazione della vulnerabilità consente di individuare, monitorare e risolvere le potenziali vulnerabilità del database. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/VulnerabilityAssessmentOnServer_Audit.json) |

### <a name="ensure-that-va-setting-send-scan-reports-to-is-configured-for-a-sql-server"></a>Assicurarsi che l'impostazione dell'utilità di analisi Invia report a sia configurata per un server SQL

**ID:** CIS Azure 4.2.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le impostazioni di Valutazione della vulnerabilità per SQL Server devono contenere un indirizzo di posta elettronica per ricevere i report di analisi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057d6cfe-9c4f-4a6d-bc60-14420ea1f1a9) |Assicurarsi di aver specificato un indirizzo di posta elettronica nel campo 'Invia report di analisi a' nelle impostazioni di Valutazione della vulnerabilità. Questo indirizzo di posta elettronica riceverà il riepilogo dei risultati dell'analisi dopo l'esecuzione di un'analisi periodica nei server SQL. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_VulnerabilityAssessmentEmails_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>Assicurarsi che il criterio "Imponi connessione SSL" sia abilitato per il server di database PostgreSQL

**ID:** CIS Azure 4.3.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il criterio Imponi connessione SSL deve essere abilitato per i server di database MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |Database di Azure per il server MySQL supporta la connessione alle applicazioni client tramite Secure Sockets Layer (SSL). L'imposizione di connessioni SSL tra il server di database e le applicazioni client garantisce la protezione da attacchi 'man in the middle' tramite la crittografia del flusso di dati tra il server e l'applicazione. Questa configurazione impone che SSL sia sempre abilitato per l'accesso al server di database. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |

### <a name="ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>Assicurarsi che il criterio "Imponi connessione SSL" sia abilitato per il server di database MySQL

**ID:** CIS Azure 4.3.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Il criterio Imponi connessione SSL deve essere abilitato per i server di database PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd158790f-bfb0-486c-8631-2dc6b4e8e6af) |Il database di Azure per PostgreSQL supporta la connessione del server di database di Azure per PostgreSQL alle applicazioni client tramite SSL (Secure Sockets Layer). L'imposizione di connessioni SSL tra il server di database e le applicazioni client garantisce la protezione da attacchi 'man in the middle' tramite la crittografia del flusso di dati tra il server e l'applicazione. Questa configurazione impone che SSL sia sempre abilitato per l'accesso al server di database. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableSSL_Audit.json) |

### <a name="ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>Assicurarsi che il parametro del server "log_checkpoints" sia impostato su "Sì" per il server di database PostgreSQL

**ID:** CIS Azure 4.3.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'impostazione di registrazione dei punti di controllo deve essere abilitata per i server di database PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e43d) |Questo criterio consente di controllare i database PostgreSQL nell'ambiente corrente in cui l'impostazione log_checkpoints non è abilitata. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogCheckpoint_Audit.json) |

### <a name="ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>Assicurarsi che il parametro del server "log_connections" sia impostato su "Sì" per il server di database PostgreSQL

**ID:** CIS Azure 4.3.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'impostazione di registrazione delle connessioni deve essere abilitata per i server di database PostgreSQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e442) |Questo criterio consente di controllare i database PostgreSQL nell'ambiente corrente in cui l'impostazione log_connections non è abilitata. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogConnections_Audit.json) |

### <a name="ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>Assicurarsi che il parametro del server "log_disconnections" sia impostato su "Sì" per il server di database PostgreSQL

**ID:** CIS Azure 4.3.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'impostazione di registrazione delle disconnessioni deve essere abilitata per i server di database PostgreSQL.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feb6f77b9-bd53-4e35-a23d-7f65d5f0e446) |Questo criterio consente di controllare i database PostgreSQL nell'ambiente corrente in cui l'impostazione log_disconnections non è abilitata. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_EnableLogDisconnections_Audit.json) |

### <a name="ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>Assicurarsi che il parametro del server "connection_throttling" sia impostato su "Sì" per il server di database PostgreSQL

**ID:** CIS Azure 4.3.6 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[La limitazione delle connessioni per i server di database PostgreSQL deve essere abilitata](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5345bb39-67dc-4960-a1bf-427e16b9a0bd) |Questo criterio consente di controllare gli eventuali database PostgreSQL per cui non è abilitata la limitazione delle connessioni nell'ambiente corrente. Questa impostazione abilita la limitazione delle connessioni temporanea per indirizzo IP nel caso di un numero eccessivo di errori di accesso con password non valida. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/PostgreSQL_ConnectionThrottling_Enabled_Audit.json) |

### <a name="ensure-that-azure-active-directory-admin-is-configured"></a>Assicurarsi che l'amministratore di Azure Active Directory sia configurato

**ID**: CIS Azure 4.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile effettuare il provisioning di un amministratore di Azure Active Directory per i server SQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1f314764-cb73-4fc9-b863-8eca98ac36e9) |Controlla il provisioning di un amministratore di Azure Active Directory per il server SQL per abilitare l'autenticazione di Azure AD. L'autenticazione di Azure AD consente una gestione semplificata delle autorizzazioni e una gestione centralizzata delle identità di utenti di database e di altri servizi Microsoft |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SQL_DB_AuditServerADAdmins_Audit.json) |

### <a name="ensure-sql-servers-tde-protector-is-encrypted-with-customer-managed-key"></a>Assicurarsi che la protezione TDE di SQL Server sia crittografata con la chiave gestita dal cliente

**ID**: CIS Azure 4.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le istanze gestite di SQL devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F048248b0-55cd-46da-b1ff-39efd52db260) |L'implementazione di Transparent Data Encryption con la chiave personale offre maggiore trasparenza e controllo su TDE Protector, sicurezza avanzata con un servizio esterno supportato dal modulo di protezione hardware e la promozione della separazione dei compiti. Questa raccomandazione si applica alle organizzazioni con un requisito di conformità correlato. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlManagedInstance_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |
|[I server SQL devono usare chiavi gestite dal cliente per la crittografia dei dati inattivi](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0d134df8-db83-46fb-ad72-fe0c9428c8dd) |L'implementazione di Transparent Data Encryption con la chiave personale offre maggiore trasparenza e controllo su TDE Protector, sicurezza avanzata con un servizio esterno supportato dal modulo di protezione hardware e la promozione della separazione dei compiti. Questa raccomandazione si applica alle organizzazioni con un requisito di conformità correlato. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/SqlServer_EnsureServerTDEisEncryptedWithYourOwnKey_Audit.json) |

## <a name="logging-and-monitoring"></a>Registrazione e monitoraggio

### <a name="ensure-the-storage-container-storing-the-activity-logs-is-not-publicly-accessible"></a>Assicurarsi che il contenitore di archiviazione che archivia i log attività non sia accessibile pubblicamente

**ID**: CIS Azure 5.1.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'accesso pubblico agli account di archiviazione non deve essere consentito](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4fa4b6c0-31ca-4c0d-b10d-24b96f62a751) |L'accesso in lettura pubblico anonimo a contenitori e BLOB in Archiviazione di Azure è un metodo pratico per condividere i dati, ma potrebbe comportare rischi per la sicurezza. Per evitare violazioni dei dati provocate da accesso anonimo indesiderato, Microsoft consiglia di impedire l'accesso pubblico a un account di archiviazione, a meno che non sia richiesto dallo scenario. |Audit, Deny, Disabled |[2.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Storage/ASC_Storage_DisallowPublicBlobAccess_Audit.json) |

### <a name="ensure-the-storage-account-containing-the-container-with-activity-logs-is-encrypted-with-byok-use-your-own-key"></a>Assicurarsi che l'account di archiviazione contenente il contenitore con i log attività sia crittografato con BYOK (Bring Your Own Key)

**ID**: CIS Azure 5.1.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'account di archiviazione che include il contenitore con i log attività deve essere crittografato tramite BYOK](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffbb99e8e-e444-4da0-9ff1-75c92f5a85b2) |Questo criterio verifica se l'account di archiviazione che include il contenitore con i log attività è crittografato tramite BYOK. Il criterio funziona solo se l'account di archiviazione risiede nella stessa sottoscrizione dei log attività per impostazione predefinita. Per altre informazioni sulla crittografia dei dati inattivi in Archiviazione di Azure, vedere [https://aka.ms/azurestoragebyok](https://aka.ms/azurestoragebyok).  |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_StorageAccountBYOK_Audit.json) |

### <a name="ensure-that-logging-for-azure-keyvault-is-enabled"></a>Assicurarsi che la registrazione per l'insieme di credenziali delle credenziali di Azure sia abilitata

**ID:** CIS Azure 5.1.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I log delle risorse Key Vault devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Controllare l'abilitazione dei log delle risorse. consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-policy-assignment"></a>Assicurarsi che esista un avviso del log attività per l'assegnazione di criteri di creazione

**ID**: CIS Azure 5.2.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni dei criteri specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Questo criterio controlla operazioni dei criteri specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-policy-assignment"></a>Assicurarsi che esista un avviso del log attività per l'assegnazione di criteri di eliminazione

**ID**: CIS Azure 5.2.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni dei criteri specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc5447c04-a4d7-4ba8-a263-c9ee321a6858) |Questo criterio controlla operazioni dei criteri specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_PolicyOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group"></a>Assicurarsi che esista un avviso del log attività per la creazione o l'aggiornamento del gruppo di sicurezza di rete

**ID**: CIS Azure 5.2.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni amministrative specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Questo criterio controlla operazioni amministrative specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-network-security-group"></a>Assicurarsi che esista un avviso del log attività per l'eliminazione del gruppo di sicurezza di rete

**ID**: CIS Azure 5.2.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni amministrative specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Questo criterio controlla operazioni amministrative specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-network-security-group-rule"></a>Assicurarsi che esista un avviso del log attività per la creazione o l'aggiornamento di una regola del gruppo di sicurezza di rete

**ID**: CIS Azure 5.2.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni amministrative specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Questo criterio controlla operazioni amministrative specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-the-delete-network-security-group-rule"></a>Assicurarsi che esista un avviso del log attività per l'eliminazione di una regola del gruppo di sicurezza di rete

**ID**: CIS Azure 5.2.6 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni amministrative specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Questo criterio controlla operazioni amministrative specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-security-solution"></a>Assicurarsi che esista un avviso del log attività per la creazione o l'aggiornamento di una soluzione di sicurezza

**ID**: CIS Azure 5.2.7 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni di sicurezza specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Questo criterio controlla operazioni di sicurezza specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-delete-security-solution"></a>Assicurarsi che esista un avviso del log attività per l'eliminazione di una soluzione di sicurezza

**ID**: CIS Azure 5.2.8 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni di sicurezza specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3b980d31-7904-4bb7-8575-5665739a8052) |Questo criterio controlla operazioni di sicurezza specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_SecurityOperations_Audit.json) |

### <a name="ensure-that-activity-log-alert-exists-for-create-or-update-or-delete-sql-server-firewall-rule"></a>Assicurarsi che esista un avviso del log attività per la creazione, l'aggiornamento o l'eliminazione di una regola del firewall di SQL Server

**ID**: CIS Azure 5.2.9 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Per operazioni amministrative specifiche deve esistere un avviso del log attività](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb954148f-4c11-4c38-8221-be76711e194a) |Questo criterio controlla operazioni amministrative specifiche per cui non sono configurati avvisi del log attività. |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/ActivityLog_AdministrativeOperations_Audit.json) |

### <a name="ensure-that-diagnostic-logs-are-enabled-for-all-services-which-support-it"></a>Assicurarsi che i log di diagnostica siano abilitati per tutti i servizi che lo supportano.

**ID:** CIS Azure 5.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I log di diagnostica devono essere abilitati in Servizi app](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb607c5de-e7d9-4eee-9e5c-83f1bcee4fa0) |Controlla l'abilitazione dei log di diagnostica nell'app. consentendo di ricreare la traccia delle attività ai fini di controllo se si verifica un problema di sicurezza o la rete viene compromessa |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditLoggingMonitoring_Audit.json) |
|[I log delle risorse in Azure Data Lake Store devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F057ef27e-665e-4328-8ea3-04b3122bd9fb) |Controllare l'abilitazione dei log delle risorse. consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeStore_AuditDiagnosticLog_Audit.json) |
|[I log delle risorse Analisi di flusso di Azure devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9be5368-9bf5-4b84-9e0a-7850da98bb46) |Controlla l'abilitazione dei log delle risorse. consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Stream%20Analytics/StreamAnalytics_AuditDiagnosticLog_Audit.json) |
|[I log delle risorse negli account Batch devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Controlla l'abilitazione dei log delle risorse. consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json) |
|[I log delle risorse Data Lake Analytics devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc95c74d9-38fe-4f0d-af86-0c7d626a315c) |Controlla l'abilitazione dei log delle risorse. consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Lake/DataLakeAnalytics_AuditDiagnosticLog_Audit.json) |
|[I log delle risorse nell'hub eventi devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Controlla l'abilitazione dei log delle risorse. consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json) |
|[I log delle risorse nell'hub IoT devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F383856f8-de7f-44a2-81fc-e5135b5c2aa4) |Controlla l'abilitazione dei log delle risorse. consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[3.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Internet%20of%20Things/IoTHub_AuditDiagnosticLog_Audit.json) |
|[I log delle risorse Key Vault devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fcf820ca0-f99e-4f3e-84fb-66e913812d21) |Controlla l'abilitazione dei log delle risorse. consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |
|[I log delle risorse in App per la logica devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F34f95f76-5386-4de7-b824-0d8478470c9d) |Controlla l'abilitazione dei log delle risorse. consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Logic%20Apps/LogicApps_AuditDiagnosticLog_Audit.json) |
|[I log delle risorse nei servizi di ricerca devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4330a05-a843-4bc8-bf9a-cacce50c67f4) |Controlla l'abilitazione dei log delle risorse. consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Search/Search_AuditDiagnosticLog_Audit.json) |
|[I log delle risorse nel bus di servizio devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Controlla l'abilitazione dei log delle risorse. consentendo di ricreare la traccia delle attività da usare a fini di controllo se si verifica un problema di sicurezza o se la rete viene compromessa |AuditIfNotExists, Disabled |[4.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json) |
|[I log delle risorse nei set di scalabilità di macchine virtuali devono essere abilitati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7c1b1214-f927-48bf-8882-84f0af6588b1) |È consigliabile abilitare i log per poter ricreare la traccia delle attività quando sono necessarie indagini in caso di evento imprevisto o compromissione. |AuditIfNotExists, Disabled |[2.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |

## <a name="networking"></a>Rete

### <a name="ensure-that-rdp-access-is-restricted-from-the-internet"></a>Assicurarsi che l'accesso RDP sia limitato da Internet

**ID**: CIS Azure 6.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'accesso RDP da Internet deve essere bloccato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe372f825-a257-4fb8-9175-797a8a8627d6) |Questo criterio controlla le regole di sicurezza di rete che consentono l'accesso RDP da Internet |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_RDPAccess_Audit.json) |

### <a name="ensure-that-ssh-access-is-restricted-from-the-internet"></a>Assicurarsi che l'accesso SSH sia limitato da Internet

**ID**: CIS Azure 6.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'accesso SSH da Internet deve essere bloccato](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fab) |Questo criterio controlla le regole di sicurezza di rete che consentono l'accesso SSH da Internet |Audit, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkSecurityGroup_SSHAccess_Audit.json) |

### <a name="ensure-that-network-watcher-is-enabled"></a>Assicurarsi che Network Watcher sia abilitato

**ID**: CIS Azure 6.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile abilitare Network Watcher](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb6e2945c-0b7b-40f5-9233-7a5323b5cdc6) |Network Watcher è un servizio a livello di area che permette di monitorare e diagnosticare le condizioni al livello di scenario di rete da, verso e all'interno di Azure. Il monitoraggio a livello di scenario permette di diagnosticare i problemi in una visualizzazione completa a livello di rete. Gli strumenti di visualizzazione e diagnostica di rete disponibili in Network Watcher permettono di comprendere, diagnosticare e ottenere informazioni dettagliate sulla rete in Azure. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/NetworkWatcher_Enabled_Audit.json) |

## <a name="virtual-machines"></a>Macchine virtuali

### <a name="ensure-virtual-machines-are-utilizing-managed-disks"></a>Assicurarsi che le macchine virtuali Managed Disks

**ID**: CIS Azure 7.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Controlla macchine virtuali che non usano dischi gestiti](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |Questo criterio controlla le macchine virtuali che non usano dischi gestiti |controllo |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |

### <a name="ensure-that-os-and-data-disks-are-encrypted-with-cmk"></a>Assicurarsi che i dischi del sistema operativo e dei dati siano crittografati con la chiave CMK

**ID**: CIS Azure 7.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile applicare la crittografia del disco nelle macchine virtuali](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0961003e-5a0a-4549-abde-af6a37f2724d) |Le macchine virtuali per cui non è abilitata la crittografia dei dischi verranno monitorate dal Centro sicurezza di Azure che invierà raccomandazioni. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_UnencryptedVMDisks_Audit.json) |

### <a name="ensure-that-unattached-disks-are-encrypted-with-cmk"></a>Assicurarsi che i dischi non associati siano crittografati con CMK

**ID**: CIS Azure 7.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[È consigliabile crittografare i dischi non collegati](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2c89a2e5-7285-40fe-afe0-ae8654b92fb2) |Questo criterio controlla i dischi non collegati in cui non è abilitata la crittografia. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |

### <a name="ensure-that-only-approved-extensions-are-installed"></a>Assicurarsi che siano installate solo le estensioni approvate

**ID**: CIS Azure 7.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Devono essere installate solo le estensioni macchina virtuale approvate](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc0e996f8-39cf-4af9-9f45-83fbde810432) |Questo criterio regolamenta le estensioni macchina virtuale non approvate. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |

### <a name="ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>Assicurarsi che vengano applicate le patch più recenti del sistema operativo per tutte le macchine virtuali

**ID**: CIS Azure 7.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Gli aggiornamenti di sistema devono essere installati nelle macchine](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86b3d65f-7626-441e-b690-81a8b71cff60) |Gli aggiornamenti di sistema per la sicurezza nei server verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti |AuditIfNotExists, Disabled |[4.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingSystemUpdates_Audit.json) |

### <a name="ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>Assicurarsi che venga installata la protezione endpoint per tutte le Macchine virtuali di Microsoft Azure

**ID**: CIS Azure 7.6 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Monitorare il server senza Endpoint Protection nel Centro sicurezza di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf6cd1bd-1635-48cb-bde7-5b15693900b9) |I server in cui non è installato un agente di Endpoint Protection verranno monitorati dal Centro sicurezza di Azure che invierà i consigli corrispondenti |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_MissingEndpointProtection_Audit.json) |

## <a name="other-security-considerations"></a>Altre considerazioni sulla sicurezza

### <a name="ensure-that-the-expiration-date-is-set-on-all-keys"></a>Assicurarsi che la data di scadenza sia impostata su tutte le chiavi

**ID:** CIS Azure 8.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Le chiavi degli insiemi di credenziali delle chiavi devono avere una data di scadenza](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F152b15f7-8e1f-4c1f-ab71-8c010ba5dbc0) |Le chiavi di crittografia devono avere una data di scadenza definita e non devono essere permanenti. Le chiavi sempre valide offrono a un potenziale utente malintenzionato più tempo per comprometterle. Impostare le date di scadenza per le chiavi di crittografia è una procedura di sicurezza consigliata. |Audit, Deny, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Keys_ExpirationSet.json) |

### <a name="ensure-that-the-expiration-date-is-set-on-all-secrets"></a>Assicurarsi che la data di scadenza sia impostata su tutti i segreti

**ID:** CIS Azure 8.2 **Proprietà:** Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[I segreti degli insiemi di credenziali delle chiavi devono avere una data di scadenza](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F98728c90-32c7-4049-8429-847dc0f4fe37) |I segreti devono avere una data di scadenza definita e non devono essere permanenti. I segreti validi a tempo indefinito forniscono ai potenziali utenti malintenzionati più tempo per comprometterli. Impostare le date di scadenza per i segreti è una procedura di sicurezza consigliata. |Audit, Deny, Disabled |[1.0.1-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/Secrets_ExpirationSet.json) |

### <a name="ensure-the-key-vault-is-recoverable"></a>Assicurarsi che l'insieme di credenziali delle chiavi sia recuperabile

**ID**: CIS Azure 8.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Negli insiemi di credenziali delle chiavi deve essere abilitata la protezione dalla rimozione definitiva](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b60c0b2-2dc2-4e1c-b5c9-abbed971de53) |L'eliminazione dolosa di un insieme di credenziali delle chiavi può causare la perdita permanente di dati. Un utente malintenzionato interno all'organizzazione può potenzialmente eliminare e rimuovere definitivamente gli insiemi di credenziali delle chiavi. La protezione dalla rimozione definitiva consente di rispondere a questi tipi di attacco imponendo un periodo di conservazione obbligatorio per gli insiemi di credenziali delle chiavi eliminati temporaneamente. Nessuno all'interno dell'organizzazione né Microsoft sarà in grado di rimuovere definitivamente gli insiemi di credenziali delle chiavi durante il periodo di conservazione associato all'eliminazione temporanea. |Audit, Deny, Disabled |[1.1.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |

### <a name="enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>Abilitare il controllo degli accessi in base al ruolo con il servizio Azure Kubernetes

**ID**: CIS Azure 8.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Nei servizi Kubernetes deve essere usato il controllo degli accessi in base al ruolo](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fac4a19c2-fa67-49b4-8ae5-0b2e78c49457) |Per garantire un filtro granulare per le azioni che gli utenti possono eseguire, usare il controllo degli accessi in base al ruolo per gestire le autorizzazioni nei cluster del servizio Kubernetes e configurare i criteri di autorizzazione pertinenti. |Audit, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Security%20Center/ASC_EnableRBAC_KubernetesService_Audit.json) |

## <a name="app-service"></a>Servizio app

### <a name="ensure-app-service-authentication-is-set-on-azure-app-service"></a>Assicurarsi che l'autenticazione del servizio app sia impostata nel servizio app di Azure

**ID**: CIS Azure 9.1 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'autenticazione deve essere abilitata nell'app per le API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4ebc54a-46e1-481a-bee2-d4411e95d828) |L'autenticazione del Servizio app di Azure è una funzionalità che può impedire alle richieste HTTP anonime di raggiungere l'app per le API o autenticare coloro che dispongono dei token prima che raggiungano l'app per le API |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_ApiApp_Audit.json) |
|[L'autenticazione deve essere abilitata nell'app per le funzioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc75248c1-ea1d-4a9c-8fc9-29a6aabd5da8) |L'autenticazione del Servizio app di Azure è una funzionalità che può impedire alle richieste HTTP anonime di raggiungere l'app per le funzioni o autenticare coloro che dispongono dei token prima che raggiungano l'app per le funzioni |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_functionapp_Audit.json) |
|[L'autenticazione deve essere abilitata nell'app Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95bccee9-a7f8-4bec-9ee9-62c3473701fc) |L'autenticazione del Servizio app di Azure è una funzionalità che può impedire alle richieste HTTP anonime di raggiungere l'app Web o autenticare coloro che dispongono dei token prima che raggiungano l'app Web |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Authentication_WebApp_Audit.json) |

### <a name="ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>Assicurarsi che l'app Web reindirizzi tutto il traffico HTTP a HTTPS nel servizio app Azure

**ID**: CIS Azure 9.2 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'applicazione Web deve essere accessibile solo tramite HTTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa4af4a39-4135-47fb-b175-47fbdf85311d) |L'uso di HTTPS assicura l'autenticazione di server/servizi e protegge i dati in transito da attacchi di intercettazione a livello rete. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppServiceWebapp_AuditHTTP_Audit.json) |

### <a name="ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>Assicurarsi che l'app Web usi la versione più recente della crittografia TLS

**ID**: CIS Azure 9.3 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Nell'app per le API è necessario usare la versione più recente di TLS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8cb6aa8b-9e41-4f4e-aa25-089a7ac2581e) |Esegui l'aggiornamento alla versione più recente di TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_ApiApp_Audit.json) |
|[Nell'app per le funzioni è necessario usare la versione più recente di TLS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff9d614c5-c173-4d56-95a7-b4437057d193) |Esegui l'aggiornamento alla versione più recente di TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_FunctionApp_Audit.json) |
|[Nell'app Web è necessario usare la versione più recente di TLS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff0e6e85b-9b9f-4a4b-b67b-f730d42f1b0b) |Esegui l'aggiornamento alla versione più recente di TLS |AuditIfNotExists, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_RequireLatestTls_WebApp_Audit.json) |

### <a name="ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>Assicurarsi che l'opzione "Certificati client (certificati client in ingresso)" dell'app Web sia impostata su "Sì"

**ID**: CIS Azure 9.4 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Assicurarsi che l'opzione "Certificati client (certificati client in ingresso)" dell'app per le API sia impostata su 'Sì'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0c192fe8-9cbb-4516-85b3-0ade8bd03886) |I certificati client consentono all'app di richiedere un certificato per le richieste in ingresso. Solo i client con un certificato valido potranno raggiungere l'app. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_ClientCert.json) |
|[Assicurarsi che l'opzione "Certificati client (certificati client in ingresso)" dell'app Web sia impostata su 'Sì'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5bb220d9-2698-4ee4-8404-b9c30c9df609) |I certificati client consentono all'app di richiedere un certificato per le richieste in ingresso. Solo i client con un certificato valido potranno raggiungere l'app. |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_ClientCert.json) |
|[Per le app per le funzioni deve essere abilitata l'opzione 'Certificati client (certificati client in ingresso)'](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Feaebaea7-8013-4ceb-9d14-7eb32271373c) |I certificati client consentono all'app di richiedere un certificato per le richieste in ingresso. Solo i client con certificati validi potranno raggiungere l'app. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_ClientCert.json) |

### <a name="ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>Assicurarsi che l'opzione Registra con Azure Active Directory sia abilitata nel Servizio app

**ID**: CIS Azure 9.5 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Nell'app per le API è necessario usare un'identità gestita](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4d441f8-f9d9-4a9e-9cef-e82117cb3eef) |Usare un'identità gestita per la protezione avanzata dell'autenticazione |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_ApiApp_Audit.json) |
|[Nell'app per le funzioni è necessario usare un'identità gestita](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0da106f2-4ca3-48e8-bc85-c638fe6aea8f) |Usare un'identità gestita per la protezione avanzata dell'autenticazione |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_FunctionApp_Audit.json) |
|[Nell'app Web è necessario usare un'identità gestita](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2b9ad585-36bc-4615-b300-fd4435808332) |Usare un'identità gestita per la protezione avanzata dell'autenticazione |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_UseManagedIdentity_WebApp_Audit.json) |

### <a name="ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>Assicurarsi che la "versione di PHP" sia la più recente, se usata per eseguire l'app Web

**ID**: CIS Azure 9.6 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app per le API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1bc1795e-d44a-4d48-9b3b-6fff0fd5f9ba) |Periodicamente, per il software PHP vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di PHP più recente per le app per le API per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_PHP_Latest.json) |
|[Assicurarsi che la 'versione di PHP' sia la più recente, se usata come parte dell'app Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7261b898-8a84-4db8-9e04-18527132abb3) |Periodicamente, per il software PHP vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di PHP più recente per le app Web per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_Webapp_Audit_PHP_Latest.json) |

### <a name="ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>Assicurarsi che la "versione di Python" sia la più recente, se usata per eseguire l'app Web

**ID**: CIS Azure 9.7 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app per le API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F74c3584d-afae-46f7-a20a-6f8adba71a16) |Periodicamente, per il software Python vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di Python più recente per le app per le API per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_python_Latest.json) |
|[Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app per le funzioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7238174a-fd10-4ef0-817e-fc820a951d73) |Periodicamente, per il software Python vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di Python più recente per le app per le funzioni per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_python_Latest.json) |
|[Assicurarsi che la 'versione di Python' sia la più recente, se usata come parte dell'app Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7008174a-fd10-4ef0-817e-fc820a951d73) |Periodicamente, per il software Python vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di Python più recente per le app Web per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[3.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_python_Latest.json) |

### <a name="ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>Assicurarsi che la "versione di Java" sia la più recente, se usata per eseguire l'app Web

**ID**: CIS Azure 9.8 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app per le API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F88999f4c-376a-45c8-bcb3-4058f713cf39) |Periodicamente, per Java vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di Python più recente per le app per le API per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_java_Latest.json) |
|[Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app per le funzioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9d0b6ea4-93e2-4578-bf2f-6bb17d22b4bc) |Periodicamente, per il software Java vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di Java più recente per le app per le funzioni per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_java_Latest.json) |
|[Assicurarsi che la 'versione di Java' sia la più recente, se usata come parte dell'app Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F496223c3-ad65-4ecd-878a-bae78737e9ed) |Periodicamente, per il software Java vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di Java più recente per le app Web per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_java_Latest.json) |

### <a name="ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>Assicurarsi che la "versione di HTTP" sia la più recente, se usata per eseguire l'app Web

**ID**: CIS Azure 9.9 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app per le API](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F991310cd-e9f3-47bc-b7b6-f57b557d07db) |Periodicamente, per HTTP vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di HTTP più recente per le app Web per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_ApiApp_Audit_HTTP_Latest.json) |
|[Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app per le funzioni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe2c1c086-2d84-4019-bff3-c44ccd95113c) |Periodicamente, per HTTP vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di HTTP più recente per le app Web per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_FunctionApp_Audit_HTTP_Latest.json) |
|[Assicurarsi che la 'versione di HTTP' sia la più recente, se usata per eseguire l'app Web](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F8c122334-9d20-4eb8-89ea-ac9a705b74ae) |Periodicamente, per HTTP vengono rilasciate versioni più recenti, sia a causa di difetti di sicurezza o per includere funzionalità aggiuntive. È consigliabile usare la versione di HTTP più recente per le app Web per poter sfruttare le eventuali correzioni per la sicurezza e/o le nuove funzionalità dell'ultima versione. Attualmente, questo criterio si applica solo alle app Web Linux. |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_WebApp_Audit_HTTP_Latest.json) |

### <a name="ensure-ftp-deployments-are-disabled"></a>Assicurarsi che le distribuzioni FTP siano disabilitate

**ID**: CIS Azure 9.10 **Proprietà**: Customer

|Nome<br /><sub>(Portale di Azure)</sub> |Descrizione |Effetto/i |Versione<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[L'app per le API deve usare solo FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9a1b8c48-453a-4044-86c3-d8bfd823e4f5) |Abilita imposizione FTPS per la protezione avanzata |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_ApiApp_Audit.json) |
|[L'app per le funzioni deve usare solo FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F399b2637-a50f-4f95-96f8-3a145476eb15) |Abilita imposizione FTPS per la protezione avanzata |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_FunctionApp_Audit.json) |
|[L'app Web deve usare FTPS](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4d24b6d4-5e53-4a4f-a7f4-618fa573ee4b) |Abilita imposizione FTPS per la protezione avanzata |AuditIfNotExists, Disabled |[2.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Service/AppService_AuditFTPS_WebApp_Audit.json) |

> [!NOTE]
> La disponibilità di specifiche definizioni di Criteri di Azure può variare in Azure per enti pubblici e in altri cloud nazionali.

## <a name="next-steps"></a>Passaggi successivi

Articoli aggiuntivi su Criteri di Azure:

- Panoramica della [Conformità con le normative](../concepts/regulatory-compliance.md).
- Vedere la [struttura della definizione dell'iniziativa](../concepts/initiative-definition-structure.md).
- Vedere altri esempi in [Esempi di Criteri di Azure](./index.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).
- Informazioni su come [correggere le risorse non conformi](../how-to/remediate-resources.md).
