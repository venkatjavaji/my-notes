1) Generate server key and self signed server certificate

keytool -genkey -alias serverkey -keystore serverkeystore.p12 -keyalg RSA -storetype PKCS12

	javaji@venkatVJ:/e/work/git/my notes/two way ssl-springboot/certs$ keytool -genkey -alias serverkey -keystore serverkeystore.p12 -keyalg RSA -storetype PKCS12
	Enter keystore password:password<everywhere password>>
	Re-enter new password:
	What is your first and last name?
	  [Unknown]:  venkat
	What is the name of your organizational unit?
	  [Unknown]:  serverkey
	What is the name of your organization?
	  [Unknown]:  skey
	What is the name of your City or Locality?
	  [Unknown]:  heredia
	What is the name of your State or Province?
	  [Unknown]:  sanjose
	What is the two-letter country code for this unit?
	  [Unknown]:  CR
	Is CN=venkat, OU=serverkey, O=skey, L=heredia, ST=sanjose, C=CR correct?
	  [no]:  yes


2) Generate client key and self signed client certificate

keytool -genkey -alias clientkey -keystore clientkeystore.p12 -keyalg RSA -storetype PKCS12

	javaji@venkatVJ:/e/work/git/my notes/two way ssl-springboot/certs$ keytool -genkey -alias clientkey -keystore clientkeystore.p12 -keyalg RSA -storetype PKCS12
	Enter keystore password:
	Re-enter new password:
	What is your first and last name?
	  [Unknown]:  venkat
	What is the name of your organizational unit?
	  [Unknown]:  clientkey
	What is the name of your organization?
	  [Unknown]:  cky
	What is the name of your City or Locality?
	  [Unknown]:  hyd
	What is the name of your State or Province?
	  [Unknown]:  tg
	What is the two-letter country code for this unit?
	  [Unknown]:  IN
	Is CN=venkat, OU=clientkey, O=cky, L=hyd, ST=tg, C=IN correct?
	  [no]:  yes

3) Export the server certificate

keytool -export -alias serverkey -file servercert.cer -keystore serverkeystore.p12
	
	javaji@venkatVJ:/e/work/git/my notes/two way ssl-springboot/certs$ keytool -export -alias serverkey -file servercert.cer -keystore serverkeystore.p12
	Enter keystore password:
	Certificate stored in file <servercert.cer>

4) Export the client certificate

keytool -export -alias clientkey -file clientcert.cer -keystore clientkeystore.p12

	javaji@venkatVJ:/e/work/git/my notes/two way ssl-springboot/certs$ keytool -export -alias clientkey -file clientcert.cer -keystore clientkeystore.p12
	Enter keystore password:
	Certificate stored in file <clientcert.cer>

	
	*****************
	javaji@venkatVJ:/e/work/git/my notes/two way ssl-springboot/certs$ ll
	total 16
	drwxrwxrwx 1 javaji javaji 4096 Nov 21 22:47 ./
	drwxrwxrwx 1 javaji javaji 4096 Nov 21 22:32 ../
	-rw-rw-rw- 1 javaji javaji  857 Nov 21 22:47 clientcert.cer
	-rw-rw-rw- 1 javaji javaji 2565 Nov 21 22:46 clientkeystore.p12
	-rw-rw-rw- 1 javaji javaji  877 Nov 21 22:46 servercert.cer
	-rw-rw-rw- 1 javaji javaji 2581 Nov 21 22:44 serverkeystore.p12
	********************
	
5) Import the SERVER certificate into CLIENT truststore

keytool -importcert -file servercert.cer -keystore clienttruststore.p12 -alias servercert	

	javaji@venkatVJ:/e/work/git/my notes/two way ssl-springboot/certs$ keytool -importcert -file servercert.cer -keystore clienttruststore.p12 -alias servercert
	Enter keystore password:
	Re-enter new password:
	Owner: CN=venkat, OU=serverkey, O=skey, L=heredia, ST=sanjose, C=CR
	Issuer: CN=venkat, OU=serverkey, O=skey, L=heredia, ST=sanjose, C=CR
	Serial number: 3cb906e1
	Valid from: Thu Nov 22 04:44:35 GMT 2018 until: Wed Feb 20 04:44:35 GMT 2019
	Certificate fingerprints:
			 SHA1: 96:D8:0C:BE:B4:71:2C:B6:2B:2B:B6:FF:51:79:1B:1C:49:8D:31:F8
			 SHA256: A2:FC:B2:C6:BD:4E:28:E5:56:13:31:E2:48:E4:97:B4:E6:65:FA:D4:6A:31:EF:6F:A9:FE:14:56:D1:A7:D7:A7
	Signature algorithm name: SHA256withRSA
	Subject Public Key Algorithm: 2048-bit RSA key
	Version: 3

	Extensions:

	#1: ObjectId: 2.5.29.14 Criticality=false
	SubjectKeyIdentifier [
	KeyIdentifier [
	0000: CD 1D 93 05 90 4B AC 79   A3 02 C9 ED 88 4F 3E C1  .....K.y.....O>.
	0010: CE 07 4F 5B                                        ..O[
	]
	]

	Trust this certificate? [no]:  yes
	Certificate was added to keystore

6) Import the CLIENT certificate into SERVER truststore

keytool -importcert -file clientcert.cer -keystore servertruststore.p12 -alias clientcert

	javaji@venkatVJ:/e/work/git/my notes/two way ssl-springboot/certs$ keytool -importcert -file clientcert.cer -keystore servertruststore.p12 -alias clientcert
	Enter keystore password:
	Re-enter new password:
	Owner: CN=venkat, OU=clientkey, O=cky, L=hyd, ST=tg, C=IN
	Issuer: CN=venkat, OU=clientkey, O=cky, L=hyd, ST=tg, C=IN
	Serial number: 3e2596d0
	Valid from: Thu Nov 22 04:46:17 GMT 2018 until: Wed Feb 20 04:46:17 GMT 2019
	Certificate fingerprints:
			 SHA1: 14:D7:DD:5A:83:B9:77:13:86:EA:EC:41:5F:62:63:56:95:13:9D:7D
			 SHA256: 87:09:5C:85:CB:2B:CE:33:EE:CE:08:78:0C:62:80:B0:C5:56:0B:30:CD:07:D0:32:08:9D:28:9E:01:67:3F:94
	Signature algorithm name: SHA256withRSA
	Subject Public Key Algorithm: 2048-bit RSA key
	Version: 3

	Extensions:

	#1: ObjectId: 2.5.29.14 Criticality=false
	SubjectKeyIdentifier [
	KeyIdentifier [
	0000: 06 60 AE 78 7D E0 0A D3   29 72 91 B9 B6 82 4D 7B  .`.x....)r....M.
	0010: F7 C0 B0 B4                                        ....
	]
	]

	Trust this certificate? [no]:  yes
	Certificate was added to keystore
 