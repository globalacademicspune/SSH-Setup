# EC2 3-Server SSH Setup

This guide explains how to configure passwordless SSH between three AWS EC2 instances using their private IP addresses.

## 🖥️ Server Details

| Server | Hostname | Example Private IP |
|---|---|---|
| Server-1 | `server1` | `10.0.0.10` |
| Server-2 | `server2` | `10.0.0.11` |
| Server-3 | `server3` | `10.0.0.12` |

**Note:** Replace the example IP addresses with the actual private IPs of your EC2 instances.

---

## Step 1: Set Hostnames

Run the appropriate command on each server.

**On Server-1**

```bash
sudo hostnamectl set-hostname server1
exec bash
```

**On Server-2**

```bash
sudo hostnamectl set-hostname server2
exec bash
```

**On Server-3**

```bash
sudo hostnamectl set-hostname server3
exec bash
```

Verify the hostname:

```bash
hostname
```

---

## Step 2: Configure `/etc/hosts`

First, find the private IP address of each EC2 instance.

```bash
hostname -I
```

Alternatively:

```bash
ip a
```

On **all three servers**, open the hosts file:

```bash
sudo vi /etc/hosts
```

Add the following entries, replacing the example IPs with your actual private IPs:

```text
10.0.0.10   server1
10.0.0.11   server2
10.0.0.12   server3
```

Save and exit Vim:

```text
:wq
```

Verify hostname resolution:

```bash
getent hosts server1
getent hosts server2
getent hosts server3
```

---

## Step 3: Generate SSH Keys

Run the following command on **Server-1, Server-2, and Server-3**.

```bash
ssh-keygen
```

Press **Enter** to accept the default file location. For a passwordless setup, leave the passphrase empty if appropriate for your environment.

Display the public key on each server:

```bash
cat ~/.ssh/id_rsa.pub
```

Copy each server's public key separately.

> Never share or copy your private key (`~/.ssh/id_rsa`). Only exchange public keys (`~/.ssh/id_rsa.pub`).

---

## Step 4: Exchange SSH Public Keys

Each server must trust the public keys of the other two servers.

### On Server-1

Open the authorized keys file:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
vi ~/.ssh/authorized_keys
```

Paste the **Server-2 and Server-3 public keys**, one key per line.

Save and exit, then set permissions:

```bash
chmod 600 ~/.ssh/authorized_keys
```

### On Server-2

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
vi ~/.ssh/authorized_keys
```

Paste the **Server-1 and Server-3 public keys**, one key per line.

Save and exit:

```bash
chmod 600 ~/.ssh/authorized_keys
```

### On Server-3

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
vi ~/.ssh/authorized_keys
```

Paste the **Server-1 and Server-2 public keys**, one key per line.

Save and exit:

```bash
chmod 600 ~/.ssh/authorized_keys
```

**Important:** If `authorized_keys` already contains keys required by other users or systems, append the new keys instead of overwriting the existing file.

---

## Step 5: Test SSH Connectivity

From Server-1, test connectivity to the other servers:

```bash
ssh server2
ssh server3
```

From Server-2:

```bash
ssh server1
ssh server3
```

From Server-3:

```bash
ssh server1
ssh server2
```

On the first connection, SSH may ask you to verify the server's host key.

```text
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Verify the fingerprint where possible, then type `yes` to continue.

If SSH login succeeds without asking for the remote account's password, passwordless SSH is working.

---

## Troubleshooting

If SSH fails, check the following:

**1. Test hostname resolution**

```bash
getent hosts server1
getent hosts server2
getent hosts server3
```

**2. Check SSH service status**

On Ubuntu:

```bash
sudo systemctl status ssh
```

On Amazon Linux or RHEL:

```bash
sudo systemctl status sshd
```

**3. Check SSH permissions**

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

**4. Enable verbose SSH debugging**

```bash
ssh -v server2
```

**5. Check AWS security groups**

Ensure inbound SSH traffic (TCP port `22`) is allowed from the other servers' private IP addresses or their security group, as appropriate.

Also verify that the instances have private network connectivity and that the SSH username is correct. For example, Amazon Linux commonly uses `ec2-user`, while Ubuntu commonly uses `ubuntu`.

---

## Final Result

After completing these steps, all three EC2 instances should be able to connect to each other over SSH using their hostnames and configured public keys, without requiring the remote account's password.

**Use this setup only for trusted servers and protect private keys carefully.**
