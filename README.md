# iReport 5.6.0 Installation and Setup on Ubuntu (Java 7/8)

---

## 1. Install Java 7 (Zulu JDK)

1. Install required tools:

```bash
sudo apt install apt-transport-https ca-certificates wget gnupg -y
```

2. Add the Azul Zulu repository:

```bash
wget -qO - https://repos.azul.com/azul-repo.key | sudo gpg --dearmor -o /usr/share/keyrings/azul-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/azul-archive-keyring.gpg] https://repos.azul.com/zulu/deb stable main" | sudo tee /etc/apt/sources.list.d/zulu.list
sudo apt update
```

3. Install Zulu JDK 7:

```bash
sudo apt install zulu-7 -y
```

4. Verify installation:

```bash
java -version
javac -version
```

Expected output:

```
openjdk version "1.7.0_xxx"
OpenJDK Runtime Environment (Zulu 7.x.x.x)
OpenJDK 64-Bit Server VM (Zulu 7.x.x.x)
```

> Note: Java 7 will be installed at a path like: `/usr/lib/jvm/zulu7.56.0.11-ca-jdk7.0.352-linux_x64`

---

### Optional: Install Java 8 (fallback)

```bash
sudo apt install openjdk-8-jdk -y
java -version
```

* Useful if iReport 5.6.0 has issues with Java 7 on newer Ubuntu versions.
* Path usually: `/usr/lib/jvm/java-8-openjdk-amd64`

---

## 2. Download and Extract iReport 5.6.0

1. Download the iReport 5.6.0 ZIP file from SourceForge:

[Download iReport 5.6.0](https://altushost-swe.dl.sourceforge.net/project/erpbarcode/JasperSoft/iReport-5.6.0.zip)

2. Move the downloaded file to `Downloads` (if not already there):

```bash
mv ~/Downloads/iReport-5.6.0.zip ~/Downloads/
```

3. Extract it to `/opt`:

```bash
sudo unzip ~/Downloads/iReport-5.6.0.zip -d /opt/
```

Resulting folder: `/opt/iReport-5.6.0`

---

## 3. Create a Wrapper Script for iReport

1. Create the script:

```bash
sudo nano /usr/local/bin/ireport7
```

2. Paste:

```bash
#!/bin/bash
# Set Java 7 explicitly
export JAVA_HOME=/usr/lib/jvm/zulu7.56.0.11-ca-jdk7.0.352-linux_x64
export PATH=$JAVA_HOME/bin:$PATH

# Increase allowed open file descriptors
ulimit -n 8192

# Optional: use 32-bit JVM mode if available
# export _JAVA_OPTIONS="-d32"

# Launch iReport
/opt/iReport-5.6.0/bin/ireport "$@"
```

3. Make it executable:

```bash
sudo chmod +x /usr/local/bin/ireport7
```

4. Test the script:

```bash
ireport7
```

---

## 4. Configure iReport Icon

1. Copy your icon to the iReport folder:

```bash
sudo cp /home/mahmudul/Downloads/ireportlogo.png /opt/iReport-5.6.0/ireport.png
```

---

## 5. Create Desktop Launcher

1. Create a `.desktop` file:

```bash
mkdir -p ~/.local/share/applications
nano ~/.local/share/applications/ireport7.desktop
```

2. Paste:

```ini
[Desktop Entry]
Version=1.0
Type=Application
Name=iReport Designer 5.6.0
Comment=Launch iReport 5.6.0 with Java 7
Exec=/usr/local/bin/ireport7
Icon=/opt/iReport-5.6.0/ireport.png
Terminal=false
Categories=Development;IDE;
StartupNotify=true
```

3. Make the launcher executable:

```bash
chmod +x ~/.local/share/applications/ireport7.desktop
```

4. Refresh desktop database:

```bash
update-desktop-database ~/.local/share/applications/
```

5. Launch iReport via the menu: Press **Super / Windows key** → search **“iReport Designer 5.6.0”** → click the icon

---

## 6. Troubleshooting Tips

### 6.1 GTK Warnings

```
Gtk-Message: Failed to load module "canberra-gtk-module"
```

**Fix:**

```bash
sudo apt install libcanberra-gtk-module libcanberra-gtk3-module -y
```

### 6.2 JNA Temporary Library Warnings

* Harmless; safe to ignore.

### 6.3 Memory / File Descriptor Limits

* Ensure wrapper script includes:

```bash
ulimit -n 8192
```

* Adjust JVM memory in `/opt/iReport-5.6.0/etc/ireport.conf` if needed:

```text
-J-Xms256m -J-Xmx1024m -J-XX:MaxPermSize=512m
```

### 6.4 Icon Not Showing

* Confirm `.desktop` file points to correct path:

```ini
Icon=/opt/iReport-5.6.0/ireport.png
```

* Make launcher executable:

```bash
chmod +x ~/.local/share/applications/ireport7.desktop
```

* Refresh database:

```bash
update-desktop-database ~/.local/share/applications/
```

---

### Summary

* iReport 5.6.0 launches reliably with Java 7
* Wrapper script ensures proper memory and file descriptors
* Desktop launcher includes custom icon
* GTK and JNA warnings minimized
