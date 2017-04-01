# GnuPG Notes

## First Things First

1. Generate your public and private keys
2. Generate a revoke key - just in case you ever need to cancel your key

### Generating Keys

### Generating a Revoke Key

	$ gpg2 -a --output user@host.tld.asc.revoke --gen-revoke keyID
		# select a reason
		# quit

## Signing Someone's Key

After verifying your friend's credentials at the key party, download their key from a public keyserver - for this example, I'll use the MIT PGP key server. Try to remember to ask your new friend at the key signing party if its ok to upload the key you sign to a keyserver.

    $ gpg2 --keyserver pgp.mit.edu --recv-keys <friendsKeyID>
  
Double check the fingerprint and keyID on the key you just downloaded against the details you got at the key signing party.

If everything matches, sign the key.

    $ gpg2 --default-key <yourKeyID> --sign-key <friendsKeyID>
    
Set the trust level on your signature on the key you just signed.

    $ gpg2 --edit key <friendsKeyID>
    gpg> trust
    # set the trust level as appropriate
    gpg> quit

Next, upload the key (if your friend says it is ok) to the key server.

    $ gpg2 --keyserver pgp.mit.edu --send-key <friendsKeyID>
  
Just for ha-has, check the key server to make sure everything uploaded successfully.
