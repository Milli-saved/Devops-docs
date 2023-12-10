# Generate SSH Key

To generate an SSH key, execute the following command on your Linux server or environment with the openssh-client installed:
```bash
ssh-keygen -t rsa -b 4096
```
This command initiates the SSH key generation process, creating a secure RSA key pair with a bit size of 4096. After running this command, you'll be prompted to specify the file location for the generated key and, optionally, set a passphrase for added security.