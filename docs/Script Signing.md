# Script signing

The `scriptSignature` and `scriptPublicKey` fields in your config json file should be set whenever you deploy your script (NOT REQUIRED DURING DEVELOPMENT). The purpose of these fields is to verify that a plugin update was made by the same individual that developed the original plugin. This prevents somebody from hijacking your plugin without having access to your public private keypair. When this value is not present, you can still use this plugin, however the user will be informed that these values are missing and that this is a security risk. [Below](#example-script) is an example script showing you how to generate these values. See below for more details.

The `sign.sh` scripts are used to sign the scripts you used in your plugin; all the `.js` files. The script takes your public and private keys to generate you a signature and public key. First before using this script, you need to generate keys. On Unix systems, you can use the `ssh-keygen` command to generate a keypair; `ssh-keygen -t rsa -b 4096 -m PEM -f ~/.ssh/id_rsa`. This will generate a private key at `~/.ssh/id_rsa` and a public key at `~/.ssh/id_rsa.pub`. Or if you wish to store it somewhere else, make sure to make sure the script knows where to put it, and remove the `-f ~/.ssh/id_rsa` from the command before and specify when it asks where you want to store it. 

## Example script
You can use (or build off of) this template example script to generate the `scriptSignature` and `scriptPublicKey` fields above, but with this script you'll have to manually add them into the config json file.

`sign-script.sh`
```sh
#!/bin/sh
#Example usage:
#cat script.js | sign-script.sh
#sh sign-script.sh script.js

#Set your key paths here
PRIVATE_KEY_PATH=~/.ssh/id_rsa
PUBLIC_KEY_PATH=~/.ssh/id_rsa.pub

PUBLIC_KEY_PKCS8=$(ssh-keygen -f "$PUBLIC_KEY_PATH" -e -m pkcs8 | tail -n +2 | head -n -1 | tr -d '\n')
echo "This is your public key: '$PUBLIC_KEY_PKCS8'"

if [ $# -eq 0 ]; then
  # No parameter provided, read from stdin
  DATA=$(cat)
else
  # Parameter provided, read from file
  DATA=$(cat "$1")
fi

SIGNATURE=$(echo -n "$DATA" | openssl dgst -sha512 -sign ~/.ssh/id_rsa | base64 -w 0)
echo "This is your signature: '$SIGNATURE'"
```