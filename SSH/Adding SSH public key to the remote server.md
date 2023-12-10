# Adding SSH Public Key to the Remote Server

Before you add the SSH public key to the remote server, ensure you have generated an SSH key. If not, please refer to the guide on [SSH Key Generation](https://chat.openai.com/c/link-to-SSH-Key-Generation).

Follow these steps to add the SSH public key to the remote server:

1. Connect to the remote server using existing connection options.
2. Navigate to the `~/.ssh/` directory on the remote server.
3. Open the `authorized_hosts` file for editing.
```bash
nano ~/.ssh/authorized_hosts
```
4. Obtain the SSH public key you generated on your local machine.
```bash
cat ~/.ssh/id_rsa.pub
```
5. Copy the public key output.
6. In the `nano` editor, paste the copied public key at the end of the `authorized_hosts` file.
7. To save and exit from the editor, press `Ctrl + X`, then press `Y` to confirm.
 

By following these steps, you have successfully added your SSH public key to the `authorized_hosts` file on the remote server. This key is now authorized to establish secure connections to the server.