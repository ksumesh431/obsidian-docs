

This guide provides step-by-step instructions to set up Zen Browser Beta on Ubuntu, including creating a desktop entry, handling the security features warning, and ensuring the binary is named `zen-beta`.

---

## **Step 1: Extract the Zen Browser Beta Archive**
1. Open a terminal.
2. Navigate to the directory where the `zen.linux-x86_64.tar.bz2` file is located. For example:
   ```bash
   cd ~/Downloads
   ```
3. Extract the archive:

---

## **Step 2: Move the Extracted Folder (zen) to `/opt`**
1. Move the extracted folder to `/opt` (requires `sudo`):
   ```bash
   sudo mv zen /opt/zen-beta
   ```

---

## **Step 3: Create a Symlink for the Zen Beta Binary**
1. Create a symlink to the `zen` binary in `/usr/local/bin` (requires `sudo`):
   ```bash
   sudo ln -s /opt/zen-beta/zen /usr/local/bin/zen-beta
   ```

---

## **Step 4: Create a Desktop Entry**
1. Create a `.desktop` file for Zen Browser Beta:
   ```bash
   sudo nano /usr/local/share/applications/zen-beta.desktop
   ```
2. Add the following content to the file:

``` ini

   [Desktop Entry]
   Name=Zen Browser Beta
   Comment=Experience tranquillity while browsing the web without people tracking you!
   Exec=zen-beta %u
   Icon=/opt/zen-beta/browser/chrome/icons/default/default128.png
   Type=Application
   MimeType=text/html;text/xml;application/xhtml+xml;x-scheme-handler/http;x-scheme-handler/https;application/x-xpinstall;application/pdf;application/json;
   StartupWMClass=zen-beta
   Categories=Network;WebBrowser;
   StartupNotify=true
   Terminal=false
   X-MultipleArgs=false
   Keywords=Internet;WWW;Browser;Web;Explorer;
   Actions=new-window;new-private-window;profilemanager;

   [Desktop Action new-window]
   Name=Open a New Window
   Exec=zen-beta %u

   [Desktop Action new-private-window]
   Name=Open a New Private Window
   Exec=zen-beta --private-window %u

   [Desktop Action profilemanager]
   Name=Open the Profile Manager
   Exec=zen-beta --ProfileManager %u

```

3. Save and exit the file (`Ctrl+O`, `Enter`, `Ctrl+X`).

---

## **Step 5: Fix the Security Features Warning**
To address the security features warning, create an AppArmor profile for Zen Browser Beta:
1. Create a new AppArmor profile file:
   ```bash
   sudo nano /etc/apparmor.d/zen-beta
   ```
2. Add the following content to the file:
   ```plaintext
   # This profile allows everything and only exists to give the
   # application a name instead of having the label "unconfined"
   abi <abi/4.0>,
   include <tunables/global>
   profile zen-beta /opt/zen-beta/{zen,zen-bin} flags=(unconfined) {
       userns,
       # Site-specific additions and overrides. See local/README for details.
       include if exists <local/zen-beta>
   }
   ```
3. Save and exit the file (`Ctrl+O`, `Enter`, `Ctrl+X`).
4. Restart the AppArmor service:
   ```bash
   sudo systemctl restart apparmor.service
   ```

---

## **Step 6: Verify the Installation**
1. Search for "Zen Browser Beta" in your application menu and launch it.
2. To verify the binary path, open the browser and navigate to `about:support`. Check the "Application Binary" field—it should show `/opt/zen-beta/zen`.

---

## **Step 7: (Optional) Create a Desktop Shortcut**
1. Create a desktop shortcut for Zen Browser Beta:
   ```bash
   ln -s /usr/local/bin/zen-beta ~/Desktop/Zen-Browser-Beta
   ```

---

## **Step 8: Clean Up**
1. Delete the downloaded archive if no longer needed:
   ```bash
   rm ~/Downloads/zen.linux-x86_64.tar.bz2
   ```


