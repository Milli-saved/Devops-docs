#   
SSH to Server

Before initiating an SSH connection, it's essential to prioritize secure communication. Make sure to review the guide on [SSH-Key Generation](https://git.gebeya.training/devops-docs/devops-course-technical-docs/-/blob/main/SSH/SSH-Key%20Generation.md?ref_type=heads) for secure key management.

## Connecting to Remote Server

To connect to the remote server, follow these steps:

1. Ensure you have added your SSH public key to the remote server using the guide [Adding SSH Public Key to the Remote Server](https://git.gebeya.training/devops-docs/devops-course-technical-docs/-/blob/main/SSH/Adding%20SSH%20public%20key%20to%20the%20remote%20server.md?ref_type=heads).
2. After adding your public key, run the following command to establish an SSH connection:
```bash
ssh -i [/path/to/ssh-private-key] [username]@[hostIP/dns]
```
example
```bash
ssh -i ~/.ssh/devops-class.pem ec2-user@ec2-52-209-48-167.eu-west-1.compute.amazonaws.com
```
Ensure to replace `[/path/to/ssh-private-key]`, `[username]`, and `[hostIP/dns]` with your specific information. This command allows you to securely connect to the remote server using your private key.