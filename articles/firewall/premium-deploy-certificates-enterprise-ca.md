---
title: Distribuire e configurare i certificati della CA globale (Enterprise) per Azure Firewall Premium Preview
description: Informazioni su come distribuire e configurare i certificati della CA globale (Enterprise) per la versione Premium Preview di Azure firewall.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 03/09/2021
ms.author: victorh
ms.openlocfilehash: fba95214a6bbb0482166eab8f77f30911986fbb7
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102525484"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall-preview"></a>Distribuire e configurare i certificati della CA globale (Enterprise) per Azure firewall Preview

> [!IMPORTANT]
> Azure Firewall Premium è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Azure Firewall Premium Preview include una funzionalità di ispezione TLS, che richiede una catena di autenticazione dei certificati. Per le distribuzioni di produzione, è consigliabile usare un'infrastruttura a chiave pubblica (PKI) aziendale per generare i certificati usati con Azure Firewall Premium. Usare questo articolo per creare e gestire un certificato CA intermedio per l'anteprima di Azure Firewall Premium.

Per altre informazioni sui certificati usati da Azure Firewall Premium Preview, vedere [certificati di anteprima Premium del firewall di Azure](premium-certificates.md).

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

Per usare una CA globale (Enterprise) per generare un certificato da usare con l'anteprima Premium di Azure firewall, è necessario disporre delle risorse seguenti: 

- Foresta Active Directory 
- CA radice dei servizi di certificazione Active Directory con registrazione Web abilitata 
- un firewall di Azure Premium con criteri del firewall di livello Premium 
- Azure Key Vault 
- Identità gestita con autorizzazioni di lettura per i **certificati e i segreti** definiti nei criteri di accesso key Vault 

## <a name="request-and-export-a-certificate"></a>Richiedere ed esportare un certificato

1. Accedere al sito di registrazione Web nella CA radice, in genere `https://<servername>/certsrv` e selezionare **Richiedi un certificato**.
1. Selezionare **richiesta avanzata di certificati**.
1. Selezionare **Crea e inviare una richiesta a questa CA**.
1. Compilare il modulo usando il modello dell'autorità di certificazione subordinata come illustrato di seguito:
1. Inviare la richiesta e installare il certificato.
1. Supponendo che questa richiesta venga effettuata da un server Windows utilizzando Internet Explorer, aprire **Opzioni Internet**.
1. Passare alla scheda **contenuto** e selezionare **certificati**.
1. Selezionare il certificato appena emesso, quindi selezionare **Esporta**.
1. Selezionare **Avanti** per avviare la procedura guidata. Selezionare **Sì, Esporta la chiave privata** e quindi fare clic su **Avanti**.
1. il formato del file PFX è selezionato per impostazione predefinita. Deselezionare **Includi tutti i certificati nel percorso certificazione, se possibile**. Se si esporta l'intera catena di certificati, il processo di importazione in Azure firewall avrà esito negativo.
1. Assegnare e confermare una password per proteggere la chiave e quindi fare clic su **Avanti**.
1. Scegliere un nome file e un percorso di esportazione, quindi fare clic su **Avanti**.
1. Selezionare **Finish (fine** ) e spostare il certificato esportato in una posizione sicura.

## <a name="add-the-certificate-to-a-firewall-policy"></a>Aggiungere il certificato a un criterio del firewall

1. Nella portale di Azure passare alla pagina certificati della Key Vault e selezionare **genera/importa**.
1. Selezionare **Importa** come metodo di creazione, assegnare un nome al certificato, selezionare il file PFX esportato, immettere la password e quindi selezionare **Crea**.
1. Passare alla pagina **ispezione TLS (anteprima)** del criterio firewall e selezionare l'identità gestita, il Key Vault e il certificato. 
1. Selezionare **Salva**.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="Ispezione TLS":::

## <a name="validate-tls-inspection"></a>Convalida ispezione TLS

1. Creare una regola dell'applicazione usando l'ispezione TLS per l'URL di destinazione o il nome di dominio completo di propria scelta.  Ad esempio: `*bing.com`.
1. Da un computer aggiunto a un dominio all'interno dell'intervallo di origine della regola, passare alla destinazione e selezionare il simbolo di blocco accanto alla barra degli indirizzi nel browser. Il certificato deve indicare che è stato emesso dalla CA globale (Enterprise) anziché da una CA pubblica.
1. Mostra il certificato per visualizzare altri dettagli, incluso il percorso del certificato.
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="Dettagli certificato":::
1. In Log Analytics eseguire la query KQL seguente per restituire tutte le richieste soggette all'ispezione TLS:
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   Il risultato mostra l'URL completo del traffico controllato: :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="query KQL":::

## <a name="next-steps"></a>Passaggi successivi

[Anteprima di Azure Firewall Premium nella portale di Azure](premium-portal.md)
