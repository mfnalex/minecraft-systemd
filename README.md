# Installation

Simply install all required files and create the server directory. Run the following commands as root:

```sh
# Step zero: Install required packages. If you're not using Debian, you might have to run a different command.
apt install git wget openjdk-17-jre-headless sudo

# Step one: Clone this repository into any folder you like
cd /tmp
git clone https://github.com/mfnalex/minecraft-systemd
cd minecraft-systemd

# Step two: Install the systemd units
cp minecraft@.service minecraft@.socket /etc/systemd/system

# Step three: Create a user "minecraft". You can skip this if you already have a minecraft user
adduser --no-create-home --disabled-password minecraft

# Step four: Create the server directory. You may change this directory to something else, but then you'll also have to adjust /etc/systemd/system/minecraft@.service
mkdir -p /opt/minecraft
chown minecraft: /opt/minecraft

# Step five: Install sudoers file
cp sudoers /etc/sudoers.d/
```

# Setup your first server

Let's setup a server called "test-server" using Spigot 1.20.4.

```sh
# Step zero: Become the minecraft user
su - minecraft

# Step one: Download BuildTools to any folder you like
mkdir -p /tmp/buildtools
cd /tmp/buildtools
wget https://hub.spigotmc.org/jenkins/job/BuildTools/lastSuccessfulBuild/artifact/target/BuildTools.jar

# Step two: Run BuildTools, for example 1.20.4.
java -Xmx1G -jar BuildTools.jar --rev 1.20.4 --final-name server.jar

# Step three: Create the test server folder and copy the spigot server .jar
mkdir -p /opt/minecraft/test-server
cp server.jar /opt/minecraft/test-server

# Step four: Accept Mojang's EULA (https://www.minecraft.net/en-us/eula)
echo eula=true > /opt/minecraft/test-server/eula.txt

# Step five: Optionally copy server.env into the server folder and adjust it
cp /tmp/minecraft-systemd/server.env /opt/minecraft/test-server/
```

# Run your first server
Now you can run the server. Use the following commands as root to control your server. If you want to run them using the minecraft user, you'll need to use `sudo`.

```sh
# Start a server
systemctl start minecraft@<servername> # e.g. systemctl start minecraft@test-server

# Stop a server
systemctl stop minecraft@<servername>

# Restart a server
systemctl restart minecraft@<servername>

# See server log
journalctl -u minecraft@<servername>

# ... or follow the latest log entries
journalctl -fu minecraft@<servername>

# Send commands
echo say Hello from console > /run/minecraft/<servername>.stdin
```

