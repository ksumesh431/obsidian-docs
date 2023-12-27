

# Symmetric Encryption

Encrypting and decrypting data with the same key is called symmetric encryption.

In this, the sender and reciever, both need to have the same key for encryption/ decryption.




<br/>
<br/>

---

---
<br/>
<br/>




# Asymmetric Encryption 


## In case of SSH

![](Images/Pasted%20image%2020230214000754.png)

<br/>

## To give ssh access to other users 

- Make the user generate public and private key
- ssh with the existing key and copy the public key to the authorized_keys . This Locks the server with the new public key too.
- Now the other user can ssh using his private key

![](Images/Pasted%20image%2020230213234230.png)



<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

---

---
<br/>
<br/>



# Asymmetric Encryption in case of Server

<br/>

1.  Server Creates set of private and public keys 

![](Images/Pasted%20image%2020230213232957.png)
![](Images/Pasted%20image%2020230213235224.png)

<br/>
<br/>
<br/>
<br/>

2.  First the user/browser gets the **Public Key of the web server**. The hacker also gets a copy.

![](Images/Pasted%20image%2020230213235353.png)

<br/>
<br/>
<br/>
<br/>

3.  Then the user/browser uses the <u>PUBLIC KEY OF WEB SERVER</u> to encrypt the **symmetric key** on the user's side.

![](Images/Pasted%20image%2020230213235508.png)


<br/>
<br/>
<br/>
<br/>

4.  The Symmetric key is now secured . The user/browser sends this to the server. The hacker also gets a copy.

![](Images/Pasted%20image%2020230213235551.png)


<br/>
<br/>
<br/>
<br/>

5. The server now uses the PRIVATE KEY OF WEB SERVER to decrypt the **symmetric key**. Now both the user and web server have the symmetric key for encrypting and exchanging data.

The hacker doesnt have the Private key of the web server hence cant do anything.

![](Images/Pasted%20image%2020230213235852.png)





<br/>
<br/>

---

---
<br/>
<br/>



# Working of Certificates

In asymmetric encryption, we recieve the public key of the server, encrypt our symmetric key with it and send the encrypted symmetric key back to the server.

Tho the hacker can create a fake page and send a similar public key. 

<br/>

**To verify the legitimacy of the pub key, we check the certificate that contains the key.**
To make sure the certificate is not fake, we check that who issued and signed the certificate. 

> Certificate Authorities sign the certificates. The browser then validates the certificates' legitimacy.
> 
> The CAs also have a set of public and private keys. They sign certificates using their private key. The browsers have the CA's public keys which they use to validate the CAs and their certificates.

<br/>
<br/>



## To get a signed cert from the CA, we have to make a "certificate signing request" to the CA with the website domain name.

![](Images/Pasted%20image%2020230214003525.png)



Note: The server also needs to verify the legitimacy of the CLIENT. 
So the client also generates a set of public n private keys, gets a cert from CA and sends the signed certificate with public key to the server 





<br/>
<br/>

---

---
<br/>
<br/>




# Public vs Private key

Both keys can be used to encrypt data. But decryption can be done only with the alternate key.

Therefore must encrypt using the public key so that the private key can decrypt the data and not the vice versa.



<br/>
<br/>

---

---
<br/>
<br/>





# Naming Conventions

![](Images/Pasted%20image%2020230214005211.png)

