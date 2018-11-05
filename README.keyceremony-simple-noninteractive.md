# Noninteractive runbook example

This is an example runbook for a noninteractive key ceremony using plain/static keys.

2013-12-16 Martin Bartosch



Assumptions:
2048 Bit RSA key protected by the passphrase '1234'.

```
1. Preparation of CLCA configuration

rm -rf dummyca/
mkdir -p dummyca/etc
mkdir -p dummyca/private/
chmod 700 dummyca/private/
cp etc/clca.cfg dummyca/etc/
cp etc/openssl.cnf dummyca/etc/

cat <<EOF >>dummyca/etc/clca.cfg
get_passphrase() {
   echo "1234"
}
EOF

2. Generate CA key and perform secret sharing.

PASSPHRASE="1234" openssl genrsa -aes256 -passout env:PASSPHRASE -out dummyca/private/rsa-rootkey 2048

3. Create the CA certificate

cd dummyca
../bin/clca initialize

4. Create initial CRL

../bin/clca issuecrl

4. Sign certificate

../bin/clca certify --profile foo REQUEST


```

