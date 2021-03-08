---
title: Informazioni di riferimento sui tipi di entità di Azure Sentinel | Microsoft Docs
description: In questo articolo vengono visualizzati i tipi di entità Sentinel di Azure e i relativi identificatori obbligatori.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 17a4df3037f9922d92fca924de0d246458cfa08e
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456305"
---
# <a name="azure-sentinel-entity-types-reference"></a>Riferimento ai tipi di entità di Azure Sentinel

## <a name="entity-types-and-identifiers"></a>Tipi di entità e identificatori

La tabella seguente illustra i **tipi di entità** attualmente disponibili per il mapping in Sentinel di Azure e gli **attributi** disponibili come **identificatori** per ogni tipo di entità, che vengono visualizzati nell'elenco a discesa **identificatori** nella sezione [mapping entità](map-data-fields-to-entities.md) della [creazione guidata regola di analisi](tutorial-detect-threats-custom.md).

Ogni identificatore nella colonna degli **identificatori obbligatori** è minimo necessario per identificare la relativa entità. Tuttavia, un identificatore obbligatorio potrebbe non essere sufficiente per fornire un'identificazione *univoca* . Maggiore è il numero di identificatori usati, maggiore è la probabilità di identificazione univoca. È possibile utilizzare fino a tre identificatori per un mapping di entità singola.

Per ottenere risultati ottimali, per un'identificazione univoca garantita, quando possibile, è consigliabile usare gli identificatori della colonna **identificatori più sicuri** . L'utilizzo di più identificatori sicuri consente la correlazione tra identificatori sicuri da diverse origini dati e schemi. Ciò consente a Azure Sentinel di fornire informazioni dettagliate più complete per una determinata entità.

| Tipo di entità | Identificatori | Identificatori obbligatori | Identificatori più sicuri |
| - | - | - | - |
| [**Account utente**](#user-account)<br>*Account* | Nome<br>FullName<br>NTDomain<br>DnsDomain<br>UPNSuffix<br>Sid<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>DisplayName<br>ObjectGuid | FullName<br>Sid<br>Nome<br>AadUserId<br>PUID<br>ObjectGuid | Nome + NTDomain<br>Nome + UPNSuffix<br>AADUserId<br>Sid |
| [**Host**](#host) | DnsDomain<br>NTDomain<br>HostName<br>FullName<br>NetBiosName<br>AzureID<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined | FullName<br>HostName<br>NetBiosName<br>AzureID<br>OMSAgentID | Nome host + NTDomain<br>Nome host + DnsDomain<br>NetBiosname + NTDomain<br>NetBiosname + DnsDomain<br>AzureID<br>OMSAgentID |
| [**Indirizzo IP**](#ip-address)<br>*IP* | Indirizzo | Indirizzo | |
| [**Malware**](#malware) | Nome<br>Category | Nome | |
| [**File**](#file) | Directory<br>Nome | Nome | |
| [**Processo**](#process) | ProcessId<br>CommandLine<br>ElevationToken<br>CreationTimeUtc | CommandLine<br>ProcessId | |
| [**Applicazione cloud**](#cloud-application)<br>*(CloudApplication)* | AppId<br>Nome<br>InstanceName | AppId<br>Nome | |
| [**Nome di dominio**](#domain-name)<br>*DNS* | DomainName | DomainName | |
| [**Risorsa di Azure**](#azure-resource) | ResourceId | ResourceId | |
| [**Hash file**](#file-hash)<br>*FileHash* | Algoritmo<br>Valore | Algoritmo + valore | |
| [**Chiave del registro di sistema**](#registry-key) | Hive<br>Chiave | Hive<br>Chiave | Hive + chiave |
| [**Valore del Registro di sistema**](#registry-value) | Nome<br>Valore<br>ValueType | Nome | |
| [**Gruppo di sicurezza**](#security-group) | DistinguishedName<br>SID<br>ObjectGuid | DistinguishedName<br>SID<br>ObjectGuid | |
| [**URL**](#url) | URL | URL | |
| [**Dispositivo Internet delle cose**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>Source (Sorgente)<br>SourceRef<br>Produttore<br>Modellare<br>OperatingSystem<br>IpAddress<br>MacAddress<br>Protocolli<br>SerialNumber | IoTHub<br>DeviceId | IoTHub + DeviceId |
| [**Cassetta postale**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>UPN<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**Cluster di posta**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>Minacce<br>Query<br>QueryTime<br>MailCount<br>IsVolumeAnomaly<br>Source (Sorgente)<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup | Query<br>Source (Sorgente) | Query + origine |
| [**Messaggio di posta elettronica**](#mail-message) | Recipient<br>Urls<br>Minacce<br>Mittente<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>ReceivedDate<br>NetworkMessageId<br>InternetMessageId<br>Oggetto<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>DeliveryAction<br>DeliveryLocation<br>Linguaggio<br>ThreatDetectionMethods | NetworkMessageId<br>Recipient | NetworkMessageId + destinatario |
| [**Posta elettronica di invio**](#submission-mail) | SubmissionId<br>SubmissionDate<br>Submitter<br>NetworkMessageId<br>Timestamp<br>Recipient<br>Mittente<br>SenderIp<br>Oggetto<br>ReportType | SubmissionId<br>NetworkMessageId<br>Recipient<br>Submitter |  |
|

## <a name="entity-type-schemas"></a>Schemi di tipi di entità

Di seguito è riportato un esame più approfondito degli schemi completi di ogni tipo di entità. Si noterà che molti di questi schemi includono collegamenti ad altri tipi di entità. ad esempio, lo schema dell'account utente include un collegamento al tipo di entità host, perché un attributo di un account utente è l'host in cui è definito. Queste entità collegate esternamente non possono essere usate come identificatori per il mapping di entità, ma sono molto utili per fornire un quadro completo delle entità nelle pagine delle entità e del grafico di analisi.

> [!NOTE]
> Un punto interrogativo che segue il valore nella colonna **tipo** indica che il campo ammette i valori null.

## <a name="user-account"></a>Account utente

*Nome entità: account*

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | account |
| Nome | string | Nome dell'account. Questo campo deve conservare solo il nome senza aggiungere alcun dominio. |
| *FullName* | *N/D* | *Non parte dello schema, incluso per la compatibilità con le versioni precedenti del mapping delle entità.*
| NTDomain | string | Nome di dominio NETBIOS come visualizzato nel formato di avviso – dominio\nomeutente. Esempi: Finance, NT AUTHORITY |
| DnsDomain | string | Nome DNS di dominio completo. Esempi: finance.contoso.com |
| UPNSuffix | string | Suffisso del nome dell'entità utente per l'account. In alcuni casi si tratta anche del nome di dominio. Esempi: contoso.com |
| Host | Entità | L'host che contiene l'account, se è un account locale. |
| Sid | string | Identificatore di sicurezza dell'account, ad esempio S-1-5-18. |
| AadTenantId | GUID? | ID del tenant di Azure AD, se noto. |
| AadUserId | GUID? | ID dell'oggetto account Azure AD, se noto. |
| PUID | GUID? | ID utente di Passport Azure AD, se noto. |
| IsDomainJoined | Bool? | Determina se si tratta di un account di dominio. |
| DisplayName | string | Nome visualizzato dell'account. |
| ObjectGuid | GUID? | L'attributo objectGUID è un attributo a valore singolo che rappresenta l'identificatore univoco dell'oggetto, assegnato dal Active Directory. |
|

Identificatori sicuri di un'entità account:

- Nome + UPNSuffix
- AadUserId
- SID + host (obbligatorio per SID di account predefiniti)
- SID (eccetto SID di account predefiniti)
- Nome + NTDomain (a meno che NTDomain non sia un dominio predefinito, ad esempio "Workgroup")
- Nome + host (se NTDomain è un dominio predefinito, ad esempio "Workgroup")
- Nome + DnsDomain
- PUID
- ObjectGuid

Identificatori vulnerabili di un'entità account:

- Nome

## <a name="host"></a>Host

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | host |
| DnsDomain | string | Dominio DNS a cui appartiene l'host. Deve contenere il suffisso DNS completo per il dominio, se noto. |
| NTDomain | string | Dominio NT a cui appartiene l'host. |
| HostName | string | Nome host senza il suffisso del dominio. |
| *FullName* | *N/D* | *Non parte dello schema, incluso per la compatibilità con le versioni precedenti del mapping delle entità.*
| NetBiosName | string | Nome host (precedente a Windows 2000). |
| IoTDevice | Entità | L'entità dispositivo Internet (se questo host rappresenta un dispositivo Internet delle cose). |
| AzureID | string | ID della risorsa di Azure della macchina virtuale, se noto. |
| OMSAgentID | string | ID agente OMS, se l'host dispone di un agente OMS installato. |
| OSFamily | Enum? | Uno dei valori seguenti: <li>Linux<li>Windows<li>Android<li>IOS |
| OSVersion | string | Rappresentazione in testo libero del sistema operativo.<br>Questo campo è destinato a mantenere versioni specifiche. i valori sono più specifici di OSFamily o i valori futuri non supportati dall'enumerazione OSFamily. |
| IsDomainJoined | Bool | Determina se l'host appartiene a un dominio. |
|

Identificatori sicuri di un'entità host:
- Nome host + NTDomain
- Nome host + DnsDomain
- NetBiosname + NTDomain
- NetBiosname + DnsDomain
- AzureID
- OMSAgentID
- IoTDevice (non supportato per il mapping di entità)

Identificatori vulnerabili di un'entità host:
- HostName
- NetBiosName

## <a name="ip-address"></a>Indirizzo IP

*Nome entità: IP*

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | IP |
| Indirizzo | string | Indirizzo IP come stringa, ad esempio 127.0.0.1 (in IPv4 o IPv6). |
| Location | GeoLocation | Il contesto della posizione geografica collegato all'entità IP. |
|

Identificatori sicuri di un'entità IP:
- Indirizzo

## <a name="malware"></a>Malware

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | malware |
| Nome | string | Nome del malware da parte del fornitore, ad esempio `Win32/Toga!rfn` . |
| Category | string | Categoria di malware da parte del fornitore, ad esempio Trojan. |
| File | Elenco\<Entity> | Elenco di entità file collegate in cui è stato rilevato il malware. Può contenere le entità file inline o come riferimento.<br>Per ulteriori informazioni sulla struttura, vedere l'entità file. |
| Processi | Elenco\<Entity> | Elenco di entità di processo collegate in cui è stato rilevato il malware. Questa operazione viene spesso usata quando l'avviso viene attivato in un'attività di file.<br>Per ulteriori informazioni sulla struttura, vedere l'entità [Process](#process) . |
|

Identificatori sicuri di un'entità malware:

- Nome + categoria

## <a name="file"></a>File

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | file |
| Directory | string | Percorso completo del file. |
| Nome | string | Nome del file senza percorso (alcuni avvisi potrebbero non includere il percorso). |
| Host | Entità | Host in cui è stato archiviato il file. |
| Filehash | Elenca &lt; entità&gt; | Hash del file associato a questo file. |
|

Identificatori sicuri di un'entità file:
- Nome + directory
- Nome + filehash
- Nome + directory + filehash

## <a name="process"></a>Processo

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | processo |
| ProcessId | string | ID del processo. |
| CommandLine | string | Riga di comando utilizzata per creare il processo. |
| ElevationToken | Enum? | Token di elevazione associato al processo.<br>Valori possibili:<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | DateTime? | Ora di inizio dell'esecuzione del processo. |
| ImageFile | Entità (file) | Può contenere l'entità file inline o come riferimento.<br>Per ulteriori informazioni sulla struttura, vedere l'entità file. |
| Account | Entità | Account che esegue i processi.<br>Può contenere l'entità [account](#user-account) inline o come riferimento.<br>Per ulteriori informazioni sulla struttura, vedere l'entità [account](#user-account) . |
| ParentProcess | Entità (processo) | Entità del processo padre. <br>Può contenere dati parziali, ovvero solo il PID. |
| Host | Entità | Host in cui è in esecuzione il processo. |
| LogonSession | Entità (HostLogonSession) | Sessione in cui è stato eseguito il processo. |
|

Identificatori sicuri di un'entità di processo:

- Host + ProcessId + CreationTimeUtc
- Host + ParentProcessId + CreationTimeUtc + CommandLine
- Host + ProcessId + CreationTimeUtc + ImageFile
- Host + ProcessId + CreationTimeUtc + ImageFile. filehash

Identificatori vulnerabili di un'entità di processo:

- ProcessID + CreationTimeUtc + CommandLine (e nessun host)
- ProcessID + CreationTimeUtc + ImageFile (e nessun host)

## <a name="cloud-application"></a>Applicazione cloud

*Nome entità: CloudApplication*

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | ' Cloud-Application ' |
| AppId | Int | Identificatore tecnico dell'applicazione. Deve essere uno dei valori definiti nell'elenco degli [identificatori dell'applicazione cloud](#cloud-application-identifiers). Il valore per il campo AppId è facoltativo. |
| Nome | string | Nome dell'applicazione cloud correlata. Il valore di nome applicazione è facoltativo. |
| InstanceName | string | Nome dell'istanza definita dall'utente dell'applicazione cloud. Viene spesso usato per distinguere tra più applicazioni dello stesso tipo di un cliente. |
|

Identificatori sicuri di un'entità applicazione cloud:
 - AppId (senza NomeIstanza)
 - Nome (senza NomeIstanza)
 - AppId + NomeIstanza
 - Nome + nomeistanza

## <a name="domain-name"></a>Nome di dominio

*Nome entità: DNS*

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | DNS |
| DomainName | string | Nome del record DNS associato all'avviso. |
| IpAddress | Elenca &lt; entità (IP)&gt; | Entità corrispondenti agli indirizzi IP risolti. |
| DnsServerIp | Entità (IP) | Entità che rappresenta il server DNS che risolve la richiesta. |
| HostIpAddress | Entità (IP) | Entità che rappresenta il client della richiesta DNS. |
|

Identificatori sicuri di un'entità DNS:
- NomeDominio + DnsServerIp + HostIpAddress

Identificatori vulnerabili di un'entità DNS:
- NomeDominio + HostIpAddress

## <a name="azure-resource"></a>Risorsa di Azure

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | ' Azure-Resource ' |
| ResourceId | string | ID risorsa di Azure della risorsa. |
| SubscriptionId | string | L'ID sottoscrizione della risorsa. |
| TryGetResourceGroup | Bool | Il valore del gruppo di risorse, se esistente. |
| TryGetProvider | Bool | Valore del provider, se esistente. |
| TryGetName | Bool | Valore del nome, se esistente. |
|

Identificatori sicuri di un'entità di risorse di Azure:
- ResourceId

## <a name="file-hash"></a>Hash file

*Nome entità: filehash*

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | filehash |
| Algoritmo | Enumerazione | Tipo di algoritmo hash. Valori possibili:<li>Sconosciuto<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| Valore | string | Valore hash. |
|

Identificatori sicuri di un'entità hash file:
- Algoritmo + valore

## <a name="registry-key"></a>Chiave del Registro di sistema

*Nome entità: RegistryKey*

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | ' Registry-Key ' |
| Hive | Enum? | Uno dei valori seguenti:<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| Chiave | string | Percorso della chiave del registro di sistema. |
|

Identificatori sicuri di un'entità chiave del registro di sistema:
- Hive + chiave

## <a name="registry-value"></a>Valore del Registro di sistema

*Nome entità: RegistryValue*

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | ' Registry-Value ' |
| Chiave | Entity (RegistryKey) | Entità chiave del registro di sistema. |
| Nome | string | Nome del valore del registro di sistema. |
| Valore | string | Rappresentazione in formato stringa dei dati del valore. |
| ValueType | Enum? | Uno dei valori seguenti:<li>string<li>Binary<li>DWord<li>QWORD<li>MultiString<li>ExpandString<li>nessuno<li>Sconosciuto<br>I valori devono essere conformi all'enumerazione Microsoft. Win32. RegistryValueKind. |
|

Identificatori sicuri di un'entità valore del registro di sistema:
- Chiave + nome

Identificatori vulnerabili di un'entità del valore del registro di sistema:
- Nome (senza chiave)

## <a name="security-group"></a>Gruppo di protezione

*Nome entità: SecurityGroup*

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | ' Security-Group ' |
| DistinguishedName | string | Nome distinto del gruppo. |
| SID | string | L'attributo SID è un attributo a valore singolo che specifica l'ID di sicurezza (SID) del gruppo. |
| ObjectGuid | GUID? | L'attributo objectGUID è un attributo a valore singolo che rappresenta l'identificatore univoco dell'oggetto, assegnato dal Active Directory. |
|

Identificatori sicuri di un'entità del gruppo di sicurezza:
 - DistinguishedName
 - SID
 - ObjectGuid

## <a name="url"></a>URL

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | URL |
| URL | Uri | URL completo a cui fa riferimento l'entità. |
|

Identificatori sicuri di un'entità URL:
- URL (quando un URL assoluto)

Identificatori vulnerabili di un'entità URL:
- URL (quando un URL relativo)

## <a name="iot-device"></a>Dispositivo IoT

*Nome entità: IoTDevice*

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | 'iotdevice' |
| IoTHub | Entità (AzureResource) | Entità AzureResource che rappresenta l'hub delle cose a cui appartiene il dispositivo. |
| DeviceId | string | ID del dispositivo nel contesto dell'hub Internet. |
| DeviceName | string | Nome descrittivo del dispositivo. |
| IoTSecurityAgentId | GUID? | ID del *difensore per* l'agente Internet delle cose in esecuzione nel dispositivo. |
| DeviceType | string | Il tipo di dispositivo ("sensore di temperatura", "freezer", "turbina eolica" e così via). |
| Source (Sorgente) | string | Origine (Microsoft/Vendor) dell'entità dispositivo. |
| SourceRef | Entità (URL) | Un riferimento URL all'elemento di origine in cui viene gestito il dispositivo. |
| Produttore | string | Produttore del dispositivo. |
| Modello | string | Modello del dispositivo. |
| OperatingSystem | string | Sistema operativo in esecuzione nel dispositivo. |
| IpAddress | Entità (IP) | Indirizzo IP corrente del dispositivo. |
| MacAddress | string | Indirizzo MAC del dispositivo. |
| Protocolli | &lt;Stringa elenco&gt; | Elenco di protocolli supportati dal dispositivo. |
| SerialNumber | string | Numero di serie del dispositivo. |
|

Identificatori sicuri di un'entità dispositivo Internet:
- IoTHub + DeviceId

Identificatori vulnerabili di un'entità dispositivo Internet:
- DeviceId (senza IoTHub)

## <a name="mailbox"></a>Mailbox

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | cassetta postale |
| MailboxPrimaryAddress | string | Indirizzo primario della cassetta postale. |
| DisplayName | string | Nome visualizzato della cassetta postale. |
| UPN | string | UPN della cassetta postale. |
| RiskLevel | Enum? | Livello di rischio della cassetta postale. Valori possibili:<li>nessuno<li>Basso<li>Medio<li>Alto |
| ExternalDirectoryObjectId | GUID? | Identificatore AzureAD della cassetta postale. Simile a AadUserId nell'entità account, ma questa proprietà è specifica dell'oggetto cassetta postale sul lato ufficio. |
|

Identificatori sicuri di un'entità cassetta postale:
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>Cluster di posta

*Nome entità: MailCluster*

> [!NOTE]
> **MDO**  =  **Microsoft Defender per office 365**, noto in precedenza come Office 365 Advanced Threat Protection (O365 ATP).

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | "mail-cluster" |
| NetworkMessageIds | IList ( &lt; stringa)&gt; | ID dei messaggi di posta elettronica che fanno parte del cluster di posta elettronica. |
| CountByDeliveryStatus | IDictionary ( &lt; stringa), int&gt; | Numero di messaggi di posta elettronica per rappresentazione di stringa DeliveryStatus. |
| CountByThreatType | IDictionary ( &lt; stringa), int&gt; | Numero di messaggi di posta elettronica per rappresentazione di stringa ThreatType. |
| CountByProtectionStatus | &lt;Stringa IDictionary, Long&gt; | Numero di messaggi di posta elettronica in base allo stato di protezione dalle minacce. |
| Minacce | IList ( &lt; stringa)&gt; | Minacce dei messaggi di posta che fanno parte del cluster di posta elettronica. |
| Query | string | Query utilizzata per identificare i messaggi del cluster di posta elettronica. |
| QueryTime | DateTime? | Tempo di esecuzione della query. |
| MailCount | Int? | Numero di messaggi di posta che fanno parte del cluster di posta elettronica. |
| IsVolumeAnomaly | Bool? | Determina se si tratta di un cluster di posta anomalia del volume. |
| Source (Sorgente) | string | Origine del cluster di posta (il valore predefinito è "O365 ATP"). |
| ClusterSourceIdentifier | string | ID del messaggio di rete del messaggio di posta elettronica che rappresenta l'origine del cluster di posta elettronica. |
| ClusterSourceType | string | Tipo di origine del cluster di posta elettronica. Viene eseguito il mapping all'impostazione MailClusterSourceType da MDO (vedere la nota precedente). |
| ClusterQueryStartTime | DateTime? | Ora di inizio del cluster: utilizzata come ora di inizio per la query dei conteggi del cluster. In genere le date all'ora di fine meno l'impostazione di DaysToLookBack da MDO (vedere la nota precedente). |
| ClusterQueryEndTime | DateTime? | Ora di fine del cluster: utilizzata come ora di fine per la query dei conteggi del cluster. In genere il tempo di ricezione dei dati di posta elettronica. |
| ClusterGroup | string | Corrisponde alla chiave di query kusto usata in MDO (vedere la nota precedente). |
|

Identificatori sicuri di un'entità del cluster di posta elettronica:
- Query + origine

## <a name="mail-message"></a>Messaggio di posta elettronica

*Nome entità: MailMessage*

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | "messaggio di posta elettronica" |
| File | IList ( &lt; file)&gt; | Entità di file degli allegati del messaggio di posta elettronica. |
| Recipient | string | Destinatario del messaggio di posta elettronica. Nel caso di più destinatari, il messaggio di posta elettronica viene copiato e ogni copia dispone di un destinatario. |
| Urls | IList ( &lt; stringa)&gt; | URL contenuti in questo messaggio di posta elettronica. |
| Minacce | IList ( &lt; stringa)&gt; | Minacce contenute in questo messaggio di posta elettronica. |
| Mittente | string | Indirizzo di posta elettronica del mittente. |
| P1Sender | string | ID di posta elettronica dell'utente (delegato) che ha inviato questo messaggio di posta elettronica "per conto dell'utente P2 (primario)". Se il messaggio di posta elettronica non viene inviato dal delegato, questo valore è uguale a P2Sender. |
| P1SenderDisplayName | string | Nome visualizzato dell'utente (delegato) che ha inviato questo messaggio di posta elettronica "per conto dell'utente P2 (primario)". Rappresentata nell'intestazione di posta elettronica tramite la proprietà "OnbehalfofSenderDisplayName". |
| P1SenderDomain | string | Dominio di posta elettronica dell'utente (delegato) che ha inviato il messaggio "per conto dell'utente P2 (primario)". Se il messaggio di posta elettronica non viene inviato dal delegato, questo valore è uguale a P2SenderDomain. |
| P2Sender | string | Indirizzo di posta elettronica dell'utente (primario) per conto del quale è stato inviato il messaggio di posta elettronica. |
| P2SenderDisplayName | string | Nome visualizzato dell'utente (primario) per conto del quale è stato inviato il messaggio di posta elettronica. Se il messaggio di posta elettronica non viene inviato dal delegato, rappresenta il nome visualizzato del mittente. |
| P2SenderDomain | string | Dominio di posta elettronica dell'utente (primario) per conto del quale è stato inviato il messaggio di posta elettronica. Se il messaggio di posta elettronica non viene inviato dal delegato, rappresenta il dominio del mittente. |
| SenderIP | string | Indirizzo IP del mittente. |
| ReceivedDate | Datetime | Data di ricezione di questo messaggio. |
| NetworkMessageId | GUID? | ID del messaggio di rete di questo messaggio di posta elettronica. |
| InternetMessageId | string | ID del messaggio Internet del messaggio di posta elettronica. |
| Oggetto | string | Oggetto di questo messaggio di posta elettronica. |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | UInt? | Usato da Microsoft Defender per Office 365 per trovare messaggi di posta elettronica corrispondenti o simili. |
| AntispamDirection | Enum? | Direzionalità del messaggio di posta elettronica. Valori possibili:<li>Sconosciuto<li>In ingresso<li>In uscita<li>IntraOrg (interno) |
| DeliveryAction | Enum? | Azione di recapito di questo messaggio di posta elettronica. Valori possibili:<li>Sconosciuto<li>DeliveredAsSpam<li>Recapitato<li>Bloccato<li>Sostituito |
| DeliveryLocation | Enum? | Posizione di recapito di questo messaggio di posta elettronica. Valori possibili:<li>Sconosciuto<li>Posta in arrivo<li>JunkFolder<li>DeletedFolder<li>Quarantena<li>Esterno<li>Non riuscito<li>Dropped<li>Inoltrato |
| Linguaggio | string | Lingua in cui viene scritto il contenuto del messaggio di posta elettronica. |
| ThreatDetectionMethods | IList ( &lt; stringa)&gt; | Elenco di metodi di rilevamento delle minacce applicati a questo messaggio di posta elettronica. |
|

Identificatori sicuri di un'entità di messaggio di posta elettronica:
- NetworkMessageId + destinatario

## <a name="submission-mail"></a>Posta elettronica di invio

*Nome entità: SubmissionMail*

| Campo | Tipo | Descrizione |
| ----- | ---- | ----------- |
| Type | string | 'SubmissionMail' |
| SubmissionId | GUID? | ID di invio. |
| SubmissionDate | DateTime? | Data/ora segnalata per questo invio. |
| Submitter | string | Indirizzo di posta elettronica del mittente. |
| NetworkMessageId | GUID? | ID del messaggio di rete dell'indirizzo di posta elettronica a cui appartiene l'invio. |
| Timestamp | DateTime? | Timestamp della ricezione del messaggio (posta elettronica). |
| Recipient | string | Destinatario del messaggio di posta elettronica. |
| Mittente | string | Mittente del messaggio di posta elettronica. |
| SenderIp | string | Indirizzo IP del mittente. |
| Oggetto | string | Oggetto del messaggio di posta elettronica di invio. |
| ReportType | string | Tipo di invio per l'istanza di specificata. Viene eseguito il mapping a Junk, attività, malware o NotJunk. |
|

Identificatori sicuri di un'entità SubmissionMail:
- SubmissionId, Submitter, NetworkMessageId, destinatario

## <a name="cloud-application-identifiers"></a>Identificatori di applicazioni cloud

Nell'elenco seguente vengono definiti gli identificatori per le applicazioni cloud note. Il valore ID app viene usato come identificatore [dell'entità applicazione cloud](#cloud-application) .

|ID dell'app|Nome|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|Box|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|cornerstone ondemand|
|10921|Zendesk|
|10980|Okta|
|11042|Software Jive|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Services|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|Microsoft OneDrive for Business|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc|
|18432|Zscaler|
|19688|Xactly|
|20595|Microsoft Cloud App Security|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Ciclo di vita di Autodesk Fusion|
|23043|Slack|
|23233|Microsoft Office Online|
|25275|Microsoft Skype for Business|
|25988|Google docs|
|26055|Interfaccia di amministrazione di Microsoft Office 365|
|26060|OPSWAT Gears|
|26061|Microsoft Word online|
|26062|Microsoft PowerPoint online|
|26063|Microsoft Excel Online|
|26069|Google Drive|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Approfondimento Microsoft|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|Emulatore proxy CAS|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics Talent|
|

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come struttura di entità, identificatori e schema in Sentinel di Azure.

Altre informazioni sulle [entità](entities-in-azure-sentinel.md) e sul [mapping delle entità](map-data-fields-to-entities.md). 
