## SSL Process 

The Private Key is generated with your Certificate Signing Request (CSR). The CSR is submitted to the Certificate Authority right after you activate your Certificate. The Private Key must be kept safe and secret on your server or device, because later you’ll need it for Certificate installation.

Private Key look like?

It looks like a block of encoded data, starting and ending with headers, such as —–BEGIN RSA PRIVATE KEY—– and —–END RSA PRIVATE KEY—–.

```
openssl genrsa -out example.com.key 2048

openssl req -new -key example.com.key -out example.com.csr -sha256

```
#### From CA you will get  4 files:

1. Domain Certificate (may have a .domain, .crt, or .cer extension).

2. Intermediate Certificate(s) (optional, may be one or more, and may have an .inter, .crt, or .cer extension).

3. A Root Certificate (may have a .root, .crt, or .cer extension).

---------
##### COMODO SSL

* Root CA Certificate – AddTrustExternalCARoot.crt

* Intermediate CA Certificate – COMODORSAAddTrustCA.crt

* Intermediate CA Certificate – COMODORSADomainValidationSecureServerCA.crt

* Your PositiveSSL Certificate – example_com.crt

---------
 
##### How to create CRT file as recieved from CA

cat example_com.crt COMODORSADomainValidationSecureServerCA.crt  COMODORSAAddTrustCA.crt > example.com.crt

### SSL apply in WebServer

```
ssl on;
    ssl_certificate /var/www/example.com/cert/example.com.crt;
    ssl_certificate_key /var/www/example.com/cert/example.com.key;

```
-------
### Tomcat Base Application server SSL create 
###### Using keytool 
keytool is a key and certificate management utility. It enables users to administer their own public/private key pairs and associated certificates for use in self-authentication (where the user authenticates himself/herself to other users/services) or data integrity and authentication services, using digital signatures. It also allows users to cache the public keys (in the form of certificates) of their communicating peers.

keytool stores the keys and certificates in a so-called keystore. The keytool default keystore implementation implements the keystore as a file. It protects private keys with a password.

-------
## How to Install an SSL/TLS Certificate In Tomcat

## What You’ll Need
#### 1. Your server certificate
This is the certificate you received from the CA for your domain. You may have been sent this via email. If not, you can download it by visiting your Account Dashboard and clicking on your order.

#### 2. Your intermediate certificates
These files allow the devices connecting to your server to identify the issuing CA. There may be more than one of these certificates. If you got your certificate in a ZIP folder, it should also contain the Intermediate certificate(s), which is sometimes referred to as a CA Bundle. If not, download the appropriate CA Bundle for your certificate.

#### 3. Your private key
This file should be on your server, or in your possession if you generated your CSR from a free generator tool. On certain platforms, such as Microsoft IIS, the private key is not immediately visible to you but the server is keeping track of it.

## Installation Instructions
#### 1. Convert your certificate files
Convert your certificate files from PEM (.cer or .crt)  to PKCS#7 (.p7b) Format. You can easily do this on your own system by running below OpenSSL command.

```
openssl crl2pkcs7 -nocrl -certfile certificate.cer -out certificate.p7b 
```

#### 2. Access your Directory
Go to the same Directory where you previously saved the keystore and Certificate Signing Request (CSR).

Note: You must install the certificate on the same keystore and under the same “alias name.” If not, you will encounter problems during installation and may have to start over.

#### 3. Run the Install command
Install the certificate in the same keystore by running the following command:

```
keytool -import -trustcacerts -alias server -file your_file_name.p7b -keystore your_domain_name.jks
```

Note: Replace “your_domain_name” with the primary domain you will be securing and “your_file_name” with the PKCS#7 file name that you recently converted & saved.

#### 4. Check confirmation message
You should receive this confirmation message: “Certificate reply was installed in keystore.”

#### 5. Enter Y
Enter Y or Yes when prompted to trust the certificate. Note: Your keystore now has the correct certificate files to use SSL/https on your Tomcat server.

#### 6. Configure an SSL Connector
Now, you need to configure an SSL connector which will enable the server to accept secure connections.

#### 7. Open the .xml file
Open the .xml file from your server in a text editor such as Notepad.

Note: The .xml file is generally stored in the conf folder in your server’s home directory.

#### 8. Locate your connector
Locate the connector that you intend to use the new keystore to secure.

Note: Typically, the connector used has port 443 or 8443.

#### 9. Uncomment the connector
Uncomment the connector – if necessary – by removing the comment tags (<!– and –>).

#### 10. Enter the keystore filename and password
Enter the correct keystore filename and password. See our example below:

```
<  Connector port="443" maxHttpHeaderSize="8192" maxThreads="150"  minSpareThreads="25" maxSpareThreads="75"  enableLookups="false" disableUploadTimeout="true"  acceptCount="100" scheme="https" secure="true" SSLEnabled="true" clientAuth="false" sslProtocol="TLS" keyAlias="server"  keystoreFile="/home/user_name/your_domain_name.jks"  keystorePass="your_keystore_password" />
```

Note: If you are using a version prior to Tomcat 7, please change the word “keystorePass” to “keypass”.

#### 11. Save your changes
Save all changes made to your .xml file.

#### 12. Restart Tomcat
Restart your Tomcat server to complete the SSL installation process.




```
keytool -importkeystore -srckeystore keystore.jks -destkeystore keystore.p12 -deststoretype PKCS12 -srcalias <jkskeyalias> -srcstorepass <jkspassword> -srckeypass <keypassword> -deststorepass <newp12password> -destkeypass <newkeypassword>

openssl pkcs12 -in keystore.p12 -nocerts -nodes -out private.key

```
