

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
- ssh with the existing key and copy the public key the the authorized_keys folder. This Locks the server with the new public key too.
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

4.  The Symmetric key is now secured . The user/browser sends this to the server. The haccker also gets a copy.

![](Images/Pasted%20image%2020230213235551.png)


<br/>
<br/>
<br/>
<br/>

5. The server now uses the PRIVATE KEY OF WEB SERVER to decrypt the **symmetric key**. Now both the user and web server have the symmetric key for encrypting and exchanging data.

The hacker doesnt have the Private key of the web server hence cant do anything.

![](Images/Pasted%20image%2020230213235852.png)



