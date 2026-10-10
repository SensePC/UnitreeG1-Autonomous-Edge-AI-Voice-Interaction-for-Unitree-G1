# Unitree G1 Setup and Troubleshooting Guide

## 1. Connecting the Robot to a New Wi-Fi Network

After connecting to the robot via Ethernet and SSH (see Section 2), configure the new Wi-Fi network.

### Create a New Wi-Fi Network Configuration

Run the following command:

```bash
wpa_passphrase <ssid> [passphrase]
```

Example:

```bash
wpa_passphrase SenseLAB atlqaqwfwaf
```

Example output:

```text
network={
    ssid="SenseLAB"
    #psk="atlqaqwfwaf"
    psk=692dcf02ac351de45654b9be00b798f3242d860120becf6e3a56c2ed3caf6b2c
}
```

Copy the generated network configuration into:

`/etc/wpa_supplicant/wpa_supplicant.conf`

Edit the file using `nano`:

```bash
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
```

For the new configuration to take effect, a reboot is recommended. Alternatively, run:

```bash
sudo wpa_cli -i wlan0 reconfigure
```

## 2. Connecting the Laptop to the Robot via SSH (Ethernet or Wi-Fi)

### Step 1: Establish a Temporary Wired Connection

Connect your computer to the robot using an Ethernet cable.

To connect via Ethernet, assign a static LAN IP address to your computer in the following range:

`192.168.123.X`

For example, use `192.168.123.100`.

**Important:** Do not use `192.168.123.164` for your computer, as this is the robot's Jetson IP address.

### Step 2: Log In to the Robot via SSH

Open a terminal on your computer and connect to the Jetson using its default IP address.

Run either command:

```bash
ssh 192.168.123.164
```

or:

```bash
ssh unitree@192.168.123.164
```

When prompted, the default password is:

`123`

At this point, you can run the program directly on the robot.

If you want to work entirely wirelessly, continue with the following steps.

### Step 3: Find the Robot's Wi-Fi IP Address

Once logged into the robot's terminal, run:

```bash
ip a
```

Look through the output for the wireless network interface, usually named `wlan0`.

It is typically interface number 7. Look for the IP address displayed after `inet` on that interface, followed by a subnet suffix such as `/24`.

The address should be assigned by your router. For example:

`147.27.113.X`

Write down the robot's Wi-Fi IP address.

### Step 4: Connect Wirelessly via SSH

You can now disconnect the Ethernet cable and connect directly over Wi-Fi:

```bash
ssh unitree@<NEW_WIFI_IP>
```

Replace `<NEW_WIFI_IP>` with the IP address identified in the previous step.

## 3. Running the Program on the Robot

In the robot's terminal, navigate to the projects directory:

```bash
cd projects
```

### Greek-Only Version

Navigate to the Greek-only project directory:

```bash
cd babis_test
```

### English and Greek Version

Navigate to the bilingual project directory:

```bash
cd babis_test_v2
```

### Run the Program

The following command works for both versions:

```bash
python3 main.py
```

To stop the program, press:

`Ctrl+C`

## 4. Changing the Welcome Phrase or Prompt

To change the welcome phrase or prompt, navigate to the `babis_test` directory and open the configuration file:

```bash
nano config.py
```

Make your changes, then save and exit:

1. Press `Ctrl+O` to save.
2. Press `Enter` to confirm the filename.
3. Press `Ctrl+X` to exit.

You can also edit the language model configuration in:

```bash
nano llm.py
```

Make the necessary changes and save the file.

## 5. Microphone and Audio Driver

The following repository may be useful for setting up the robot's microphone and audio functionality:

https://github.com/experientialtech/g1-audio-driver

## 6. Audio Troubleshooting: If the Sound Is Not Working

If the audio is not working, run the following commands in the robot's terminal.

### Step 1: Stop the Audio Driver Process

```bash
pkill -f g1_audio_driver.py
```

### Step 2: Stop the Audio Driver Service

```bash
systemctl --user stop g1-audio-driver
```

### Step 3: Remove Old Audio Pipes

```bash
rm -f /tmp/g1_*.pipe
```

### Step 4: Restart the Audio Driver

Navigate to the audio driver directory:

```bash
cd ~/g1-audio-driver
```

Run the installation script with the start option:

```bash
./install.sh --start
```

## 7. Optional: Connecting to a New Wi-Fi Network Using NetworkManager

**Note:** These commands are currently not required for the setup described above. Keep this section for reference only.

### Scan for Nearby Wi-Fi Networks

To refresh the list of available networks:

```bash
sudo nmcli device wifi rescan
sudo nmcli device wifi list
```

### Connect to the New Wi-Fi Network

Replace `New_SSID` and `New_Password` with your network details:

```bash
sudo nmcli device wifi connect "New_SSID" password "New_Password"
```

NetworkManager should disconnect from the old Wi-Fi network and switch to the new one.

Additional documentation:

https://docs.westonrobot.com/guides/unitree/g1_internet_guide/
