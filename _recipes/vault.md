---
title: "Hashicorp Vault OTP Setup"
last_modified_at: 2021-05-23T22:38:42-05:00
categories:
  - recipes
tags:
  - vault
---
## Process
This process is for setting up a PAM client to use Hashicorp vaults OTP feature. Use [Hashicorps Learn](https://learn.hashicorp.com/tutorials/vault/ssh-otp) for more details.

### Download and install vault-ssh-helper from [releases.hashicorp.com](https://releases.hashicorp.com/vault-ssh-helper).
1. Download vault-ssh-helper.
```sh
$ wget https://releases.hashicorp.com/vault-ssh-helper/<latest>/vault-ssh-helper_<latest>_linux
```
2. Unzip the binary from the archive into /usr/local/bin.
```sh
$ sudo unzip -q vault-ssh-helper_<latest>_linux_amd64.zip -d /usr/local/bin
```
3. Set the vault-ssh-helper binary's permissions to executable.
```sh
$ sudo chmod 0755 /usr/local/bin/vault-ssh-helper
```
4. Set the vault-ssh-helper binary's user and group to root.
```sh
$ sudo chown root:root /usr/local/bin/vault-ssh-helper
```
### Create a Vault SSH Helper configuration file.

1. Create a directory to store the configuration file.
```sh
$ sudo mkdir /etc/vault-ssh-helper.d/
```
2. Create a config file with the following content:
```sh
emacs -nw /etc/vault-ssh-helper.d/config.hcl

vault_addr = "<VAULT_EXTERNAL_ADDR>"
tls_skip_verify = false
ca_cert = "<Path to PEM_ENCODED_CA_CERT>"
ssh_mount_point = "ssh"
# namespace = "my_namespace" # Vault Enterprise only
allowed_roles = "*"
```
### Modify the PAM sshd configuration file.

1. Backup the original configuration file.
```sh
$ sudo cp /etc/pam.d/sshd /etc/pam.d/sshd.orig
```
Edit the file
```sh
$ sudo emacs /etc/pam.d/sshd
```
2. Ensure the following is added.
```sh
auth requisite pam_exec.so quiet expose_authtok log=/var/log/vault-ssh.log /usr/local/bin/vault-ssh-helper -config=/etc/vault-ssh-helper.d/config.hcl
auth optional pam_unix.so not_set_pass use_first_pass nodelay
```
If your on RHEL then you may have to do a couple more things

```
#%PAM-1.0
auth       required    pam_sepermit.so # Added for vault
# auth       substack     password-auth # Commented for vault
auth       include      postlogin
auth       requisite    pam_exec.so quiet expose_authtok log=/var/log/vault-ssh.log /usr/local/bin/vault-ssh-helper -config=/etc/vault-ssh-helper.d/config.hcl # Added for vault
auth       optional     pam_unix.so not_set_pass use_first_pass nodelay # Added for vault
account    required     pam_sepermit.so
account    required     pam_nologin.so
account    include      password-auth
# password   include      password-auth # Commented for vault
...
```
### Modify the sshd configuration file.

1. Backup the original configuration file.
```sh
$ sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.orig
```
2. Open the file in your  editor.
```sh
$ sudo emacs /etc/ssh/sshd_config
```
3. Add or set the following:

**WARNING:** Ensure you have a means outside of username and password to log into the server or you will have locked yourself out.
{: .notice--warning}

```sh
ChallengeResponseAuthentication yes
UsePAM yes
PasswordAuthentication no # Stops Password authentication and watch out!
```
4. Restart the sshd service.
```sh
sudo systemctl restart sshd
```

5. Verify the configuration.

```sh
$ vault-ssh-helper -verify-only -config /etc/vault-ssh-helper.d/config.hcl
2021/05/24 09:11:01 [INFO] using SSH mount point: ssh
2021/05/24 09:11:01 [INFO] using namespace:
2021/05/24 09:11:01 [INFO] vault-ssh-helper verification successful!
```

**NOTE:** In order to log into the server you will need the account you are trying to access with, added to the server.
{: .notice--info}

### Check logon status
```sh
cat /var/log/vault-ssh.log #Location defined by the pam ssh config
*** Mon May 24 10:32:05 2021
2021/05/24 10:32:05 [INFO] using SSH mount point: ssh
2021/05/24 10:32:05 [INFO] using namespace:
2021/05/24 10:32:05 [INFO] vault.user@10.0.0.2 authenticated!
```

### Validate otp service is working
```sh
curl -XPOST https://<VaultServer>:8200/v1/ssh/verify -d '{"otp":"<otp key provided from vault>"}' -k
```

### Check the audit log (RHEL)
tail -n 200 audit.log | grep vault-ssh | audit2allow -a -w

### Blocks in RHEL
1. You may get blocks by SE that look like this. 
```sh
type=AVC msg=audit(1621874594.249:1491): avc:  denied  { create } for  pid=5368 comm="sshd" name="vault-ssh.log" scontext=system_u:system_r:sshd_t:s0-s0:c0.c1023 tcontext=system_u:object_r:var_log_t:s0 tclass=file permissive=0
```
You'll need to update your SE Policy to allow this and other blocks.

### RHEL SE may block sshd from completing actions 
You may have to make multiple attempts to unblock the right parts.

1. Create a policy for vaultPAM to use from the log.
```sh
tail -n 200 audit.log | grep vault-ssh | audit2allow -a -M vaultPAM
```
Mine looked similar to:
```sh
# file_name: vaultPAM.te
module vaultPAM 1.0;

require {
        type trivnet1_port_t;
        type sshd_t;
        type var_log_t;
        type admin_home_t;
        class file { create execute execute_no_trans map open read };
        class tcp_socket name_connect;
}

#============= sshd_t ==============

#!!!! This avc is allowed in the current policy
allow sshd_t admin_home_t:file { execute execute_no_trans map open read };

#!!!! This avc is allowed in the current policy
allow sshd_t trivnet1_port_t:tcp_socket name_connect;

#!!!! This avc is allowed in the current policy
allow sshd_t var_log_t:file { create open };
```

2. If you use my policy then you'll need to do the following:
```sh
checkmodule -M -m -o vaultPAM.mod vaultPAM.te
semodule_package -o vaultPAM.pp -m vaultPAM.mod
```

3. Apply the policy
```sh
semodule -i vaultPAM.pp
```