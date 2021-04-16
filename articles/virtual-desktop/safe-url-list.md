---
title: Elenco di URL necessari di Desktop virtuale Windows - Azure
description: Elenco di URL che è necessario sbloccare per assicurarsi che la distribuzione di Desktop virtuale Windows funzioni come previsto.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 00ae761af44b9e6537149c96607c0ba00e6439c8
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514986"
---
# <a name="required-url-list"></a>Elenco di URL necessari

Per distribuire e usare Desktop virtuale Windows, è necessario sbloccare determinati URL in modo che le macchine virtuali possano accedervi in qualsiasi momento. Questo articolo elenca gli URL necessari da sbloccare per il corretto funzionamento di Desktop virtuale Windows. 

>[!IMPORTANT]
>Desktop virtuale Windows non supporta le distribuzioni che bloccano gli URL elencati in questo articolo.

## <a name="required-url-check-tool"></a>Strumento controllo URL obbligatorio

Lo strumento Controllo URL obbligatorio convalida gli URL e visualizza se gli URL necessari per il funzionamento della macchina virtuale sono accessibili. In caso contrario, lo strumento elenca gli URL inaccessibili in modo da poterli sbloccare, se necessario.

È importante tenere presenti gli aspetti seguenti:

- È possibile usare solo lo strumento Controllo URL obbligatorio per le distribuzioni nei cloud commerciali.
- Lo strumento Controllo URL obbligatorio non può controllare gli URL con caratteri jolly, quindi assicurarsi di sbloccarli per primi.

### <a name="requirements"></a>Requisiti

Per usare lo strumento Controllo URL obbligatorio sono necessari gli elementi seguenti:

- La macchina virtuale deve avere un framework .NET 4.6.2
- RDAgent versione 1.0.2944.400 o successiva
- Il WVDAgentUrlTool.exe file deve essere nella stessa cartella del file WVDAgentUrlTool.config

### <a name="how-to-use-the-required-url-check-tool"></a>Come usare lo strumento Controllo URL obbligatorio

Per usare lo strumento Controllo URL obbligatorio:

1. Aprire un prompt dei comandi come amministratore nella macchina virtuale.
2. Eseguire il comando seguente per modificare la directory nella stessa cartella dell'agente di compilazione:

    ```console
    cd C:\Program Files\Microsoft RDInfra\RDAgent_1.0.2944.1200
    ```

3. Eseguire il comando seguente:

    ```console
    WVDAgentUrlTool.exe
    ```
 
4. Dopo aver eseguito il file, verrà visualizzato un elenco di URL accessibili e inaccessibili.

    Ad esempio, lo screenshot seguente mostra uno scenario in cui è necessario sbloccare due URL non jolly obbligatori:

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'output degli URL non accessibili.](media/noaccess.png)
    
    Ecco l'aspetto dell'output dopo aver sbloccato tutti gli URL non jolly necessari:

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'output degli URL accessibili.](media/access.png)

## <a name="virtual-machines"></a>Macchine virtuali

Le macchine virtuali di Azure create per Desktop virtuale Windows devono avere accesso agli URL seguenti nel cloud commerciale di Azure:

|Indirizzo|Porta TCP in uscita|Scopo|Tag del servizio|
|---|---|---|---|
|*.wvd.microsoft.com|443|Traffico del servizio|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Traffico dell'agente|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Traffico dell'agente|AzureCloud|
|*xt.blob.core.windows.net|443|Traffico dell'agente|AzureCloud|
|*eh.servicebus.windows.net|443|Traffico dell'agente|AzureCloud|
|*xt.table.core.windows.net|443|Traffico dell'agente|AzureCloud|
|*xt.queue.core.windows.net|443|Traffico dell'agente|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Attivazione di Windows|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Aggiornamenti dello stack dell'agente e di SXS|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Supporto del portale di Azure|AzureCloud|
| 169.254.169.254 | 80 | [Endpoint del servizio metadati dell'istanza di Azure](../virtual-machines/windows/instance-metadata-service.md) | N/D |
| 168.63.129.16 | 80 | [Monitoraggio dell'integrità dell'host di sessione](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/D |

>[!IMPORTANT]
>Desktop virtuale Windows supporta ora il tag FQDN. Per altre informazioni, vedere [Usare Firewall di Azure per proteggere le distribuzioni di Desktop virtuale Windows](../firewall/protect-windows-virtual-desktop.md).
>
>Per evitare problemi del servizio, è consigliabile usare tag FQDN o del servizio invece degli URL. Gli URL e i tag elencati corrispondono solo a risorse e siti di Desktop virtuale Windows. Gli URL di altri servizi, come Azure Active Directory, non sono inclusi.

Le macchine virtuali di Azure create per Desktop virtuale Windows devono avere accesso agli URL seguenti nel cloud Azure per enti pubblici virtuale:

|Indirizzo|Porta TCP in uscita|Scopo|Tag del servizio|
|---|---|---|---|
|*.wvd.microsoft.us|443|Traffico del servizio|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Traffico dell'agente|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Traffico dell'agente|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Traffico dell'agente|AzureCloud|
|*xt.blob.core.usgovcloudapi.net|443|Traffico dell'agente|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|Traffico dell'agente|AzureCloud|
|*xt.table.core.usgovcloudapi.net|443|Traffico dell'agente|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Attivazione di Windows|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Aggiornamenti dello stack dell'agente e di SXS|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Supporto del portale di Azure|AzureCloud|
| 169.254.169.254 | 80 | [Endpoint del servizio metadati dell'istanza di Azure](../virtual-machines/windows/instance-metadata-service.md) | N/D |
| 168.63.129.16 | 80 | [Monitoraggio dell'integrità dell'host di sessione](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | N/D |

La tabella seguente elenca gli URL facoltativi accessibili alle macchine virtuali di Azure:

|Indirizzo|Porta TCP in uscita|Scopo|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Autenticazione a Microsoft Online Services|login.microsoftonline.us|
|*.events.data.microsoft.com|443|Servizio di telemetria|nessuno|
|www.msftconnecttest.com|443|Rileva se il sistema operativo è connesso a Internet|nessuno|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|nessuno|
|login.windows.net|443|Accedere a Microsoft Online Services, Microsoft 365|login.microsoftonline.us|
|*.sfx.ms|443|Aggiornamenti per il software client di OneDrive|oneclient.sfx.ms|
|*.digicert.com|443|Verifica della revoca del certificato|nessuno|
|*.azure-dns.com|443|DNS di Azure risoluzione|nessuno|
|*.azure-dns.net|443|DNS di Azure risoluzione|nessuno|

>[!NOTE]
>Desktop virtuale Windows attualmente non ha un elenco di intervalli di indirizzi IP che è possibile sbloccare per consentire il traffico di rete. Al momento è possibile sbloccare solo URL specifici.
>
>Se si usa un firewall di nuova generazione (NGFW), è necessario usare un elenco dinamico appositamente creato per gli indirizzi IP di Azure per assicurarsi di potersi connettere.
>
>Per un elenco di URL sicuri correlati a Office, inclusi gli URL Azure Active Directory, vedere URL e intervalli di indirizzi IP di [Office 365.](/office365/enterprise/urls-and-ip-address-ranges)
>
>È necessario usare il carattere jolly (*) per gli URL che coinvolgono il traffico del servizio. Se si preferisce non usare * per il traffico correlato ad agenti, ecco come trovare gli URL senza caratteri jolly:
>
>1. Registrare le macchine virtuali nel pool di host di Desktop virtuale Windows.
>2. Aprire **il Visualizzatore** eventi, quindi passare ai log **di Windows**  >  **Application**  >  **WVD-Agent** e cercare l'ID evento 3701.
>3. Sbloccare gli URL trovati in ID evento 3701. Gli URL in ID evento 3701 sono specifici dell'area. È necessario ripetere il processo di sblocco con gli URL pertinenti per ogni area in cui si vogliono distribuire le macchine virtuali.

## <a name="remote-desktop-clients"></a>Client Desktop remoto

Qualsiasi Desktop remoto client in uso deve avere accesso agli URL seguenti:

|Indirizzo|Porta TCP in uscita|Scopo|Client|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|Traffico del servizio|Tutti|*.wvd.microsoft.us|
|*.servicebus.windows.net|443|Dati per la risoluzione dei problemi|Tutti|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|FWLink Microsoft|Tutti|nessuno|
|aka.ms|443|Abbreviazione URL Microsoft|Tutti|nessuno|
|docs.microsoft.com|443|Documentazione|Tutti|nessuno|
|privacy.microsoft.com|443|Informativa sulla privacy|Tutti|nessuno|
|query.prod.cms.rt.microsoft.com|443|Aggiornamenti client|Desktop di Windows|nessuno|

>[!IMPORTANT]
>L'apertura di questi URL è essenziale per un'esperienza client affidabile. Il blocco dell'accesso a questi URL non è supportato e influirà sulle funzionalità del servizio.
>
>Questi URL corrispondono solo a siti e risorse client. Questo elenco non include gli URL per altri servizi, ad esempio Azure Active Directory. Azure Active Directory gli URL sono disponibili in ID 56 in URL e intervalli di indirizzi IP di [Office 365.](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)
