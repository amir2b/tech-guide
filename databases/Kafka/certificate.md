# Extract Certificate

## Extract CA Certificate (ca.pem)

```shell
## Check what's in your truststore:
keytool -list -v -keystore client.truststore.jks

## Export all certificates:
keytool -list -keystore client.truststore.jks

## Export the CA certificate to PEM format:
keytool -exportcert -alias <CA_ALIAS> -keystore client.truststore.jks -rfc -file ca.pem
```

## Extract Client Certificate (client.crt)

```shell
## Check what's in your keystore:
keytool -list -v -keystore client.keystore.jks

## Export all certificates:
keytool -list -keystore client.keystore.jks

## Export the client certificate to PEM:
keytool -exportcert -alias <CLIENT_ALIAS> -keystore client.keystore.jks -rfc -file client.crt
```

## Extract Client Private Key (client.key)

Option A: If you only have JKS (requires store password):

```shell
# Convert JKS to PKCS12
keytool -importkeystore -srckeystore client.keystore.jks -srcstoretype JKS -destkeystore client.p12 -deststoretype PKCS12

# Extract private key (you'll be prompted for the PKCS12 password)
openssl pkcs12 -in client.p12 -nocerts -nodes -out client.pem

# The private key is in client.pem, you might need to separate it
# Typically looks for "-----BEGIN PRIVATE KEY-----" section
mv client.pem client.key

rm -f client.p12
```

Option B: If you have the original PKCS12 (.p12) file:

```shell
## Convert JKS to PKCS12 first
keytool -importkeystore -srckeystore client.keystore.jks -destkeystore client.p12 -deststoretype PKCS12

## Extract private key from PKCS12
openssl pkcs12 -in client.p12 -nocerts -nodes -out client.key
```

## Verify the Files

```shell
# Check certificate
openssl x509 -in client.crt -text -noout

# Check private key
openssl rsa -in client.key -check -noout

# Verify certificate was issued by CA
openssl verify -CAfile ca.pem client.crt
```
