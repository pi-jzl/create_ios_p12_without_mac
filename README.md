# create_ios_p12_without_mac
Steps to create a p12 signing key for building .ipa file without Mac

# start a openssh docker container

docker run -d  -v `pwd`:`pwd` -w `pwd` -it tannerfe/alpine-openssl

# In the docker container , run following commands

```docker shell
# create private key
openssl genrsa -out ios_distribution.key 2048

# convert to CSR file
openssl req -new -key ios_distribution.key -out ios_distribution.csr -subj '/emailAddress=john.li@planetinnovation.com.au, CN=CordovaHelloWorld, C=AU'

# 2) Upload CSR to apple at: https://developer.apple.com/account/ios/certificate/create
#    - choose Production -> App Store and Ad Hoc

# 3) Download the resulting ios_distribution.cer
# convert it to .pem format:
openssl x509 -inform der -in ios_distribution.cer -out ios_distribution.pem

# Download Apple's Worldwide developer cert 
wget https://developer.apple.com/certificationauthority/AppleWWDRCA.cer

openssl x509 -in AppleWWDRCA.cer -inform DER -out AppleWWDRCA.pem -outform PEM

# Convert your cert plus Apple's cert to p12 format (choose a password for the .p12):
openssl pkcs12 -export -out ios_distribution.p12 -inkey ios_distribution.key -in ios_distribution.pem -certfile AppleWWDRCA.pem
# A password must set as phonegap build does not support empty password

```
