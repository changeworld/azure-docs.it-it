---
title: Certificati di anteprima Premium di Azure firewall
description: Per configurare correttamente l'ispezione TLS nell'anteprima di Azure Firewall Premium, è necessario configurare e installare i certificati CA intermedi.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 3914a82903c293cf1a8306b5ecc1f542fef83e72
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549757"
---
# <a name="azure-firewall-premium-preview-certificates"></a>Certificati di anteprima Premium di Azure firewall 

> [!IMPORTANT]
> Azure Firewall Premium è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Per configurare correttamente l'ispezione TLS del firewall Premium Preview di Azure, è necessario fornire un certificato CA intermedio valido e archiviarlo nell'insieme di credenziali delle chiavi di Azure.

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Certificati usati da Azure Firewall Premium Preview

Esistono tre tipi di certificati utilizzati in una distribuzione tipica:

- **Certificato CA intermedio (certificato CA)**

   Un'autorità di certificazione (CA) è un'organizzazione considerata attendibile per la firma dei certificati digitali. Un'autorità di certificazione verifica l'identità e la legittimità di una società o di una persona che richiede un certificato. Se la verifica ha esito positivo, la CA rilascia un certificato firmato. Quando il server presenta il certificato al client, ad esempio il Web browser, durante un handshake SSL/TLS, il client tenta di verificare la firma rispetto a un elenco di firmatari *validi noti* . I Web browser sono in genere dotati di elenchi di autorità di certificazione attendibili in modo implicito per identificare gli host. Se l'autorità non è presente nell'elenco, come per alcuni siti che firmano i propri certificati, il browser avvisa l'utente che il certificato non è firmato da un'autorità riconosciuta e chiede all'utente se desidera continuare le comunicazioni con il sito non verificato.

- **Certificato server (certificato del sito Web)**

   Certificato associato a un nome di dominio specifico. Se un sito Web dispone di un certificato valido, significa che un'autorità di certificazione ha effettuato i passaggi per verificare che l'indirizzo Web appartenga effettivamente a tale organizzazione. Quando si digita un URL o si segue un collegamento a un sito Web protetto, il browser controlla il certificato per le seguenti caratteristiche:
   - L'indirizzo del sito Web corrisponde all'indirizzo del certificato.
   - Il certificato è firmato da un'autorità di certificazione che il browser riconosce come autorità *attendibile* .
   
   Occasionalmente, gli utenti possono connettersi a un server con un certificato non attendibile. Il firewall di Azure eliminerà la connessione come se il server avesse terminato la connessione.

- **Certificato CA radice (certificato radice)**

   Un'autorità di certificazione può emettere più certificati sotto forma di struttura ad albero. Un certificato radice è il certificato di primo livello dell'albero.

Azure Firewall Premium Preview è in grado di intercettare il traffico HTTP/S in uscita e di generare automaticamente un certificato server per `www.website.com` . Questo certificato viene generato utilizzando il certificato CA intermedio fornito dall'utente. Per il corretto funzionamento di questa procedura, il browser dell'utente finale e le applicazioni client devono considerare attendibile il certificato CA radice dell'organizzazione o il certificato CA intermedio. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="Processo certificato":::

## <a name="intermediate-ca-certificate-requirements"></a>Requisiti del certificato della CA intermedia

Verificare che il certificato della CA sia conforme ai requisiti seguenti:

- Quando viene distribuito come segreto di Key Vault, è necessario usare PFX senza password (PKCS12) con un certificato e una chiave privata.

- Deve essere un singolo certificato e non deve includere l'intera catena di certificati.  

- Deve essere valido per un anno in poi.  

- Deve essere una chiave privata RSA con dimensioni minime pari a 4096 byte.  

- Deve avere l' `KeyUsage` estensione contrassegnata come critica con il `KeyCertSign` flag (RFC 5280; 4.2.1.3 Key Usage).

- Deve avere l' `BasicContraints` estensione contrassegnata come critica (RFC 5280; 4.2.1.9 Basic Constraints).  

- Il `CA` flag deve essere impostato su true.

- La lunghezza del percorso deve essere maggiore o uguale a uno.

## <a name="azure-key-vault"></a>Insieme di credenziali chiave di Azure

[Azure Key Vault](../key-vault/general/overview.md) è un archivio segreto gestito da piattaforma che è possibile usare per proteggere segreti, chiavi e certificati TLS/SSL. Azure Firewall Premium supporta l'integrazione con Key Vault per i certificati del server collegati a un criterio del firewall.
 
Per configurare l'insieme di credenziali delle chiavi:

- È necessario importare un certificato esistente con la coppia di chiavi nell'insieme di credenziali delle chiavi. 
- In alternativa, è possibile usare anche un segreto dell'insieme di credenziali delle chiavi archiviato come file PFX codificato in base 64 senza password.  Un file PFX è un certificato digitale contenente sia la chiave privata che la chiave pubblica.
- È consigliabile usare un'importazione di certificati CA perché consente di configurare un avviso in base alla data di scadenza del certificato.
- Dopo aver importato un certificato o un segreto, è necessario definire i criteri di accesso nell'insieme di credenziali delle chiavi per consentire all'identità di ottenere l'accesso al certificato/segreto.
- Il certificato della CA specificato deve essere considerato attendibile dal carico di lavoro di Azure. Assicurarsi che siano distribuite correttamente.

È possibile creare o riusare un'identità gestita assegnata dall'utente esistente, che verrà usata da Azure firewall per recuperare i certificati da Key Vault per conto dell'utente. Per altre informazioni, vedere informazioni sulle [identità gestite per le risorse di Azure.](../active-directory/managed-identities-azure-resources/overview.md) 

## <a name="configure-a-certificate-in-your-policy"></a>Configurare un certificato nei criteri

Per configurare un certificato della CA nei criteri Premium del firewall, selezionare il criterio e quindi selezionare **ispezione TLS (anteprima)**. Selezionare **abilitato** nella pagina **ispezione TLS** . Quindi selezionare il certificato della CA in Azure Key Vault, come illustrato nella figura seguente:

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Diagramma della panoramica Premium di Azure firewall":::
 
> [!IMPORTANT]
> Per visualizzare e configurare un certificato dal portale di Azure, è necessario aggiungere l'account utente di Azure ai criteri di accesso Key Vault. Assegnare **all'account utente** un **elenco** con le **autorizzazioni segrete**.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Criteri di accesso Azure Key Vault":::


## <a name="troubleshooting"></a>Risoluzione dei problemi

Se il certificato della CA è valido, ma non è possibile accedere a FQDN o URL in TLS Inspection, controllare gli elementi seguenti:

- Verificare che il certificato del server Web sia valido.  

- Verificare che il certificato CA radice sia installato nel sistema operativo client.  

- Verificare che il browser o il client HTTPS contenga un certificato radice valido. Firefox e altri browser possono avere criteri di certificazione speciali.  

- Verificare che il tipo di destinazione URL nella regola dell'applicazione includa il percorso corretto e qualsiasi altro collegamento ipertestuale incorporato nella pagina HTML di destinazione. È possibile usare caratteri jolly per una facile copertura dell'intero percorso URL richiesto.  


## <a name="next-steps"></a>Passaggi successivi

- [Scopri di più sulle funzionalità Premium di Azure firewall](premium-features.md)
