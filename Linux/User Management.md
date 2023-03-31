## Newer Linux
Chage the expiry date of users password
```bash
chage -M 365 simon
```
The above command will set the password for a user called simon to 365 days

To check if a users account is locked
```bash
faillock --user simon
```

To unlock a users account
```bash
faillock --user simon --reset
```

## Older Linux
Some older version of linux use pam_tally2

To if a users account is locked 
```bash
pam_tally2 --user simon
```

Unlock a users account
```bash
pam_tally2 --user simon --reset
```
