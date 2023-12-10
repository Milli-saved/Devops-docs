#   
SSH to Server

Before initiating an SSH connection, it's essential to prioritize secure communication. Make sure to review the guide on [[SSH-Key Generation]] for secure key management.

## Connecting to Remote Server

To connect to the remote server, follow these steps:

1. Ensure you have added your SSH public key to the remote server using the guide [[Adding SSH Public Key to the Remote Server]].
2. After adding your public key, run the following command to establish an SSH connection:
```bash
ssh -i [/path/to/ssh-private-key] [username]@[hostIP/dns]
```
example
```bash
ssh -i ~/.ssh/devops-class.pem ec2-user@ec2-52-209-48-167.eu-west-1.compute.amazonaws.com
```
Ensure to replace `[/path/to/ssh-private-key]`, `[username]`, and `[hostIP/dns]` with your specific information. This command allows you to securely connect to the remote server using your private key.