## Add user to a group
sudo usermod -aG username group

## Remove user from a group
sudo gpasswd -d username group

## Change user password expiry date
Change the expiry date of users password
```bash
chage -M 365 simon
```
The above command will set the password for a user called simon to 365 days

## To check if a users account is locked
```bash
faillock --user simon
```
**On older Linux versions**
```bash
pam_tally2 --user simon
```
## To unlock a users account
```bash
faillock --user simon --reset
```
**On older Linux versions**
```bash
pam_tally2 --user simon --reset
```
