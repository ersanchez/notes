# GnuPG Notes

## Signing Someone's Key

After verifying your friend's credentials at the key party, download their key from a public keyserver - for this example, I'll use the MIT PGP key server. Try to remember to ask your new friend at the key signing party if its ok to upload the key you sign to a keyserver.

  $ gpg2 --keyserver pgp.mit.edu --recv-keys <friendsKeyID>
  
Double check the fingerprint and keyID on the key you just downloaded against the details you got at the key signing party.

If everything matches, sign the key.

  $ gpg2 --default-key <yourKeyID> --sign-key <friendsKeyID>

Next, upload the key (if your friend says it is ok) to the key server.

  $ g-g2 --keyserver pgp.mit.edu --send-key <friendsKeyID>
  
Just for ha-has, check the key server to make sure everything uploaded successfully.
