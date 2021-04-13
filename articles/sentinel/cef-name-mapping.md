---
title: Chiave CEF (Common Event Format) e mapping dei campi CommonSecurityLog
description: Questo articolo esegue il mapping delle chiavi CEF ai nomi dei campi corrispondenti in CommonSecurityLog in Sentinel di Azure.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 04/12/2021
ms.openlocfilehash: 1670d1bb291e30295018146f2a24c5282feac6e7
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311652"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>Mapping dei campi CEF e CommonSecurityLog

Le tabelle seguenti eseguono il mapping dei nomi dei campi di Common Event Format (CEF) ai nomi usati nei CommonSecurityLog di Azure Sentinel e possono essere utili quando si lavora con un'origine dati CEF in Sentinel di Azure.

Per altre informazioni, vedere [connettere la soluzione esterna usando il formato di evento comune](connect-common-event-format.md).

## <a name="a---c"></a>A-C

|Nome chiave CEF  |Nome campo CommonSecurityLog  |Descrizione  |
|---------|---------|---------|
| atto    |    <a name="deviceaction"></a> DeviceAction     |  Azione indicata nell'evento.       |
|   app  |    ApplicationProtocol     |  Il protocollo usato nell'applicazione, ad esempio HTTP, HTTPS, SSHv2, Telnet, POP, IMPA, IMAPs e così via.   |
| cnt    |    EventCount     |  Conteggio associato all'evento, che mostra il numero di volte in cui è stato osservato lo stesso evento.       |
| | | |

## <a name="d"></a>D

|Nome chiave CEF  |Nome CommonSecurityLog  |Descrizione  |
|---------|---------|---------|
|Fornitore del dispositivo     |  DeviceVendor       | Stringa che, insieme alle definizioni del prodotto e della versione del dispositivo, identifica in modo univoco il tipo di dispositivo mittente.       |
|Prodotto del dispositivo     |   DeviceProduct      |   Stringa che, insieme alle definizioni del produttore e della versione del dispositivo, identifica in modo univoco il tipo di dispositivo mittente.        |
|Versione del dispositivo     |   DeviceVersion      |      Stringa che, insieme alle definizioni del prodotto e del fornitore del dispositivo, identifica in modo univoco il tipo di dispositivo mittente.     |
| destinationDnsDomain    | DestinationDnsDomain        |   Parte DNS del nome di dominio completo (FQDN).      |
| destinationServiceName | DestinationServiceName | Il servizio di destinazione dell'evento. Ad esempio: `sshd`.|
| destinationTranslatedAddress | DestinationTranslatedAddress | Identifica la destinazione tradotta a cui fa riferimento l'evento in una rete IP come indirizzo IP IPv4. |
| destinationTranslatedPort | DestinationTranslatedPort | Porta, dopo la conversione, ad esempio un firewall. <br>Numeri di porta validi: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | Tutte le informazioni sulla direzione acquisita dalla comunicazione osservata. Valori validi: <br>- `0` = In ingresso <br>- `1` = In uscita |
| deviceDnsDomain | DeviceDnsDomain | Parte del dominio DNS del nome di dominio completo (FQDN) |
|DeviceEventClassID     |   DeviceEventClassID     |   Stringa o Integer che funge da identificatore univoco per tipo di evento.      |
| deviceExternalID | DeviceExternalID | Nome che identifica in modo univoco il dispositivo che genera l'evento. |
| deviceFacility | DeviceFacility | Struttura che genera l'evento.|
| deviceInboundInterface | DeviceInboundInterface |Interfaccia in cui il pacchetto o i dati sono stati inseriti nel dispositivo.  |
| deviceNtDomain | DeviceNtDomain | Dominio di Windows dell'indirizzo del dispositivo |
| deviceOutboundInterface | DeviceOutboundInterface |Interfaccia su cui il pacchetto o i dati hanno lasciato il dispositivo. |
| devicePayloadId |DevicePayloadId |Identificatore univoco per il payload associato all'evento. |
| deviceProcessName | ProcessName | Nome del processo associato all'evento. <br><br>In UNIX, ad esempio, il processo genera la voce syslog. |
| deviceTranslatedAddress | DeviceTranslatedAddress | Identifica l'indirizzo del dispositivo convertito a cui fa riferimento l'evento, in una rete IP. <br><br>Il formato è un indirizzo IPv4. |
| dhost |DestinationHostName | Destinazione a cui si riferisce l'evento in una rete IP.  <br>Il formato deve essere un FQDN associato al nodo di destinazione, quando è disponibile un nodo. Ad esempio, `host.domain.com` o `host`. |
| DMAC | DestinationMacAddress | Indirizzo MAC di destinazione (FQDN) |
| dntdom | DestinationNTDomain | Nome di dominio di Windows dell'indirizzo di destinazione.|
| DPID | DestinationProcessId |ID del processo di destinazione associato all'evento.|
| dpriv | DestinationUserPrivileges | Definisce i privilegi dell'utilizzo di destinazione. <br>Valori validi: `Admninistrator` , `User` , `Guest` |
| dproc | DestinationProcessName | Nome del processo di destinazione dell'evento, ad esempio `telnetd` o `sshd.` |
| DPT | DestinationPort | Porta di destinazione. <br>Valori validi: `*0` - `65535` |
| DST | DestinationIP | Indirizzo IpV4 di destinazione a cui fa riferimento l'evento in una rete IP. |
| DTZ | DeviceTimeZon | Fuso orario del dispositivo che genera l'evento |
| Duid |DestinationUserId | Identifica l'utente di destinazione in base all'ID. |
| duser | DestinationUserName |Identifica l'utente di destinazione in base al nome.|
| dvc | DeviceAddress | Indirizzo IPv4 del dispositivo che genera l'evento. |
| dvchost | DeviceName | FQDN associato al nodo del dispositivo, quando è disponibile un nodo. Ad esempio, `host.domain.com` o `host`.| 
| dvcmac | DeviceMacAddress | Indirizzo MAC del dispositivo che genera l'evento. |
| dvcpid | ID di processo | Definisce l'ID del processo nel dispositivo che genera l'evento. |

## <a name="e---i"></a>E-I

|Nome chiave CEF  |Nome CommonSecurityLog  |Descrizione  |
|---------|---------|---------|
|end     |  EndTime       | Ora di fine dell'attività correlata all'evento.        |
|externalId    |   ExternalID      | ID utilizzato dal dispositivo di origine. In genere, questi valori hanno valori crescenti ognuno associato a un evento.        |
|fileCreateTime     |  FileCreateTime      | Ora di creazione del file.        |
|fileHash     |   FileHash      |   Hash di un file.      |
|fileId     |   FileID      |ID associato a un file, ad esempio inode.         |
| fileModificationTime | FileModificationTime |Ora dell'Ultima modifica apportata al file. |
| filePath | FilePath | Percorso completo del file, incluso il nome file. Ad esempio, `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` o `/usr/bin/zip`.|
| filePermission |FilePermission |Autorizzazioni del file. |
| fileType | FileType | Tipo di file, ad esempio pipe, socket e così via.|
|fname | FileName| Nome del file, senza percorso. |
| fsize | FileSize | Dimensione del file. |
|Host    |  Computer       | Host, da syslog        |
|in ingresso     |  ReceivedBytes      |Numero di byte trasferiti in ingresso.         |
| | | |

## <a name="m---p"></a>M-P

|Nome chiave CEF  |Nome CommonSecurityLog  |Descrizione  |
|---------|---------|---------|
|msg   |  Message       | Messaggio che fornisce ulteriori dettagli sull'evento.        |
|Nome     |  Attività       |   Stringa che rappresenta una descrizione comprensibile e leggibile dell'evento.     |
|oldFileCreateTime     |  OldFileCreateTime       | Ora di creazione del file precedente.        |
|oldFileHash     |   OldFileHash      |   Hash del file precedente.      |
|oldFileId     |   OldFileId     |   E ID associati al file precedente, ad esempio inode.      |
| oldFileModificationTime | OldFileModificationTime |Ora dell'Ultima modifica del file precedente. |
| oldFileName |  OldFileName |Nome del file precedente. |
| oldFilePath | OldFilePath | Percorso completo del file precedente, incluso il nome file. <br>Ad esempio, `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` o `/usr/bin/zip`.|
| oldFilePermission | OldFilePermission |Autorizzazioni del file precedente. |
|oldFileSize | OldFileSize | Dimensioni del file precedente.|
| oldFileType | OldFileType | Tipo di file del file precedente, ad esempio una pipe, un socket e così via.|
| in uscita | SentBytes | Numero di byte trasferiti in uscita. |
| Risultato | Risultato | Risultato dell'evento, ad esempio `success` o `failure` .|
|proto    |  Protocollo       | Protocollo di trasporto che identifica il protocollo di livello 4 utilizzato. <br><br>I valori possibili includono i nomi di protocollo, ad esempio `TCP` o `UDP` .        |
| | | |

## <a name="r---t"></a>R-T

|Nome chiave CEF  |Nome CommonSecurityLog  |Descrizione  |
|---------|---------|---------|
|Motivo     |  Motivo      |Motivo per cui è stato generato un evento di controllo. <br><br>Ad esempio, `Bad password` o `Unknown user`.         |
|Richiesta     |   RequestURL      | URL a cui si accede per una richiesta HTTP, incluso il protocollo. Ad esempio: `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   Agente utente associato alla richiesta.      |
| requestContext | RequestContext | Descrive il contenuto da cui ha avuto origine la richiesta, ad esempio il referrer HTTP. |
| requestCookies | RequestCookies |Cookie associati alla richiesta. |
| requestMethod | RequestMethod | Metodo utilizzato per accedere a un URL. <br><br>I valori validi includono i metodi `POST` , ad esempio, `GET` e così via. |
| rt | ReceiptTime | Ora in cui è stato ricevuto l'evento correlato all'attività. |
|Gravità     |  <a name="logseverity"></a> LogSeverity       |  Stringa o Integer che descrive l'importanza dell'evento.<br><br> Valori stringa validi: `Unknown` , `Low` , `Medium` , `High` , `Very-High` <br><br>I valori integer validi sono:<br> - `0`-`3` = Basso <br>- `4`-`6` = Medio<br>- `7`-`8` = Alta<br>- `9`-`10` = Very-High |
| shost    | SourceHostName        |Identifica l'origine a cui si riferisce l'evento in una rete IP. Il formato deve essere un nome di dominio completo (DQDN) associato al nodo di origine, quando è disponibile un nodo. Ad esempio, `host` o `host.domain.com`. |
| SMAC | SourceMacAddress | Indirizzo MAC di origine. |
| sntdom | SourceNTDomain | Nome di dominio di Windows per l'indirizzo di origine. |
| sourceDnsDomain | SourceDnsDomain | Parte del dominio DNS dell'FQDN completo. |
| sourceServiceName | SourceServiceName | Il servizio responsabile della generazione dell'evento. |
| sourceTranslatedAddress | SourceTranslatedAddress | Identifica l'origine convertita a cui fa riferimento l'evento in una rete IP. |
| sourceTranslatedPort | SourceTranslatedPort | Porta di origine dopo la conversione, ad esempio un firewall. <br>I numeri di porta validi sono `0`  -  `65535` . |
| spid | SourceProcessId | ID del processo di origine associato all'evento.|
| spriv | SourceUserPrivileges | Privilegi dell'utente di origine. <br><br>I valori validi sono: `Administrator` , `User` , `Guest` |
| SPROC | SourceProcessName | Nome del processo di origine dell'evento.|
| SPT | SourcePort | Numero di porta di origine. <br>I numeri di porta validi sono `0`  -  `65535` . |
| src | SourceIP |Origine a cui si riferisce un evento in una rete IP, come indirizzo IPv4. |
| start | StartTime | Ora di inizio dell'attività a cui si riferisce l'evento. |
| Suid | SourceUserID | Identifica l'utente di origine in base all'ID. |
| suser | SourceUserName | Identifica l'utente di origine in base al nome. |
| tipo | EventType | Tipo di evento. I valori di valore includono: <br>- `0`: evento di base <br>- `1`: aggregato <br>- `2`: evento correlazione <br>- `3`: evento azione <br><br>**Nota**: questo evento può essere omesso per gli eventi di base. |
| | | |

## <a name="custom-fields"></a>Campi personalizzati

Le tabelle seguenti eseguono il mapping dei nomi delle chiavi CEF e dei campi CommonSecurityLog disponibili per i clienti da usare per i dati che non si applicano a nessuno dei campi predefiniti.

### <a name="custom-ipv6-address-fields"></a>Campi di indirizzi IPv6 personalizzati

Nella tabella seguente viene eseguito il mapping delle chiavi CEF e dei nomi CommonSecurityLog per i campi dell'indirizzo *IPv6* disponibili per i dati personalizzati.

|Nome chiave CEF  |Nome CommonSecurityLog  |
|---------|---------|
|     c6a1    |     DeviceCustomIPv6Address1       |
|     c6a1Label    |     DeviceCustomIPv6Address1Label    |
|     c6a2    |     DeviceCustomIPv6Address2    |
|     c6a2Label    |     DeviceCustomIPv6Address2Label    |
|     c6a3    |     DeviceCustomIPv6Address3    |
|     c6a3Label    |     DeviceCustomIPv6Address3Label    |
|     c6a4    |     DeviceCustomIPv6Address4    |
|     c6a4Label    |     DeviceCustomIPv6Address4Label    |
|     cfp1    |     DeviceCustomFloatingPoint1    |
|     cfp1Label    |     deviceCustomFloatingPoint1Label    |
|     coppia cfp2    |     DeviceCustomFloatingPoint2    |
|     cfp2Label    |     deviceCustomFloatingPoint2Label    |
|     cfp3    |     DeviceCustomFloatingPoint3    |
|     cfp3Label    |     deviceCustomFloatingPoint3Label    |
|     cfp4    |     DeviceCustomFloatingPoint4    |
|     cfp4Label    |     deviceCustomFloatingPoint4Label    |
| | |

### <a name="custom-number-fields"></a>Campi numerici personalizzati

Nella tabella seguente viene eseguito il mapping delle chiavi CEF e dei nomi CommonSecurityLog per i campi *numerici* disponibili per i dati personalizzati.

|Nome chiave CEF  |Nome CommonSecurityLog  |
|---------|---------|
|     CN1    |     DeviceCustomNumber1       |
|     cn1Label    |     DeviceCustomNumber1Label       |
|     CN2    |     DeviceCustomNumber2       |
|     cn2Label    |     DeviceCustomNumber2Label       |
|     CN3    |     DeviceCustomNumber3       |
|     cn3Label    |     DeviceCustomNumber3Label       |
| | |

### <a name="custom-string-fields"></a>Campi stringa personalizzati

Nella tabella seguente viene eseguito il mapping delle chiavi CEF e dei nomi CommonSecurityLog per i campi *stringa* disponibili per i dati personalizzati.

|Nome chiave CEF  |Nome CommonSecurityLog  |
|---------|---------|
|     cs1    |     DeviceCustomString1 <sup> [1](#use-sparingly)</sup>    |
|     cs1Label    |     DeviceCustomString1Label <sup> [1](#use-sparingly)</sup>    |
|     cs2    |     DeviceCustomString2 <sup> [1](#use-sparingly)</sup>   |
|     cs2Label    |     DeviceCustomString2Label <sup> [1](#use-sparingly)</sup> |
|     CS3    |     DeviceCustomString3 <sup> [1](#use-sparingly)</sup>  |
|     cs3Label    |     DeviceCustomString3Label <sup> [1](#use-sparingly)</sup> |
|     CS4    |     DeviceCustomString4 <sup> [1](#use-sparingly)</sup> |
|     cs4Label    |     DeviceCustomString4Label <sup> [1](#use-sparingly)</sup>  |
|     CS5    |     DeviceCustomString5 <sup> [1](#use-sparingly)</sup>   |
|     cs5Label    |     DeviceCustomString5Label <sup> [1](#use-sparingly)</sup>    |
|     CS6    |     DeviceCustomString6 <sup> [1](#use-sparingly)</sup> |
|     cs6Label    |     DeviceCustomString6Label <sup> [1](#use-sparingly)</sup> |
|     flexString1    |     FlexString1    |
|     flexString1Label    |     FlexString1Label    |
|     flexString2    |     FlexString2    |
|     flexString2Label    |     FlexString2Label    |
| | |

> [!TIP]
> <a name="use-sparingly"></a><sup>1</sup> è consigliabile usare i campi **DeviceCustomString** sporadicamente e usare i campi predefiniti più specifici, quando possibile.
> 

### <a name="custom-timestamp-fields"></a>Campi timestamp personalizzati

Nella tabella seguente viene eseguito il mapping delle chiavi CEF e dei nomi CommonSecurityLog per i campi di *timestamp* disponibili per i dati personalizzati.

|Nome chiave CEF  |Nome CommonSecurityLog  |
|---------|---------|
|     deviceCustomDate1    |     DeviceCustomDate1    |
|     deviceCustomDate1Label    |     DeviceCustomDate1Label    |
|     deviceCustomDate2       |     DeviceCustomDate2    |
|     deviceCustomDate2Label    |     DeviceCustomDate2Label    |
|     flexDate1    |     FlexDate1    |
|     flexDate1Label    |     FlexDate1Label    |
| | |

### <a name="custom-integer-data-fields"></a>Campi dati integer personalizzati

Nella tabella seguente viene eseguito il mapping delle chiavi CEF e dei nomi CommonSecurityLog per i campi *Integer* disponibili per i dati personalizzati.

|Nome chiave CEF  |Nome CommonSecurityLog  |
|---------|---------|
|     flexNumber1    |     FlexNumber1    |
|     flexNumber1Label    |     FlexNumber1Label    |
|     flexNumber2    |     FlexNumber2    |
|     flexNumber2Label    |     FlexNumber2Label    |
| | |

## <a name="enrichment-fields"></a>Campi di arricchimento

I campi **CommonSecurityLog** seguenti vengono aggiunti da Azure Sentinel per arricchire gli eventi originali ricevuti dai dispositivi di origine e non hanno mapping nelle chiavi CEF:

### <a name="threat-intelligence-fields"></a>Campi di intelligence per le minacce

|Nome campo CommonSecurityLog  |Descrizione  |
|---------|---------|
|   **IndicatorThreatType**  |  Tipo di minaccia [MaliciousIP](#MaliciousIP) , in base al feed di intelligence per le minacce.       |
| <a name="MaliciousIP"></a>**MaliciousIP** | Elenca gli indirizzi IP del messaggio correlati al feed di intelligence per le minacce corrente. |
|  **MaliciousIPCountry**   | Paese [MaliciousIP](#MaliciousIP) , in base alle informazioni geografiche al momento dell'inserimento del record.        |
| **MaliciousIPLatitude**    |   Longitudine [MaliciousIP](#MaliciousIP) , in base alle informazioni geografiche al momento dell'inserimento del record.      |
| **MaliciousIPLongitude**    |  Longitudine [MaliciousIP](#MaliciousIP) , in base alle informazioni geografiche al momento dell'inserimento del record.       |
| **ReportReferenceLink**    |    Collegamento al report di intelligence per le minacce.     |
|  **ThreatConfidence**   |   Attendibilità della minaccia [MaliciousIP](#MaliciousIP) , in base al feed di intelligence per le minacce.      |
| **ThreatDescription**    |   Descrizione della minaccia [MaliciousIP](#MaliciousIP) , in base al feed di intelligence per le minacce.      |
| **ThreatSeverity** | Gravità della minaccia per [MaliciousIP](#MaliciousIP), in base al feed di intelligence per le minacce al momento dell'inserimento del record. |
|     |         |

### <a name="additional-enrichment-fields"></a>Campi di arricchimento aggiuntivi

|Nome campo CommonSecurityLog  |Descrizione  |
|---------|---------|
|**OriginalLogSeverity**     |  Sempre vuoto, supportato per l'integrazione con CiscoASA. <br>Per informazioni dettagliate sui valori di gravità del log, vedere il campo [LogSeverity](#logseverity) .       |
|**RemoteIP**     |     Indirizzo IP remoto. <br>Questo valore è basato sul campo [CommunicationDirection](#communicationdirection) , se possibile.     |
|**RemotePort**     |   Porta remota. <br>Questo valore è basato sul campo [CommunicationDirection](#communicationdirection) , se possibile.      |
|**SimplifiedDeviceAction**     |   Semplifica il valore [DeviceAction](#deviceaction) a un set statico di valori, mantenendo al tempo stesso il valore originale nel campo [DeviceAction](#deviceaction) . <br>Ad esempio: `Denied`  >  `Deny` .      |
|**SourceSystem**     | Sempre definito come **OpsManager**.        |
|     |         |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [connettere la soluzione esterna usando il formato di evento comune](connect-common-event-format.md).
