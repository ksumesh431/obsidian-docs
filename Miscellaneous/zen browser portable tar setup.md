## **Step 1: Extract the Zen Browser Archive**
1. Open a terminal.
2. Navigate to the directory where the `zen.linux-x86_64.tar.bz2` file is located. For example:
   ```bash
   cd ~/Downloads
   ```
3. Extract the archive:
   ```bash
   sudo tar xvjf zen.linux-x86_64.tar.bz2
   ```

---

## **Step 2: Move the Extracted Folder (zen) to `/opt`**
1. Move the extracted folder to `/opt` (requires `sudo`):
   ```bash
   sudo mv zen /opt/zen
   ```

---

## **Step 3: Create a Symlink for the Zen Binary**
1. Create a symlink to the `zen` binary in `/usr/local/bin` (requires `sudo`):
   ```bash
   sudo ln -s /opt/zen/zen /usr/local/bin/zen
   ```

---

## **Step 4: Create a Desktop Entry**
1. Create a `.desktop` file for Zen Browser:
   ```bash
   sudo vi /usr/local/share/applications/zen.desktop
   ```
2. Add the following content to the file:

```ini
[Desktop Entry]
Name=Zen Browser
Comment=Experience tranquillity while browsing the web without people tracking you!
Exec=zen %u
Icon=/opt/zen/browser/chrome/icons/default/default128.png
Type=Application
MimeType=text/html;text/xml;application/xhtml+xml;x-scheme-handler/http;x-scheme-handler/https;application/x-xpinstall;application/pdf;application/json;
StartupWMClass=zen
Categories=Network;WebBrowser;
StartupNotify=true
Terminal=false
X-MultipleArgs=false
Keywords=Internet;WWW;Browser;Web;Explorer;
Actions=new-window;new-private-window;profilemanager;

[Desktop Action new-window]
Name=Open a New Window
Exec=zen %u

[Desktop Action new-private-window]
Name=Open a New Private Window
Exec=zen --private-window %u

[Desktop Action profilemanager]
Name=Open the Profile Manager
Exec=zen --ProfileManager %u
```

3. Save and exit the file .

---

## **Step 5: Fix the Security Features Warning**
To address the security features warning, create an AppArmor profile for Zen Browser:
1. Create a new AppArmor profile file:
   ```bash
   sudo nano /etc/apparmor.d/zen
   ```
2. Add the following content to the file:
   ```plaintext
   # This profile allows everything and only exists to give the
   # application a name instead of having the label "unconfined"
   abi <abi/4.0>,
   include <tunables/global>
   profile zen /opt/zen/{zen,zen-bin} flags=(unconfined) {
       userns,
       # Site-specific additions and overrides. See local/README for details.
       include if exists <local/zen>
   }
   ```
3. Save and exit the file (`Ctrl+O`, `Enter`, `Ctrl+X`).
4. Restart the AppArmor service:
   ```bash
   sudo systemctl restart apparmor.service
   ```

---

## **Step 6: Verify the Installation**
1. Search for "Zen Browser" in your application menu and launch it.
2. To verify the binary path, open the browser and navigate to `about:support`. Check the "Application Binary" field—it should show `/opt/zen/zen`.

---

## **Step 7: (Optional) Create a Desktop Shortcut**
1. Create a desktop shortcut for Zen Browser:
   ```bash
   ln -s /usr/local/bin/zen ~/Desktop/Zen-Browser
   ```

---

## **Step 8: Clean Up**
1. Delete the downloaded archive if no longer needed:
   ```bash
   rm ~/Downloads/zen.linux-x86_64.tar.bz2
   ```

---

### **Additional Notes**
- If you encounter issues with the taskbar icon after updates, check the `StartupWMClass` value in `about:support` and update the `.desktop` file accordingly.
- If you want to prevent the `.desktop` file from being overwritten during updates, lock it:
  ```bash
  sudo chattr +i /usr/local/share/applications/zen.desktop
  ```
  To unlock it later:
  ```bash
  sudo chattr -i /usr/local/share/applications/zen.desktop
  ```

---

This guide ensures that Zen Browser is installed and configured consistently using `zen` instead of `zen-beta`. Let me know if you need further assistance!



---
# Uninstall and cleanup

Here’s a step-by-step guide to completely uninstall Zen Browser and revert all the changes made during installation:

---

### **Step 1: Remove the Zen Browser Installation Directory**
1. Remove the Zen Browser folder from `/opt`:
   ```bash
   sudo rm -rf /opt/zen
   ```

---

### **Step 2: Remove the Symlink**
1. Remove the symlink created in `/usr/local/bin`:
   ```bash
   sudo rm /usr/local/bin/zen
   ```

---

### **Step 3: Remove the Desktop Entry**
1. Remove the `.desktop` file:
   ```bash
   sudo rm /usr/local/share/applications/zen.desktop
   ```

---

### **Step 4: Remove the AppArmor Profile**
1. Remove the AppArmor profile:
   ```bash
   sudo rm /etc/apparmor.d/zen
   ```
2. Restart the AppArmor service:
   ```bash
   sudo systemctl restart apparmor.service
   ```

---

### **Step 5: Remove User-Specific Files**
1. Remove cached files:
   ```bash
   rm -rf ~/.cache/zen
   ```
2. Remove configuration files:
   ```bash
   rm -rf ~/.config/zen
   ```
3. Remove local application data:
   ```bash
   rm -rf ~/.local/share/zen
   ```

---

### **Step 6: Remove Desktop Shortcut (If Created)**
1. Remove the desktop shortcut:
   ```bash
   rm ~/Desktop/Zen-Browser
   ```

---

### **Step 7: Refresh the Desktop Entry Cache**
1. Refresh the desktop entry cache to remove any lingering entries:
   ```bash
   sudo update-desktop-database
   ```

---

### **Step 8: Verify Uninstallation**
1. Search for "Zen Browser" in your application menu. It should no longer appear.
2. Verify that the `zen` command is no longer available:
   ```bash
   which zen
   ```
   This should return no output.

---

### **Step 9: Reboot (Optional)**
1. Reboot your system to ensure all changes take effect:
   ```bash
   sudo reboot
   ```

---

### **Additional Notes**
- If you locked the `.desktop` file during installation, unlock it before removal:
  ```bash
  sudo chattr -i /usr/local/share/applications/zen.desktop
  ```
- If you find any leftover files or directories, you can search for them:
  ```bash
  sudo find / -name "*zen*"
  ```
  Review the output and delete any remaining Zen Browser-related files or directories.

---

This guide ensures that Zen Browser is completely uninstalled and all associated files are removed. Let me know if you need further assistance!