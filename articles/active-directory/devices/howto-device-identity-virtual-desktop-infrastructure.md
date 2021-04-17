---
title: Identità del dispositivo e virtualizzazione desktop - Azure Active Directory
description: Informazioni su come usare contemporaneamente Azure AD dispositivo virtuale e le identità dei dispositivi
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1d78094effe6919587f24c2262612e4fab347d
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575378"
---
# <a name="device-identity-and-desktop-virtualization"></a>Identità del dispositivo e virtualizzazione desktop

Gli amministratori distribuiscono in genere piattaforme VDI (Virtual Desktop Infrastructure) che ospitano sistemi operativi Windows nelle organizzazioni. Gli amministratori distribuiscono VDI in:

- Semplificare la gestione.
- Ridurre i costi tramite il consolidamento e la centralizzazione delle risorse.
- Offrire agli utenti finali la mobilità e la libertà di accedere ai desktop virtuali in qualsiasi momento, ovunque ci si trovi, su qualsiasi dispositivo.

Esistono due tipi principali di desktop virtuali:

- Persistente
- Non persistente

Le versioni permanenti usano un'immagine desktop univoca per ogni utente o un pool di utenti. Questi desktop univoci possono essere personalizzati e salvati per un uso futuro. 

Le versioni non persistenti usano una raccolta di desktop a cui gli utenti possono accedere in base alle esigenze. Questi desktop non persistenti vengono ripristinati allo stato originale, nel caso di Windows corrente<sup>1</sup> ciò si verifica quando una macchina virtuale viene eseguita un processo di arresto/riavvio/reimpostazione del sistema operativo e, nel caso di Windows di livello<sup>2,</sup> ciò si verifica quando un utente si discosta.

Le distribuzioni VDI non persistenti sono aumentate, perché il lavoro remoto continua a essere la nuova norma. Poiché i clienti distribuiscono VDI non persistenti, è importante assicurarsi di gestire la varianza dei dispositivi che potrebbe essere causata da una registrazione frequente dei dispositivi senza avere una strategia appropriata per la gestione del ciclo di vita dei dispositivi.

> [!IMPORTANT]
> La mancata gestione della varianza dei dispositivi può causare un aumento della pressione sul consumo di utilizzo della quota di tenant e il rischio potenziale di interruzione del servizio, se si eserciterà la quota del tenant. Per evitare questa situazione, è consigliabile seguire le indicazioni documentate di seguito durante la distribuzione di ambienti VDI non persistenti.

Questo articolo illustra le linee guida di Microsoft per gli amministratori sul supporto per l'identità del dispositivo e VDI. Per altre informazioni sull'identità del dispositivo, vedere l'articolo [Informazioni sull'identità del dispositivo.](overview.md)

## <a name="supported-scenarios"></a>Scenari supportati

Prima di configurare le identità dei dispositivi Azure AD per l'ambiente VDI, acquisire familiarità con gli scenari supportati. La tabella seguente illustra gli scenari di provisioning supportati. Il provisioning in questo contesto implica che un amministratore può configurare le identità dei dispositivi su larga scala senza richiedere alcuna interazione con l'utente finale.

| Tipo di identità del dispositivo | Infrastruttura delle identità | Dispositivi Windows | Versione della piattaforma VDI | Supportato |
| --- | --- | --- | --- | --- |
| Aggiunta a Azure AD ibrido | Federato<sup>3</sup> | Windows corrente e Windows di livello inferiore | Persistente | Sì |
|   |   | Windows corrente | Non persistente | Sì<sup>5</sup> |
|   |   | Dispositivi Windows di livello inferiore | Non persistente | Sì<sup>6</sup> |
|   | Managed<sup>4</sup> | Windows corrente e Windows di livello inferiore | Persistente | Sì |
|   |   | Windows corrente | Non persistente | No |
|   |   | Dispositivi Windows di livello inferiore | Non persistente | Sì<sup>6</sup> |
| Aggiunta ad Azure AD | Federato | Windows corrente | Persistente | No |
|   |   |   | Non persistente | No |
|   | Gestita | Windows corrente | Persistente | No |
|   |   |   | Non persistente | No |
| Registrato con AAD | Federato/gestito | Windows corrente/Windows di livello inferiore | Persistente/Non persistente | Non applicabile |

<sup>1</sup> **I dispositivi Windows** correnti rappresentano Windows 10, Windows Server 2016 v1803 o versione successiva e Windows Server 2019.
<sup>2</sup> I dispositivi **Windows** di livello inferiore rappresentano Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2. Per informazioni sul supporto in Windows 7, vedere [Il supporto per Windows 7 sta terminando.](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support) Per informazioni sul supporto in Windows Server 2008 R2, vedere Preparare la fine del supporto per [Windows Server 2008.](https://www.microsoft.com/cloud-platform/windows-server-2008)

<sup>3</sup> Un ambiente **dell'infrastruttura** di identità federata rappresenta un ambiente con un provider di identità, ad esempio AD FS o un altro provider di identità di terze parti.

<sup>4</sup>  Un ambiente dell'infrastruttura di identità gestita rappresenta un ambiente con Azure AD come provider di identità distribuito con la sincronizzazione [dell'hash delle password (PHS)](../hybrid/whatis-phs.md) o l'autenticazione [pass-through (PTA)](../hybrid/how-to-connect-pta.md) con Accesso [Single Sign-On](../hybrid/how-to-connect-sso.md)facile.

<sup>5</sup> **Il supporto per la persistenza per Windows corrente** richiede una considerazione aggiuntiva, come illustrato di seguito nella sezione relativa alle indicazioni. Questo scenario richiede Windows 10 1803, Windows Server 2019 o Windows Server (canale semestraale) a partire dalla versione 1803

<sup>6</sup> **Il supporto non di persistenza per Windows di** livello inferiore richiede una considerazione aggiuntiva, come illustrato di seguito nella sezione relativa alle indicazioni.


## <a name="microsofts-guidance"></a>Linee guida di Microsoft

Gli amministratori devono fare riferimento agli articoli seguenti, in base all'infrastruttura di gestione delle identità, per informazioni su come configurare l'aggiunta Azure AD ibrida.

- [Configurare l'Azure Active Directory ibrido per l'ambiente federato](hybrid-azuread-join-federated-domains.md)
- [Configurare l'aggiunta Azure Active Directory ibrida per l'ambiente gestito](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>VDI non persistente

Quando si distribuisce un'interfaccia VDI non persistente, Microsoft consiglia agli amministratori IT di implementare le indicazioni riportate di seguito. In caso negativo, la directory avrà molti dispositivi aggiunti Azure AD ibrido non più obsoleti registrati dalla piattaforma VDI non persistente, con conseguente aumento della pressione sulla quota di tenant e rischio di interruzione del servizio a causa dell'eccesso della quota di tenant.

- Se si usa l'immagine Utilità preparazione sistema (sysprep.exe) e si usa un'immagine pre-Windows 10 1809 per l'installazione, assicurarsi che l'immagine non sia di un dispositivo già registrato con Azure AD come Azure AD ibrido aggiunto.
- Se si fa affidamento su uno snapshot di macchina virtuale (VM) per creare altre macchine virtuali, assicurarsi che lo snapshot non sia presente in una macchina virtuale già registrata con Azure AD come Azure AD ibrido join.
- Creare e usare un prefisso per il nome visualizzato (ad esempio NPVDI-) del computer che indica il desktop come non persistente basato su VDI.
- Per Windows di livello inferiore:
   - Implementare **il comando autoworkplacejoin /leave** come parte dello script di disconnessione. Questo comando deve essere attivato nel contesto dell'utente e deve essere eseguito prima che l'utente si sia disconnesso completamente e mentre è ancora disponibile la connettività di rete.
- Per Windows corrente in un ambiente federato (ad esempio, AD FS):
   - Implementare **dsregcmd /join** come parte della sequenza/ordine di avvio della macchina virtuale e prima dell'accesso dell'utente.
   - **NON eseguire** dsregcmd /leave come parte del processo di arresto/riavvio della macchina virtuale.
- Definire e implementare il processo per [la gestione dei dispositivi non obsoleti.](manage-stale-devices.md)
   - Dopo aver creato una strategia per identificare i dispositivi aggiunti Azure AD ibrido non persistenti (ad esempio, usando il prefisso del nome visualizzato del computer), è consigliabile eseguire la pulizia di questi dispositivi in modo più aggressivo per assicurarsi che la directory non sia utilizzata con molti dispositivi non obsoleti.
   - Per le distribuzioni VDI non persistenti in Windows corrente e di livello inferiore, è consigliabile eliminare i dispositivi con **ApproximateLastLogonTimestamp** più vecchi di 15 giorni.

> [!NOTE]
> Quando si usa VDI non persistente, se si vuole impedire uno stato di aggiunta del dispositivo assicurarsi che sia impostata la chiave del Registro di sistema seguente:  
> `HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001`    
>
> Assicurarsi di eseguire Windows 10 versione 1803 o successiva.  
>
> Il roaming di tutti i dati `%localappdata%` nel percorso non è supportato. Se si sceglie di spostare il contenuto in , assicurarsi che il contenuto delle cartelle e delle chiavi del Registro di sistema seguenti non lasci mai `%localappdata%` il dispositivo in qualsiasi condizione.  Ad esempio: gli strumenti di migrazione del profilo devono ignorare le cartelle e le chiavi seguenti:
>
> * `%localappdata%\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy`
> * `%localappdata%\Packages\Microsoft.Windows.CloudExperienceHost_cw5n1h2txyewy`
> * `%localappdata%\Packages\<any app package>\AC\TokenBroker`
> * `%localappdata%\Microsoft\TokenBroker`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\IdentityCRL`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\AAD`
>


### <a name="persistent-vdi"></a>VDI persistente

Quando si distribuisce VDI permanente, Microsoft consiglia agli amministratori IT di implementare le linee guida riportate di seguito. In caso negativo, si verificano problemi di distribuzione e autenticazione. 

- Se si usa l'immagine Utilità preparazione sistema (sysprep.exe) e si usa un'immagine pre-Windows 10 1809 per l'installazione, assicurarsi che l'immagine non sia di un dispositivo già registrato con Azure AD come Azure AD ibrido aggiunto.
- Se si basa su uno snapshot di macchina virtuale (VM) per creare altre macchine virtuali, assicurarsi che lo snapshot non sia da una macchina virtuale già registrata con Azure AD come Azure AD ibrido join.

È inoltre consigliabile implementare il processo per la [gestione dei dispositivi non obsoleti.](manage-stale-devices.md) In questo modo la directory non verrà utilizzata con molti dispositivi non aggiornati se si reimpostano periodicamente le macchine virtuali.
 
## <a name="next-steps"></a>Passaggi successivi

[Configurazione dell'aggiunta Azure Active Directory ibrida per l'ambiente federato](hybrid-azuread-join-federated-domains.md)
