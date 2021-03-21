---
title: Riferimento allo schema dell'origine dati di Azure Sentinel
description: Questo articolo elenca gli schemi di origini dati di Azure e di terze parti supportati da Azure Sentinel, con collegamenti alla relativa documentazione di riferimento.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: 0cc1a083e1400b5a3167376e374af6c7bf7ab7c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698349"
---
# <a name="data-source-schema-reference"></a>Riferimento allo schema dell'origine dati

Questo articolo elenca gli schemi di origine dati supportati di Azure e di terze parti, con collegamenti alla relativa documentazione di riferimento.

## <a name="azure-data-sources"></a>Origini dati di Azure

| Tipo                             | Origine dati             | Log Analytics TableName | Riferimento dello schema |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Proprietà di accesso dei report attività Azure AD](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Informazioni di riferimento su AuditLogs di monitoraggio di Azure](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Informazioni di riferimento su AzureActivity di monitoraggio di Azure](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | Schemi dell'API attività di gestione di Office 365: <br>- [Schema comune ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Schema di amministrazione di Exchange ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Schema delle cassette postali di Exchange](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [Schema di base di SharePoint](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [Operazioni sui file di SharePoint](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Insieme di credenziali chiave di Azure         | AzureDiagnostics       | [Informazioni di riferimento su AzureDiagnostics di monitoraggio di Azure](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Host**                             | Linux                  | syslog                 | [Informazioni di riferimento su syslog di monitoraggio di Azure](/azure/azure-monitor/reference/tables/syslog) |
| **Network**                          | Log IIS               | W3CIISLog              | [Informazioni di riferimento su W3CIISLog di monitoraggio di Azure](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Network**                          | VMinsights             | VMConnection           | [Informazioni di riferimento su VMConnection di monitoraggio di Azure](/azure/azure-monitor/reference/tables/vmconnection) |
| **Network**                          | Soluzione Wire data     | WireData               | [Informazioni di riferimento su WireData di monitoraggio di Azure](/azure/azure-monitor/reference/tables/wiredata) |
| **Network**                          | Log di flusso NSG          | AzureNetworkAnalytics  | [Schema e aggregazione dei dati in Analisi del traffico](../network-watcher/traffic-analytics-schema.md) |
| | | | |

> [!NOTE]
> Per ulteriori informazioni, vedere l'intero [riferimento ai dati di monitoraggio di Azure](/azure/azure-monitor/reference/).
>
## <a name="3rd-party-vendor-data-sources"></a>origini dati fornitore di terze parti

Nella tabella seguente sono elencati i fornitori di terze parti supportati e la relativa documentazione relativa al mapping di syslog o Common Event Format (CEF) per diversi tipi di log supportati, che contengono i mapping dei campi CEF e i log di esempio per ogni tipo di categoria.

| Tipo |    Vendor |    Prodotto | Log Analytics TableName | Riferimento al mapping dei campi CEF  |
| ----- | ----- | ----- | ----- |----- |
| **Network** | Palo Alto   | SISTEMA OPERATIVO PAN    | CommonSecurityLog |   [Guida all'integrazione del formato degli eventi comuni di Pan-OS 9,0](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) (ricerca di *formati di log CEF*) |
| **Network** | Punto di controllo  |ALL   | CommonSecurityLog | [Descrizione campi log](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Network** | FortiGate   | ALL   | CommonSecurityLog | [Struttura dello schema di log](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Network** | Barracuda | Web application firewall |  CommonSecurityLog   | [Come configurare syslog e altri log](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Network** | Cisco | ASA | CommonSecurityLog | [Messaggi syslog serie Cisco ASA](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Network** | Cisco | Potenza   | CommonSecurityLog | [Messaggi syslog di Cisco fuoco Threat Defense](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.pdf)    |
| **Network** | Cisco   | Ombrello  | Tabella dei log personalizzati  | [Formati di log e controllo delle versioni](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Network**   | Cisco | Meraki    | CommonSecurityLog |   [Tipi di eventi syslog ed esempi di log](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Network**   | Zscaler | Servizio di streaming nano (NSS)|   CommonSecurityLog | [Formattazione di feed NSS](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (solo log di Web, firewall, DNS e tunnel) |
| **Network**   |F5 | BigIP LTM|    CommonSecurityLog|  [Messaggi di evento e tipi di attacco](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Network** | F5  | BigIP ASM|    CommonSecurityLog|  [Registrazione degli eventi di sicurezza dell'applicazione](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Network** | Citrix  |Firewall dell'app Web   | CommonSecurityLog|    [Supporto della registrazione CEF (Common Event Format) nel firewall applicazione](https://support.citrix.com/article/CTX136146) <br>  [Riferimento al messaggio syslog NetScaler 12,0](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Host** |Symantec | Endpoint Protection Manager Symantec (SEPM) | CommonSecurityLog|[Impostazioni di registrazione esterna e livelli di gravità degli eventi di log per Endpoint Protection Manager](https://support.symantec.com/us/en/article.tech171741.html)|
|**Host** |Tendenza Micro |Tutti |CommonSecurityLog | [Mapping del contenuto syslog-CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui connettori sentinella di Azure supportati, ad esempio CEF, syslog, Direct, Agent e connettori personalizzati:

- [Connettere le origini dati](connect-data-sources.md)

- [Azure Sentinel syslog, CEF e altri connettori di terze parti](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)