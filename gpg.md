# GnuPG Notes

## Contents

1. Generate your public and private keys
2. Generate a revoke key - just in case you ever need to cancel your key
3. List keys
4. Revoke a key
5. Export a private key (move it to a new computer)

## Helpful Hints

You can find the `key-id` by typing this command:

	$ gpg --list-keys --fingerprint

The `--fingerprint` option will print out a hexadecimal `key-id`. The last 8 digits of this hex code is the `key-id`.

### Generate Keys

	$gpg --gen-key

### Generate a Revoke Key

Note: Generate a revoke key right after you **create** a new key. 
Note: Another section below has instructions for revoking your key.

	$ gpg -a --output user@host.tld.asc.revoke --gen-revoke key-id

select a reason for the revocation (if you have one)

	gpg> quit
	
Keep this revocation key somewhere safe. You will need it to revoke your gpg key if it is ever compromised.

## Signing Someone's Key

After verifying your friend's credentials at the key party, download their key from a public keyserver - for this example, I'll use the MIT PGP key server. Try to remember to ask your new friend at the key signing party if its ok to upload the key you sign to a keyserver.

    $ gpg2 --keyserver pgp.mit.edu --recv-keys <friendsKeyID>
  
Double check the fingerprint and `key-id` on the key you just downloaded against the details you got at the key signing party.

If everything matches, sign the key.

    $ gpg2 --default-key <yourKeyID> --sign-key <friendsKeyID>
    
Set the trust level on your signature on the key you just signed.

    $ gpg2 --edit-key <friendsKeyID>
    gpg> trust
    # set the trust level as appropriate
    gpg> quit

Next, upload the key (if your friend says it is ok) to the key server.

    $ gpg2 --keyserver pgp.mit.edu --send-key <friendsKeyID>
  
Check the key server to verify everything uploaded successfully.

## Revoke Your Key

First, import the revoke key onto your computer (and any other computers upon which your gpg key is installed).

	$ gpg --import name-of-your-revoke-key.asc

Next, verify that the key is marked as 'revoked' by your gpg installation.

	$ gpg --list-keys key-id

Lastly, upload the revoke key to the keyservers. There are not many keyservers still alive. As of this writing the active keyservers I could find were:

* keyserver.ubuntu.com
* pgp.mit.edu
* keys.openpgp.org


	$ gpg --keyserver url-to-keyser-see-list-just-above --send-key key-id
