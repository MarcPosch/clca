# Interactive Secret Sharing CA runbook example
This is an example runbook for an interactive key ceremony using Secret Sharing.

2013-12-16 Martin Bartosch

Assumptions:
2048 Bit RSA key protected by a 128 Bit random pass phrase.
The pass phrase is split into 5 shares, of which 3 will be needed to perform CA operations.



```
1. Preparation of CLCA configuration

export K=3
export N=5
rm -rf dummyca/
mkdir -p dummyca/etc
mkdir -p dummyca/private/
chmod 700 dummyca/private/
cp etc/clca.cfg dummyca/etc/
cp etc/openssl.cnf dummyca/etc/

cat <<EOF >>dummyca/etc/clca.cfg
get_passphrase() {
   eval \`../bin/secret get --n $N --k $K\`
   echo \$PASSPHRASE
}
EOF

2. Generate CA key and perform secret sharing.

Required: you need N=5 persons for safekeeping of the CA shares.

eval `./bin/secret generate  --n $N --k $K` openssl genrsa -aes256 -passout env:PASSPHRASE -out dummyca/private/rsa-rootkey 2048

Each share holder must copy the displayed share literally and keep it.

3. Create the CA certificate

cd dummyca
../bin/clca initialize

4. Create initial CRL

../bin/clca issuecrl

5. Sign certificate

../bin/clca certify --profile foo REQUEST


```

