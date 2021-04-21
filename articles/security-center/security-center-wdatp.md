---
title: Uso della licenza di Microsoft Defender per endpoint inclusa in Centro sicurezza di Azure
description: Informazioni su Microsoft Defender per Endpoint e sulla distribuzione da Centro sicurezza di Azure.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/19/2021
ms.author: memildin
ms.openlocfilehash: a9997fac66dd49af04f4ed78737118d605e27072
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829891"
---
# <a name="protect-your-endpoints-with-security-centers-integrated-edr-solution-microsoft-defender-for-endpoint"></a>Proteggere gli endpoint con la soluzione EDR integrata del Centro sicurezza: Microsoft Defender per endpoint

Microsoft Defender per endpoint è una soluzione olistica di sicurezza degli endpoint con distribuzione cloud. Le funzionalità principali sono:

- Valutazione e gestione delle vulnerabilità basate sul rischio 
- Riduzione della superficie di attacco
- Protezione basata sul comportamento e basata sul cloud
- Rilevamento e risposta degli endpoint (EDR)
- Analisi e correzione automatiche
- Servizi di ricerca gestiti

> [!TIP]
> Originariamente avviato come **Windows Defender ATP,** questo prodotto edR (Endpoint Detection and Response) è stato rinominato nel 2019 **come Microsoft Defender ATP.**
>
> A Ignite 2020 è stata avviata la [suite Microsoft Defender XDR](https://www.microsoft.com/security/business/threat-protection) e questo componente EDR è stato rinominato **Microsoft Defender per l'endpoint**.


## <a name="availability"></a>Disponibilità

| Aspetto                          | Dettagli                                                                                                                                                                                                                                                                                                       |
|---------------------------------|:--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Stato della versione:                  | Disponibile a livello generale                                                                                                                                                                                                                                                                                      |
| Prezzi:                        | Richiede [Azure Defender per server](defender-for-servers-introduction.md)                                                                                                                                                                                                                                             |
| Piattaforme supportate:            |  • Computer di Azure che eseguono Windows<br> • Azure Arc computer che eseguono Windows|
| Versioni supportate di Windows per il rilevamento:  |  • Windows Server 2019, 2016, 2012 R2 e 2008 R2 SP1<br> • [Desktop virtuale Windows (WVD)](../virtual-desktop/overview.md)<br> • [Windows 10 Enterprise multi-sessione](../virtual-desktop/windows-10-multisession-faq.yml) (in precedenza Enterprise per desktop virtuali)|
| Sistemi operativi non supportati:  |  • Windows 10 (diverso da EVD o WVD)<br> • Linux|
| Autorizzazioni e ruoli obbligatori: | Per abilitare o disabilitare l'integrazione: **Amministratore della sicurezza** o **Proprietario**<br>Per visualizzare gli avvisi MDATP nel Centro **sicurezza:** Lettore di **sicurezza,** **Lettore,** Collaboratore gruppo di **risorse,** Proprietario gruppo di **risorse,** Amministratore della sicurezza, **Proprietario** sottoscrizione o **Collaboratore sottoscrizione**|
| Cloud:                         | ![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) US Gov<br>![No](./media/icons/no-icon.png) Governo cinese, altri governi                                                        |
|                                 |                                                                                                                                                                                                                                                                                                               |

## <a name="microsoft-defender-for-endpoint-features-in-security-center"></a>Funzionalità di Microsoft Defender per endpoint nel Centro sicurezza

Microsoft Defender per Endpoint offre:

- **Sensori avanzati di rilevamento post-violazione**. I sensori di Defender for Endpoint per computer Windows raccolgono una vasta gamma di segnali comportamentali.

- **Rilevamento post-violazione** basato su analisi, basato sul cloud. Defender per Endpoint si adatta rapidamente alle minacce mutevoli. sfruttando strumenti di analisi avanzata e Big Data. È amplificata dalla potenza del Intelligent Security Graph con segnali in Windows, Azure e Office per rilevare minacce sconosciute. Fornisce avvisi interattivi e consente di reagire con tempestività.

- **Intelligence per le minacce**. Defender per Endpoint genera avvisi quando identifica strumenti, tecniche e procedure dell'utente malintenzionato. Usa i dati generati dagli specialisti di minacce e dai team di sicurezza di Microsoft, integrati con intelligence fornita da partner.

Integrando Defender for Endpoint con il Centro sicurezza, si trarranno vantaggio dalle funzionalità aggiuntive seguenti:

- **Onboarding automatizzato.** Il Centro sicurezza abilita automaticamente il sensore Microsoft Defender per endpoint per tutti i server Windows monitorati dal Centro sicurezza.

- **Riquadro singolo di glass**. Nella console del Centro sicurezza vengono visualizzati gli avvisi di Microsoft Defender per endpoint. Per approfondire l'analisi, usare le pagine del portale di Microsoft Defender per Endpoint in cui verranno visualizzate informazioni aggiuntive, ad esempio l'albero del processo di avviso e il grafico degli eventi imprevisti. È anche possibile visualizzare una sequenza temporale dettagliata che mostra ogni comportamento per un determinato periodo, fino a un massimo di sei mesi.

    :::image type="content" source="./media/security-center-wdatp/microsoft-defender-security-center.png" alt-text="Centro sicurezza di Microsoft Defender per endpoint" lightbox="./media/security-center-wdatp/microsoft-defender-security-center.png":::

## <a name="what-are-the-requirements-for-the-microsoft-defender-for-endpoint-tenant"></a>Quali sono i requisiti per il tenant di Microsoft Defender per endpoint?

Quando si usa Centro sicurezza di Azure per monitorare i server, viene creato automaticamente un tenant di Microsoft Defender per endpoint. 

- **Località:** I dati raccolti da Defender per l'endpoint vengono archiviati nella posizione geografica del tenant, come identificato durante il provisioning. I dati dei clienti, in forma pseudonimizzata, possono anche essere archiviati nei sistemi centrali di archiviazione ed elaborazione Stati Uniti. Dopo aver configurato il percorso, non è possibile modificarlo. Se si ha la propria licenza per Microsoft Defender per Endpoint ed è necessario spostare i dati in un'altra posizione, contattare Supporto tecnico Microsoft per reimpostare il tenant.
- **Spostamento delle sottoscrizioni:** Se la sottoscrizione di Azure è stata spostata tra tenant di Azure, sono necessari alcuni passaggi preliminari manuali prima che il Centro sicurezza distribuisca Defender per l'endpoint. Per informazioni dettagliate, [contattare il supporto tecnico Microsoft.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)


## <a name="enable-the-microsoft-defender-for-endpoint-integration"></a>Abilitare l'integrazione di Microsoft Defender per endpoint

### <a name="prerequisites"></a>Prerequisiti

Verificare che il computer soddisfi i requisiti necessari per Defender per l'endpoint:

1. Verificare che il computer sia connesso ad Azure in base alle esigenze:

    - Per **i server Windows,** configurare le impostazioni di rete descritte in [Configurare il proxy del dispositivo e le impostazioni di connettività Internet](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet)
    - Per **i computer locali,** connetterlo al Azure Arc come illustrato in Connettere computer [ibridi con Azure Arc server abilitati](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)
    - Per **i computer Windows Server 2019** e Desktop virtuale [Windows,](../virtual-desktop/overview.md) verificare che nei computer sia in esecuzione l'agente di Log Analytics e che l'estensione MicrosoftMonitoringAgent sia .
    
1. Abilitare **Azure Defender per i server**. Vedere [Avvio rapido: Abilitare Azure Defender](enable-azure-defender.md).
1. Se microsoft Defender for Endpoints è già stato concesso in licenza e distribuito nei server, rimuoverlo usando la procedura descritta in [Eseguire l'offboard di server Windows.](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints#offboard-windows-servers)
1. Se la sottoscrizione è stata spostata tra tenant di Azure, sono necessari anche alcuni passaggi preliminari manuali. Per informazioni dettagliate, [contattare il supporto tecnico Microsoft.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)


### <a name="enable-the-integration"></a>Abilitare l'integrazione
1. Dal menu del Centro sicurezza selezionare Prezzi & **impostazioni** e selezionare la sottoscrizione da modificare.
1. Selezionare **Rilevamento delle minacce**.
1. Selezionare **Consenti a Microsoft Defender per Endpoint di accedere ai dati** e selezionare **Salva.**

    :::image type="content" source="./media/security-center-wdatp/enable-integration-with-edr.png" alt-text="Abilitare l'integrazione tra Centro sicurezza di Azure e la soluzione EDR di Microsoft, Microsoft Defender for Endpoint":::

    Centro sicurezza di Azure automaticamente i server in Microsoft Defender per Endpoint. L'onboarding potrebbe richiedere fino a 24 ore.


## <a name="access-the-microsoft-defender-for-endpoint-portal"></a>Accedere al portale di Microsoft Defender per endpoint

1. Assicurarsi che l'account utente abbia le autorizzazioni necessarie. Per altre informazioni, [vedere Assegnare l'accesso utente Microsoft Defender Security Center](/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Controllare se è disponibile un proxy o un firewall che blocca il traffico anonimo. Il sensore Defender for Endpoint si connette dal contesto di sistema, pertanto il traffico anonimo deve essere consentito. Per garantire l'accesso senza eseguire il mapping al portale di Defender for Endpoint, seguire le istruzioni in Abilitare l'accesso agli URL del [servizio nel server proxy.](/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server)

1. Aprire il [portale Microsoft Defender Security Center .](https://securitycenter.windows.com/) Per altre informazioni sulle funzionalità e sulle icone del portale, vedere [Microsoft Defender Security Center panoramica del portale.](/windows/security/threat-protection/microsoft-defender-atp/portal-overview) 

## <a name="send-a-test-alert"></a>Inviare un avviso di test

Per generare un avviso di test di Microsoft Defender for Endpoint non dannoso:

1. Creare una cartella 'C:\test-MDATP-test'.
1. Usare Desktop remoto per accedere al computer.
1. Aprire una finestra della riga di comando.
1. Al prompt copiare ed eseguire il comando seguente. La finestra del prompt dei comandi verrà chiusa automaticamente.

    ```powershell
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```
    :::image type="content" source="./media/security-center-wdatp/generate-edr-alert.png" alt-text="Finestra del prompt dei comandi con il comando per generare un avviso di test.":::

1. Se il comando ha esito positivo, verrà visualizzato un nuovo avviso nel dashboard Centro sicurezza di Azure e nel portale di Microsoft Defender per endpoint. Possono trascorrere alcuni minuti prima che l'avviso venga visualizzato.
1. Per esaminare l'avviso nel Centro sicurezza, passare a **Avvisi di sicurezza** Sospetto Riga di comando di  >  **PowerShell.**
1. Nella finestra di indagine selezionare il collegamento per passare al portale di Microsoft Defender per endpoint.

    > [!TIP]
    > L'avviso viene attivato con **gravità** informativo.

## <a name="faq-for-security-centers-integrated-microsoft-defender-for-endpoint"></a>Domande frequenti su Microsoft Defender per endpoint integrato del Centro sicurezza

- [Quali sono i requisiti di licenza per Microsoft Defender per Endpoint?](#what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint)
- [Se si ha già una licenza di Microsoft Defender per endpoint, è possibile ottenere uno sconto per Azure Defender?](#if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender)
- [Ricerca per categorie da uno strumento EDR di terze parti?](#how-do-i-switch-from-a-third-party-edr-tool)

### <a name="what-are-the-licensing-requirements-for-microsoft-defender-for-endpoint"></a>Quali sono i requisiti di licenza per Microsoft Defender per Endpoint?
Defender per l'endpoint è incluso senza costi aggiuntivi con **Azure Defender per i server**. In alternativa, può essere acquistato separatamente per 50 o più computer.

### <a name="if-i-already-have-a-license-for-microsoft-defender-for-endpoint-can-i-get-a-discount-for-azure-defender"></a>Se si ha già una licenza di Microsoft Defender per endpoint, è possibile ottenere uno sconto per Azure Defender?
Se si ha già una licenza per Microsoft Defender per endpoint, non sarà necessario pagare per tale parte della licenza di Azure Defender.

Per confermare lo sconto, contattare il team di supporto del Centro sicurezza e fornire le informazioni appropriate per ID area di lavoro, area e licenza per ogni licenza pertinente.

### <a name="how-do-i-switch-from-a-third-party-edr-tool"></a>Ricerca per categorie da uno strumento EDR di terze parti?
Le istruzioni complete per il passaggio da una soluzione di endpoint non Microsoft sono disponibili nella documentazione di Microsoft Defender per endpoint: [Panoramica della migrazione.](/windows/security/threat-protection/microsoft-defender-atp/switch-to-microsoft-defender-migration)
  


## <a name="next-steps"></a>Passaggi successivi

- [Funzionalità e piattaforme supportate dal Centro sicurezza di Azure](security-center-os-coverage.md)
- [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni sul modo in cui le raccomandazioni semplificano la protezione delle risorse di Azure.