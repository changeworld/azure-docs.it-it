---
title: Domande frequenti su Azure AD Domain Services | Microsoft Docs
description: Leggere e comprendere alcune delle domande frequenti su configurazione, amministrazione e disponibilità per Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/09/2021
ms.author: justinha
ms.openlocfilehash: 32dcf2b387231d50796de0036388b53cab83bf72
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749331"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Domande frequenti su Azure Active Directory (AD) Domain Services

Questa pagina risponde alle domande frequenti su Azure Active Directory Domain Services.

## <a name="configuration"></a>Configurazione

* [È possibile creare più domini gestiti per una singola directory di Azure AD?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [È possibile abilitare Azure AD Domain Services in una rete virtuale classica?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [È possibile abilitare Active Directory Domain Services in una rete virtuale di Azure Resource Manager?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [È possibile eseguire la migrazione del dominio gestito esistente da una rete virtuale classica a una Resource Manager virtuale?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [È possibile abilitare Azure Active Directory Domain Services in una sottoscrizione di Azure CSP (Cloud Solution Provider)?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [È possibile abilitare Azure AD Domain Services in una directory Azure AD federata? Gli hash delle password non vengono sincronizzati con Azure AD. È possibile abilitare Azure AD Domain Services per questa directory?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [È possibile rendere disponibile Servizi di dominio Azure AD in più reti virtuali all'interno della sottoscrizione?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [È possibile abilitare Servizi di dominio Azure AD con PowerShell?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [È possibile abilitare Azure AD Domain Services usando un Resource Manager modello?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [È possibile aggiungere controller di dominio a un dominio gestito di Servizi di dominio Azure Active Directory?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Gli utenti guest possono essere invitati nella directory per Azure AD Domain Services?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [È possibile spostare un dominio Azure AD Domain Services dominio gestito esistente in una sottoscrizione, un gruppo di risorse, un'area o una rete virtuale diversa?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [È possibile rinominare un nome di Azure AD Domain Services esistente?](#can-i-rename-an-existing-azure-ad-domain-services-domain-name)
* [Sono Azure AD Domain Services opzioni di disponibilità elevata?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>È possibile creare più domini gestiti per una singola directory di Azure AD?
No. È possibile creare solo un singolo dominio gestito da Active Directory Domain Services per una singola directory di Azure AD.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>È possibile abilitare Azure AD Domain Services in una rete virtuale classica?
Le reti virtuali classiche non sono supportate per le nuove distribuzioni. I domini gestiti esistenti distribuiti nelle reti virtuali classiche continuano a essere supportati fino al ritiro il 1° marzo 2023. Per le distribuzioni esistenti, è possibile [eseguire la Azure AD Domain Services dal modello di rete virtuale classica a Resource Manager](migrate-from-classic-vnet.md).

Per altre informazioni, vedere l'avviso [di deprecazione ufficiale.](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>È possibile abilitare Active Directory Domain Services in una rete virtuale di Azure Resource Manager?
Sì. Azure AD Domain Services può essere abilitato in una rete virtuale di Azure Resource Manager. Le reti virtuali di Azure classiche non sono più disponibili quando si crea un dominio gestito.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>È possibile eseguire la migrazione del dominio gestito esistente da una rete virtuale classica a una Resource Manager virtuale?
Sì. Per altre informazioni, vedere [Eseguire la Azure AD Domain Services dal modello di rete virtuale classica a Resource Manager](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>È possibile abilitare Azure Active Directory Domain Services in una sottoscrizione di Azure CSP (Cloud Solution Provider)?
Sì. Per altre informazioni, vedere [Come abilitare l'Azure AD Domain Services nelle Azure CSP .](csp.md)

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>È possibile abilitare Azure AD Domain Services in una directory di Azure AD federata? Gli hash delle password non vengono sincronizzati in Azure AD. È possibile abilitare Azure AD Domain Services per questa directory?
No. Per autenticare gli utenti tramite NTLM o Kerberos, Azure AD Domain Services deve accedere agli hash delle password degli account utente. In una directory federata gli hash delle password non vengono archiviati nella directory Azure AD directory. Di conseguenza, Azure AD Domain Services non funziona con tali Azure AD directory.

Tuttavia, se si usa il Azure AD Connect per la sincronizzazione dell'hash delle password, è possibile usare Azure AD Domain Services perché i valori hash delle password vengono archiviati in Azure AD.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>È possibile rendere disponibile Servizi di dominio Azure AD in più reti virtuali all'interno della sottoscrizione?
Il servizio stesso non supporta direttamente questo scenario. Il dominio gestito è disponibile in una sola rete virtuale alla volta. Tuttavia, è possibile configurare la connettività tra più reti virtuali per esporre Azure AD Domain Services ad altre reti virtuali. Per altre informazioni, vedere [Come connettere reti virtuali in Azure usando gateway VPN](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) o [peering di rete virtuale.](../virtual-network/virtual-network-peering-overview.md)

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>È possibile abilitare Servizi di dominio Azure AD con PowerShell?
Sì. Per altre informazioni, vedere [Come abilitare l'Azure AD Domain Services tramite PowerShell.](powershell-create-instance.md)

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>È possibile abilitare Azure Active Directory Domain Services con un modello di Gestione risorse?
Sì, è possibile creare un Azure AD Domain Services gestito usando un Resource Manager personalizzato. Prima di distribuire il modello, è necessario creare un'entità servizio e un gruppo di Azure AD per l'amministrazione usando il portale di Azure o Azure PowerShell distribuzione del modello. Per altre informazioni, vedere [Creare un Azure AD DS gestito usando un modello Azure Resource Manager.](template-create-instance.md) Quando si crea un Azure AD Domain Services gestito nel portale di Azure, è disponibile anche un'opzione per esportare il modello da usare con distribuzioni aggiuntive.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>È possibile aggiungere controller di dominio a un dominio gestito di Servizi di dominio Azure Active Directory?
No. Il dominio fornito da Servizi di dominio Azure Active Directory è un dominio gestito. Non è necessario effettuare il provisioning, configurare o gestire in altro modo i controller di dominio per questo dominio. Queste attività di gestione vengono fornite come servizio da Microsoft. Pertanto, non è possibile aggiungere altri controller di dominio (lettura/scrittura o sola lettura) per il dominio gestito.

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Gli utenti guest possono essere invitati nella directory per Azure AD Domain Services?
No. Gli utenti guest invitati a una directory di Azure AD tramite la procedura di invito [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md) vengono sincronizzati nel dominio gestito di Azure AD Domain Services. Tuttavia, le password per questi utenti non vengono archiviate nella directory Azure AD locale. Pertanto, Azure AD Domain Services possibile sincronizzare gli hash NTLM e Kerberos per questi utenti nel dominio gestito. Tali utenti non possono accedere o aggiungere computer al dominio gestito.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>È possibile spostare un dominio Azure AD Domain Services dominio gestito esistente in una sottoscrizione, un gruppo di risorse, un'area o una rete virtuale diversa?
No. Dopo aver creato un Azure AD Domain Services gestito, non è possibile spostare il dominio gestito in un gruppo di risorse, una rete virtuale, una sottoscrizione e così via. Quando si distribuisce il dominio gestito, è necessario selezionare la sottoscrizione, il gruppo di risorse, l'area e la rete virtuale più appropriati.

### <a name="can-i-rename-an-existing-azure-ad-domain-services-domain-name"></a>È possibile rinominare un nome di Azure AD Domain Services esistente?
No. Dopo aver creato un Azure AD Domain Services gestito, non è possibile modificare il nome di dominio DNS. Quando si crea il dominio gestito, scegliere con attenzione il nome di dominio DNS. Per considerazioni sulla scelta del nome di dominio DNS, vedere l'esercitazione per creare e configurare un dominio Azure AD Domain Services [gestito.](tutorial-create-instance.md#create-a-managed-domain)

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Sono Azure AD Domain Services opzioni di disponibilità elevata?

Sì. Ogni Azure AD Domain Services dominio gestito include due controller di dominio. Non si gestiscono o si connettono a questi controller di dominio, fanno parte del servizio gestito. Se si distribuiscono Azure AD Domain Services in un'area che supporta zone di disponibilità, i controller di dominio vengono distribuiti tra più zone. Nelle aree che non supportano l'zone di disponibilità, i controller di dominio vengono distribuiti tra set di disponibilità. Non sono disponibili opzioni di configurazione o controllo di gestione su questa distribuzione. Per altre informazioni, vedere [Opzioni di disponibilità per le macchine virtuali in Azure.](../virtual-machines/availability.md)

## <a name="administration-and-operations"></a>Amministrazione e operazioni

* [È possibile connettersi al controller di dominio per il dominio gestito tramite Desktop remoto?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [È stata abilitata la Azure AD Domain Services. Quale account utente viene utilizzato per aggiungere computer a questo dominio?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [È possibile esercitare i privilegi di amministratore di dominio per il dominio gestito fornito da Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [È possibile modificare l'appartenenza ai gruppi usando il protocollo LDAP o altri strumenti di amministrazione di AD nei domini gestiti?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Quanto tempo passa prima che eventuali modifiche apportate alla directory di Azure AD siano visibili nel dominio gestito?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [È possibile estendere lo schema del dominio gestito fornito da Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [È possibile modificare o aggiungere record DNS nel domino gestito?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Quali sono i criteri di durata delle password in un dominio gestito?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Azure AD Domain Services fornisce il blocco degli account Active Directory come metodo di protezione?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [È possibile configurare file system distribuito (DFS) e la replica all'interno Azure AD Domain Services?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Come vengono applicati gli aggiornamenti di Windows in Azure AD Domain Services?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>È possibile connettersi al controller di dominio per il dominio gestito tramite Desktop remoto?
No. Non si dispone delle autorizzazioni per connettersi ai controller di dominio per il dominio gestito usando Desktop remoto. I membri del *gruppo Amministratori di AAD DC* possono amministrare il dominio gestito usando strumenti di amministrazione di ACTIVE Directory, ad esempio Centro di amministrazione di Active Directory o AD PowerShell. Questi strumenti vengono installati usando la *Strumenti di amministrazione remota del server* in un server Windows aggiunto al dominio gestito. Per altre informazioni, vedere [Creare una macchina virtuale di gestione per configurare e amministrare un Azure AD Domain Services gestito.](tutorial-create-management-vm.md)

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>È stata abilitata l'Azure AD Domain Services. quale account utente è necessario usare per aggiungere i computer a questo dominio?
Qualsiasi account utente che fa parte del dominio gestito può aggiungersi a una macchina virtuale. Ai membri del *gruppo AAD DC Administrators* viene concesso l'accesso desktop remoto ai computer aggiunti al dominio gestito.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>È possibile esercitare i privilegi di amministratore di dominio per il dominio gestito fornito da Azure AD Domain Services?
No. Non vengono concessi privilegi amministrativi per il dominio gestito. *I privilegi di* amministratore di dominio *e* amministratore dell'organizzazione non sono disponibili per l'uso all'interno del dominio. Anche ai membri dei gruppi di amministratori di dominio o di amministratori dell'organizzazione nel Active Directory locale non vengono concessi privilegi di amministratore di dominio/organizzazione nel dominio gestito.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>È possibile modificare l'appartenenza ai gruppi usando il protocollo LDAP o altri strumenti di amministrazione di AD nei domini gestiti?
Gli utenti e i gruppi sincronizzati da Azure Active Directory a Azure AD Domain Services non possono essere modificati perché l'origine è Azure Active Directory. Ciò include lo spostamento di utenti o gruppi dall'unità organizzativa gestita AADDC Users a un'unità organizzativa personalizzata. Qualsiasi utente o gruppo che ha origine nel dominio gestito può essere modificato.  

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Quanto tempo passa prima che eventuali modifiche apportate alla directory di Azure AD siano visibili nel dominio gestito?
Le modifiche apportate nella directory Azure AD usando l'interfaccia utente Azure AD o PowerShell vengono sincronizzate automaticamente con il dominio gestito. Il processo di sincronizzazione avviene in background. Non esiste un periodo di tempo definito per completare tutte le modifiche dell'oggetto da parte della sincronizzazione.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>È possibile estendere lo schema del dominio gestito fornito da Azure AD Domain Services?
No. Lo schema è amministrato da Microsoft per il dominio gestito. Le estensioni dello schema non sono supportate da Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>È possibile modificare o aggiungere record DNS nel domino gestito?
Sì. Ai membri del *gruppo AAD DC Administrators* vengono concessi privilegi di amministratore *DNS* per modificare i record DNS nel dominio gestito. Tali utenti possono usare la console di Gestione DNS in un computer che esegue Windows Server aggiunto al dominio gestito per gestire DNS. Per usare la console di Gestione *DNS,* installare DNS Server Tools , che fanno parte della Strumenti di amministrazione remota del server *facoltativa* nel server. Per altre informazioni, vedere [Amministrare DNS in un dominio Azure AD Domain Services gestito.](manage-dns.md)

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Quali sono i criteri di durata delle password in un dominio gestito?
La durata predefinita di una password in un dominio gestito di Azure AD Domain Services è 90 giorni. Questa durata non è sincronizzata con la durata delle password configurata in Azure AD. Pertanto, possono verificarsi situazioni in cui le password degli utenti scadono nel dominio gestito, ma sono ancora valide in Azure AD. In scenari di questo tipo gli utenti devono cambiare la password in Azure AD e la nuova password verrà sincronizzata nel dominio gestito. Se si vuole modificare la durata predefinita delle password in un dominio gestito, è possibile [creare e configurare criteri password personalizzati.](password-policy.md)

Inoltre, i criteri Azure AD password per *DisablePasswordExpiration* vengono sincronizzati con un dominio gestito. Quando *DisablePasswordExpiration* viene applicato a un utente in Azure AD, il *valore UserAccountControl* per l'utente *sincronizzato nel* dominio gestito viene DONT_EXPIRE_PASSWORD applicata.

Quando gli utenti reimpostano la password in Azure AD, viene applicato l'attributo *forceChangePasswordNextSignIn=True.* Un dominio gestito sincronizza questo attributo da Azure AD. Quando il dominio gestito rileva *che forceChangePasswordNextSignIn* è impostato per un utente sincronizzato da Azure AD, l'attributo *pwdLastSet* nel dominio gestito viene impostato su *0,* che invalida la password attualmente impostata.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD Domain Services fornisce il blocco degli account Active Directory come metodo di protezione?
Sì. Dopo 5 tentativi di inserimento di password non valide in 2 minuti per il dominio gestito, l'account utente viene bloccato per 30 minuti. Dopo 30 minuti l'account utente viene sbloccato automaticamente. I tentativi di password non validi nel dominio gestito non bloccano l'account utente Azure AD. L'account utente viene bloccato solo all'interno del dominio gestito di Azure AD Domain Services. Per altre informazioni, vedere [Criteri di blocco di password e account nei domini gestiti.](password-policy.md)

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>È possibile configurare la file system distribuito e la replica all'interno Azure AD Domain Services?
No. file system distribuito (DFS) e la replica non sono disponibili quando si usa Azure AD Domain Services.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Come vengono applicati gli aggiornamenti di Windows Azure AD Domain Services?
I controller di dominio in un dominio gestito applicano automaticamente gli aggiornamenti di Windows necessari. Non c'è nulla da configurare o amministrare qui. Assicurarsi di non creare regole del gruppo di sicurezza di rete che bloccano il traffico in uscita verso Windows Update. Per le macchine virtuali appartenenti al dominio gestito, l'utente è responsabile della configurazione e dell'applicazione degli aggiornamenti del sistema operativo e dell'applicazione necessari.

## <a name="billing-and-availability"></a>Fatturazione e disponibilità

* [Servizi di dominio Azure AD è a pagamento?](#is-azure-ad-domain-services-a-paid-service)
* [È disponibile una versione di valutazione gratuita per il servizio?](#is-there-a-free-trial-for-the-service)
* [È possibile sospendere un dominio gestito da Azure AD Domain Services?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [È possibile eseguire il failover Azure AD Domain Services in un'altra area per un evento di ripristino di emergenza?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [È possibile ottenere Azure AD Domain Services come parte di Enterprise Mobility Suite (EMS)? È necessario Azure AD Premium usare Azure AD Domain Services?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [In quale aree di Azure è disponibile il servizio?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Servizi di dominio Azure AD è a pagamento?
Sì. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>È disponibile una versione di valutazione gratuita per il servizio?
Azure AD Domain Services è incluso nella versione di valutazione gratuita per Azure. È possibile iscriversi per una [valutazione gratuita di un mese di Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>È possibile sospendere un dominio gestito da Azure AD Domain Services?
No. Dopo aver abilitato un dominio Azure AD Domain Services, il servizio è disponibile all'interno della rete virtuale selezionata fino a quando non si elimina il dominio gestito. Non è possibile sospendere il servizio. La fatturazione continua su base oraria finché non viene eliminato il dominio gestito.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>È possibile eseguire il failover Azure AD Domain Services in un'altra area per un evento di ripristino di emergenza?
Sì, per fornire resilienza geografica per un dominio gestito, è possibile creare un set di [repliche](tutorial-create-replica-set.md) aggiuntivo in una rete virtuale con peer in qualsiasi area di Azure che supporta Azure AD DS. I set di repliche condividono lo stesso spazio dei nomi e la stessa configurazione con il dominio gestito.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>È possibile ottenere Servizi di dominio Azure AD come parte di Enterprise Mobility Suite (EMS)? È necessario Azure AD Premium per usare Azure Active Directory Domain Services?
No. Azure AD Domain Services è un servizio di Azure con pagamento in base al prezzo e non fa parte di EMS. Azure AD Domain Services può essere usato con tutte le edizioni di Azure AD (Gratuito e Premium). La fatturazione viene addebitata su base oraria, a seconda dell'utilizzo.

### <a name="what-azure-regions-is-the-service-available-in"></a>In quale aree di Azure è disponibile il servizio?
Per l'elenco delle aree di Azure in cui è disponibile Azure Active Directory Domain Services, vedere i [servizi di Azure per area](https://azure.microsoft.com/regions/#services/).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per soluzioni relative ai problemi comuni di configurazione o amministrazione di Azure AD Domain Services, vedere la [Guida alla risoluzione dei problemi](troubleshoot.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui Azure AD Domain Services, vedere [Informazioni Azure Active Directory Domain Services?](overview.md).

Per iniziare, vedere [Creare e configurare un dominio Azure Active Directory Domain Services gestito.](tutorial-create-instance.md)
