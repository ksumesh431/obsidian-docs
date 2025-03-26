
# Password policy
To remediate the password policy issue on your Amazon Linux 2 instance, you need to configure the minimum password length requirements. Here's how to do it:

### Configure Password Policy (Minimum Length)

1. First, make sure the `libpwquality` package is installed:
   ```
   sudo yum install libpwquality
   ```

2. Create or edit the `/etc/security/pwquality.conf` file:
   ```
   sudo nano /etc/security/pwquality.conf
   ```

3. Add or modify the `minlen` setting to ensure passwords are at least 15 characters:
   ```
   # Set the minimum length parameter
   minlen = 15
   
   # You may also want to add other password complexity requirements
   dcredit = -1     # Require at least one digit
   ucredit = -1     # Require at least one uppercase letter
   lcredit = -1     # Require at least one lowercase letter
   ocredit = -1     # Require at least one special character
   ```

4. Ensure PAM is configured to use pwquality. Edit the `/etc/pam.d/system-auth` file:
   ```
   sudo nano /etc/pam.d/system-auth
   ```

5. Look for a line containing `pam_pwquality.so` or add it if it doesn't exist in the password section:
   ```
   password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=
   ```

6. Similarly, check and update the `/etc/pam.d/password-auth` file:
   ```
   sudo nano /etc/pam.d/password-auth
   ```

7. Add or modify the same line as in step 5.

These changes will:
- Require passwords to be at least 15 characters long
- Add additional complexity requirements (digits, upper/lowercase, special characters)
- Enforce these requirements when users change their passwords

After making these changes, the password policy will meet the requirements checked by your script, and users will be required to use stronger passwords.


---

# Time server
I see that your Amazon Linux 2 server is using chronyd with time servers that aren't in your valid list. You have two options:

### Option 1 (RECOMMENDED): Add your current servers to the valid list in the script

Modify the `validNtpServers` array at the beginning of your script to include the servers shown in your output:

```bash
validNtpServers=("PHLDC4" "PHLDC5" "PRDUSE1PRODDC1" "PRDUSE1PRODDC2" "PRDCAC1PRODDC1" "PRDCAC1PRODDC2" "AWSISSPRODDC1" "AWSISSPRODDC3" "AWSISSPRODDC4" "DRUSE2PRODDC3" "DRUSE2PRODDC4" "MALPRODDC1" "MALPRODDC2" "us-central-1.clearnet.pw" "tick.nowherenet.org" "169.254.169.123" "157.245.125.229" "44.190.5.123")
```

### Option 2: Reconfigure chronyd to use your valid servers

1. Edit the chrony configuration file:
   ```
   sudo nano /etc/chrony.conf
   ```

2. Comment out or remove the current server entries and add your valid NTP servers. For example:
   ```
   # Comment out existing server lines
   # server 0.amazon.pool.ntp.org iburst
   # server 1.amazon.pool.ntp.org iburst
   # server 2.amazon.pool.ntp.org iburst
   # server 3.amazon.pool.ntp.org iburst

   # Add your valid servers
   server PHLDC4 iburst
   server PHLDC5 iburst
   server PRDUSE1PRODDC1 iburst
   server PRDUSE1PRODDC2 iburst
   # Add other valid servers as needed
   ```

3. Restart the chronyd service:
   ```
   sudo systemctl restart chronyd
   ```

4. Check the new sources:
   ```
   chronyc sources
   ```

Note: The server at 169.254.169.123 is the AWS Time Sync Service, which is commonly used in AWS instances. It's reliable and provides accurate time within the AWS network, so you might want to keep it regardless.


---

# Lockout policy
To remediate the account lockout policy issue on your Amazon Linux 2 server, you need to configure PAM to implement account lockout after failed login attempts. Here's how to do it:

### Configure Account Lockout Policy

1. First, make sure the `pam_faillock` module is installed:
   ```
   sudo yum install pam
   ```

2. Edit the `/etc/pam.d/password-auth` file:
   ```
   sudo nano /etc/pam.d/password-auth
   ```

3. Add the following lines at the top of the `auth` section (before any other `auth` lines):
   ```
   auth        required      pam_faillock.so preauth silent audit deny=5 unlock_time=1800
   auth        [default=die] pam_faillock.so authfail audit deny=5 unlock_time=1800
   ```

4. Add this line after the `auth` section but before the `account` section:
   ```
   account     required      pam_faillock.so
   ```

5. Repeat the same changes for the `/etc/pam.d/system-auth` file:
   ```
   sudo nano /etc/pam.d/system-auth
   ```

6. Make the same additions to the `system-auth` file as you did with `password-auth`.

Here's an example of how the edited section of your file should look:

```
#%PAM-1.0
# This file is auto-generated.
# User changes will be destroyed the next time authconfig is run.
auth        required      pam_faillock.so preauth silent audit deny=5 unlock_time=1800
auth        [default=die] pam_faillock.so authfail audit deny=5 unlock_time=1800
auth        required      pam_env.so
auth        sufficient    pam_unix.so nullok try_first_pass
auth        requisite     pam_succeed_if.so uid >= 1000 quiet_success
auth        required      pam_deny.so

account     required      pam_faillock.so
account     required      pam_unix.so
```

These changes will:
- Lock accounts after 5 failed login attempts
- Keep them locked for 1800 seconds (30 minutes)
- Track failed login attempts
- Reset the count after a successful login
