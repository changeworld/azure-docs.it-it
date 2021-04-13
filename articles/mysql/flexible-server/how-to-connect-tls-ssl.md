---
title: Connettività crittografata con TLS/SSL nel database di Azure per MySQL-server flessibile
description: Istruzioni e informazioni su come connettersi usando TLS/SSL nel database di Azure per MySQL-server flessibile.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 6dbb1b46aef40986fc2d601aee152aed02591ac0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312604"
---
# <a name="connect-to-azure-database-for-mysql---flexible-server-with-encrypted-connections"></a>Connettersi a database di Azure per MySQL-server flessibile con connessioni crittografate

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica

Il server flessibile database di Azure per MySQL supporta la connessione delle applicazioni client al server MySQL mediante la crittografia di Secure Sockets Layer (SSL) con la crittografia TLS (Transport Layer Security). TLS è un protocollo standard del settore che garantisce connessioni di rete crittografate tra il server di database e le applicazioni client, consentendo di rispettare i requisiti di conformità.

Il server flessibile database di Azure per MySQL supporta connessioni crittografate con Transport Layer Security (TLS 1,2) per impostazione predefinita e tutte le connessioni in ingresso con TLS 1,0 e TLS 1,1 verranno negate per impostazione predefinita. L'imposizione della connessione crittografata o la configurazione della versione di TLS sul server flessibile può essere modificata come descritto in questo articolo. 

Di seguito sono riportate le diverse configurazioni delle impostazioni SSL e TLS che è possibile avere per il server flessibile:

| Scenario   | Impostazioni parametro Server      | Descrizione                                    |
|------------|--------------------------------|------------------------------------------------|
|Disabilitare l'imposizione SSL | require_secure_transport = disattivato |Se l'applicazione legacy non supporta le connessioni crittografate al server MySQL, è possibile disabilitare l'imposizione delle connessioni crittografate al server flessibile impostando require_secure_transport = disattivato.|
|Applicare SSL con la versione TLS < 1,2 | require_secure_transport = ON e tls_version = TLSV1 o TLSV 1.1| Se l'applicazione legacy supporta connessioni crittografate, ma richiede la versione TLS < 1,2, è possibile abilitare le connessioni crittografate, ma configurare il server flessibile per consentire le connessioni con la versione TLS (v 1.0 o v 1.1) supportata dall'applicazione|
|Applicare SSL con la versione TLS = 1.2 (configurazione predefinita)|require_secure_transport = ON e tls_version = TLSV 1.2| Si tratta della configurazione consigliata e predefinita per server flessibili.|
|Applicare SSL con la versione TLS = 1.3 (supportata con MySQL v 8.0 e versioni successive)| require_secure_transport = ON e tls_version = TLSV 1.3| Questa operazione è utile e consigliata per lo sviluppo di nuove applicazioni|

> [!Note]
> Le modifiche alla crittografia SSL nel server flessibile non sono supportate. Per impostazione predefinita, i pacchetti di crittografia FIPS vengono applicati quando tls_version è impostato su TLS versione 1,2. Per le versioni di TLS diverse dalla versione 1,2, la crittografia SSL è impostata sulle impostazioni predefinite che vengono fornite con l'installazione della community MySQL.

In questo articolo verrà spiegato come:
* Configurare il server flessibile 
  * Con SSL disabilitato 
  * Con SSL applicato con la versione TLS < 1,2
* Connettersi al server flessibile usando la riga di comando di MySQL 
  * Con connessioni crittografate disabilitate
  * Con connessioni crittografate abilitate
* Verificare lo stato della crittografia per la connessione
* Connettersi al server flessibile con connessioni crittografate usando diversi framework applicazione

## <a name="disable-ssl-enforcement-on-your-flexible-server"></a>Disabilitare l'imposizione SSL nel server flessibile
Se l'applicazione client non supporta le connessioni crittografate, sarà necessario disabilitare l'imposizione delle connessioni crittografate nel server flessibile. Per disabilitare l'imposizione delle connessioni crittografate, è necessario impostare require_secure_transport parametro Server su disattivato, come illustrato nello screenshot e salvare la configurazione del parametro Server per renderla effettiva. require_secure_transport è un **parametro dinamico del server** che diventa effettivo immediatamente e non richiede il riavvio del server per rendere effettivo.

> :::image type="content" source="./media/how-to-connect-tls-ssl/disable-ssl.png" alt-text="Screenshot che illustra come disabilitare SSL con database di Azure per il server flessibile MySQL.":::

### <a name="connect-using-mysql-command-line-client-with-ssl-disabled"></a>Connettersi usando il client da riga di comando MySQL con SSL disabilitato

L'esempio seguente illustra come connettersi al server usando l'interfaccia della riga di comando di MySQL. Usare l' `--ssl-mode=DISABLED` impostazione della stringa di connessione per disabilitare la connessione TLS/SSL dal client MySQL. Sostituire i valori con il nome server e la password effettivi. 

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=DISABLED 
```
È importante notare che l'impostazione require_secure_transport su OFF non significa che le connessioni crittografate non siano supportate sul lato server. Se si imposta require_secure_transport su disattivato sul server flessibile, ma se il client si connette con una connessione crittografata, verrà comunque accettato. La connessione seguente che usa il client MySQL a un server flessibile configurato con require_secure_transport = OFF funzionerà anche come illustrato di seguito.

```bash
 mysql.exe -h mydemoserver.mysql.database.azure.com -u myadmin -p --ssl-mode=REQUIRED
```
```output
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 17
Server version: 5.7.29-log MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show global variables like '%require_secure_transport%';
+--------------------------+-------+
| Variable_name            | Value |
+--------------------------+-------+
| require_secure_transport | OFF   |
+--------------------------+-------+
1 row in set (0.02 sec)
```

In breve, require_secure_transport = OFF l'impostazione consente di disattivare l'applicazione delle connessioni crittografate su un server flessibile e consente le connessioni non crittografate al server dal client oltre alle connessioni crittografate.

## <a name="enforce-ssl-with-tls-version--12"></a>Applicare SSL con la versione TLS < 1,2

Se l'applicazione supporta le connessioni al server MySQL con SSL, ma supporta la versione < 1,2 di TLS, è necessario impostare il parametro server di TLS Versions sul server flessibile. Per impostare le versioni di TLS che si desidera che il server flessibile supporti, è necessario impostare tls_version parametro Server su TLSV1, TLSV 1.1 o TLSV1 e TLSV 1.1, come illustrato nella schermata e salvare la configurazione del parametro Server per renderla effettiva. tls_version è un **parametro del server statico** che richiederà il riavvio del server affinché il parametro abbia effetto.

> :::image type="content" source="./media/how-to-connect-tls-ssl/tls-version.png" alt-text="Screenshot che illustra come impostare la versione di TLS per un server flessibile di database di Azure per MySQL.":::

## <a name="connect-using-mysql-command-line-client-with-tlsssl"></a>Connettersi usando il client da riga di comando MySQL con TLS/SSL

### <a name="download-the-public-ssl-certificate"></a>Scaricare il certificato SSL pubblico
Per usare le connessioni crittografate con le applicazioni client, sarà necessario scaricare il [certificato SSL pubblico](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) , disponibile anche nel pannello portale di Azure rete, come illustrato nella schermata seguente.

> :::image type="content" source="./media/how-to-connect-tls-ssl/download-ssl.png" alt-text="Screenshot che illustra come scaricare il certificato SSL pubblico da portale di Azure.":::

Salvare il file del certificato nel percorso preferito. Ad esempio, in questa esercitazione viene usato `c:\ssl` o nell' `\var\www\html\bin` ambiente locale o nell'ambiente client in cui è ospitata l'applicazione. Ciò consente alle applicazioni di connettersi in modo sicuro al database tramite SSL.

Se il server flessibile è stato creato con l'opzione per l'*accesso privato (integrazione rete virtuale)* , sarà necessario connettersi al server da una risorsa all'interno della stessa rete virtuale usata dal server. È possibile creare una macchina virtuale e aggiungerla alla rete virtuale creata con il server flessibile.

Se il server flessibile è stato creato con l'opzione per l'*accesso pubblico (indirizzi IP consentiti)* , è possibile aggiungere l'indirizzo IP locale all'elenco di regole del firewall nel server.

È possibile scegliere [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) o [MySQL Workbench](./connect-workbench.md)--> per connettersi al server dall'ambiente locale. 

L'esempio seguente illustra come connettersi al server usando l'interfaccia della riga di comando di MySQL. Usare l' `--ssl-mode=REQUIRED` impostazione della stringa di connessione per applicare la verifica del certificato TLS/SSL. Passare il percorso del file del certificato locale al `--ssl-ca` parametro. Sostituire i valori con il nome server e la password effettivi. 

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!Note]
> Confermare che il valore passato a `--ssl-ca` corrisponda al percorso del file del certificato salvato.

Se si tenta di connettersi al server con connessioni non crittografate, viene visualizzato un errore che indica che le connessioni che usano il trasporto non sicuro non sono consentite in modo simile a quanto riportato di seguito:

```output
ERROR 3159 (HY000): Connections using insecure transport are prohibited while --require_secure_transport=ON.
```

## <a name="verify-the-tlsssl-connection"></a>Verificare la connessione TLS/SSL

Eseguire il comando MySQL **status** per verificare di avere effettuato la connessione al server MySQL usando TLS/SSL:

```dos
mysql> status
```
Verificare che la connessione sia crittografata esaminando l'output, che dovrebbe mostrare: * * SSL: la crittografia in uso è * *. Questo pacchetto di crittografia Mostra un esempio e basato sul client. è possibile visualizzare un pacchetto di crittografia diverso.

## <a name="connect-to-your-flexible-server-with-encrypted-connections-using-various-application-frameworks"></a>Connettersi al server flessibile con connessioni crittografate usando diversi framework applicazione

Le stringhe di connessione predefinite nella pagina "stringhe di connessione" disponibili per il server nella portale di Azure includono i parametri necessari per la connessione dei linguaggi comuni al server di database tramite TLS/SSL. Il parametro TLS/SSL varia in base al connettore. Ad esempio, "useSSL = true", "sslmode = required" o "ssl_verify_cert = true" e altre varianti.

Per stabilire una connessione crittografata al server flessibile tramite TLS/SSL dall'applicazione, vedere gli esempi di codice seguenti:

### <a name="wordpress"></a>WordPress
Scaricare il [certificato pubblico SSL](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) e aggiungere le righe seguenti in wp-config. php dopo la riga ```// ** MySQL settings - You can get this info from your web host ** //``` .

```php
//** Connect with SSL** //
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
//** SSL CERT **//
define('MYSQL_SSL_CERT','/FULLPATH/on-client/to/DigiCertGlobalRootCA.crt.pem');
```

### <a name="php"></a>PHP

```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/DigiCertGlobalRootCA.crt.pem", NULL, NULL);
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```

### <a name="php-using-pdo"></a>PHP (usando DOP)

```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'myadmin', 'yourpassword', $options);
```

### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)

```python
try:
    conn = mysql.connector.connect(user='myadmin',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/DigiCertGlobalRootCA.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)

```python
conn = pymysql.connect(user='myadmin',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'})
```

### <a name="django-pymysql"></a>Django (PyMySQL)

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ca': '/var/www/html/DigiCertGlobalRootCA.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby

```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :sslca => '/var/www/html/DigiCertGlobalRootCA.crt.pem'
    )
```

### <a name="golang"></a>Golang

```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/DigiCertGlobalRootCA.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')
db, _ := sql.Open("mysql", connectionString)
```

### <a name="java-mysql-connector-for-java"></a>Java (MySQL Connector per Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB Connector per Java)

```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " +
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " +
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)

```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    SslCa = "DigiCertGlobalRootCA.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>Passaggi successivi
- [Usare MySQL Workbench per connettersi ed eseguire query sui dati nel server flessibile di database di Azure per MySQL](./connect-workbench.md)
- [Usare PHP per connettersi ed eseguire query sui dati nel server flessibile database di Azure per MySQL](./connect-php.md)
- [Creare e gestire una rete virtuale di server flessibili per database di Azure per MySQL usando l'interfaccia della riga di](./how-to-manage-virtual-network-cli.md)comando
- Altre informazioni sulla [rete nel server flessibile database di Azure per MySQL](./concepts-networking.md)
- Altre informazioni sulle [regole del firewall per server flessibili per database di Azure per MySQL](./concepts-networking.md#public-access-allowed-ip-addresses)
