# My restic backup systemd units

```bash
git clone https://github.com/queeup/restic-systemd.unit /tmp/restic-systemd.unit
mv /tmp/restic-systemd.unit/.config $HOME/

systemctl --user enable --now restic@local.timer
```

## Credentials

### TPM

- If you have TPM chip, add your `$USER` to `tss` group. ~This is for encrypt & decrypt without root privilege.~

- You will need to log off and log back in to apply these changes.

```bash
grep -E '^tss:' /usr/lib/group | sudo tee -a /etc/group  # this is for fedora silverblue only
sudo usermod -aG tss $USER
```

### Encrypt my password

```bash
echo -n "my-secret-password" | systemd-creds encrypt --with-key=tpm2 --name=resticPassword --pretty - -
# systemd-ask-password -n | systemd-creds encrypt --with-key=tpm2 --name=resticPassword  --pretty - -
# systemd-creds encrypt --with-key=tpm2 --name=resticPassword --pretty from_plaintext.txt -
```

### Add to systemd unit

```ini
[Service]
Environment=RESTIC_PASSWORD_FILE=%d/resticPassword
# Environment=RESTIC_PASSWORD_FILE=${CREDENTIALS_DIRECTORY}/resticPassword
# Environment=RESTIC_PASSWORD_COMMAND="cat ${CREDENTIALS_DIRECTORY}/resticPassword"
# ExecStart=/usr/bin/systemd-creds cat resticPassword
SetCredentialEncrypted=resticPassword: \
        k6iUCUh0RJCQyvL8k8q1UyAAAAABAAAADAAAABAAAAC1lFmbWAqWZ8dCCQkAAAAAgAAAA \
        AAAAAALACMA0AAAACAAAAAAfgAg9uNpGmj8LL2nHE0ixcycvM3XkpOCaf+9rwGscwmqRJ \
        cAEO24kB08FMtd/hfkZBX8PqoHd/yPTzRxJQBoBsvo9VqolKdy9Wkvih0HQnQ6NkTKE== \
```
