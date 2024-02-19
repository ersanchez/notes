# GnuPG Notes

## Contents

1. Generate your public and private keys
1. Generate a revoke key - just in case you ever need to cancel your key
1. Find somebody's key
1. Sign someone else's key
1. List keys on your computer
1. Revoke a key
1. Export a private key (move it to a new computer)

## Helpful Hints

**Key ID**

You can find the `key-id` by typing this command:

	$ gpg --list-keys --fingerprint

The `--fingerprint` option will print out a hexadecimal `key-id`. The last 8 digits of this hex code is the `key-id`.

**Key Servers**

Here is a list of key servers that work at the time of the last update:

* keyserver.ubuntu.com
* pgp.mit.edu
* keys.openpgp.org
* pgp.surf.nl
* the.earth.li

## Generate Keys

	$ gpg --gen-key

## Generate a Revoke Key

Note: Generate a revoke key right after you **create** a new key. 
Note: Another section below has instructions for revoking your key.

	$ gpg -a --output user@host.tld.asc.revoke --gen-revoke key-id

select a reason for the revocation (if you have one)

	gpg> quit
	
Keep this revocation key somewhere safe. You will need it to revoke your gpg key if it is ever compromised.

## Find PGP/GPG Key

You may need to try more than one of the keyservers mentioned above (see **Helpful Hints**).

If you know the person's email:

	$ gpg --keyserver url-of-keyserver-goes-here --search-keys yourfriend@example.com

## Signing Someone's Key

After verifying your friend's credentials at the key party, download their key from a public keyserver - for this example, I'll use the MIT PGP key server. Try to remember to ask your new friend at the key signing party if its ok to upload the key you sign to a keyserver.

    $ gpg --keyserver pgp.mit.edu --recv-keys <friendsKeyID>
  
Double check the fingerprint and `key-id` on the key you just downloaded against the details you got at the key signing party.

If everything matches, sign the key.

    $ gpg --default-key <yourKeyID> --sign-key <friendsKeyID>
    
Set the trust level on your signature on the key you just signed.

    $ gpg --edit-key <friendsKeyID>
    gpg> trust
    # set the trust level as appropriate
    gpg> quit

Next, upload the key (if your friend says it is ok) to the key server.

    $ gpg --keyserver pgp.mit.edu --send-key <friendsKeyID>
  
Check the key server to verify everything uploaded successfully.

## List Keys

You can list the public keys or the private keys on your computer.

Public

	$ gpg --list-keys

Private

	$ gpg --list-secret-keys

## Revoke Your Key

If your key is compromised, revoke it using the revoke key you made earlier. This will mark the key as 'revoked' on the keyservers and _hopefully_ nobody will use it to encrypt information to send to you.

First, import the revoke key onto your computer (and any other computers upon which your gpg key is installed).

	$ gpg --import name-of-your-revoke-key.asc

Next, verify that the key is marked as 'revoked' by your gpg installation.

	$ gpg --list-keys key-id

Lastly, upload the revoke key to the keyservers. 

	$ gpg --keyserver url-to-keyser-see-list-just-above --send-key key-id
