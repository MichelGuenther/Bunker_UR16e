# Bunker_UR16e

🌀 Bunker Pro 2.0 & UR16e ROS 2 System

Dieses Repository enthält die Konfiguration und Installationsskripte für die Kombination aus einer AgileX Bunker Pro 2.0 Basis und einem Universal Robots UR16e Arm unter ROS 2 Jazzy (Ubuntu 24.04).
📋 Voraussetzungen

OS: Ubuntu 24.04 LTS

ROS 2: Jazzy Jalisco (Desktop-Full empfohlen)

Hardware: CAN-USB Adapter (für Bunker), Ethernet-Verbindung (für UR16e)

🚀 Installation
1. System-Treiber (UR16e)

Der UR-Treiber wird stabil über die offiziellen Paketquellen installiert, um Versionskonflikte beim Kompilieren zu vermeiden.

Befehle:
```
sudo apt update
sudo apt install ros-jazzy-ur-robot-driver ros-jazzy-ur-moveit-config ros-jazzy-teleop-twist-keyboard -y
```
2. Workspace & Automatisches Setup

Anstatt alles manuell zu machen, nutzt du das vorbereitete Setup-Skript. Es erstellt den Workspace, clont die AgileX-Repos und fix die fehlerhafte Motor-Schleife im SDK automatisch.
Bash
```
# In dein Projekt-Verzeichnis gehen
cd ~/Bunker_UR16e

# Skript ausführbar machen und starten
chmod +x scripts/setup_workspace.sh
./scripts/setup_workspace.sh
```
Was das Skript erledigt:

    Erstellt den Ordner ~/bunker_ur16e_ws/src.

    Clont ugv_sdk und bunker_ros2.

    Fix den "Stack Smashing" Bug in der bunker_base.hpp.

    Kompiliert den gesamten Workspace mit colcon build.
```
source install/setup.bash
```
⚙️ Hardware-Setup
Netzwerk (UR16e)

PC IP: 192.168.1.101 (Netzmaske: 255.255.255.0, Gateway leer lassen!)

UR16e IP: 192.168.1.102

Firewall: Muss deaktiviert sein, damit der Roboter Daten senden darf:
```
sudo ufw disable
```
CAN-Bus (Bunker)

Der CAN-USB Adapter muss vor dem Start aktiviert werden:
```
sudo modprobe gs_usb
sudo ip link set can0 up type can bitrate 500000
```
🚦 Betrieb
Schritt 1: UR16e Treiber

Am UR-Tablet das Programm mit dem Knoten External Control starten.

Am PC folgenden Befehl ausführen:
```
source /opt/ros/jazzy/setup.bash
ros2 launch ur_robot_driver ur_control.launch.py ur_type:=ur16e robot_ip:=192.168.1.102 launch_rviz:=true
```
Schritt 2: Bunker Base

In einem neuen Terminal:
```
source /opt/ros/jazzy/setup.bash
source ~/bunker_ur16e_ws/install/setup.bash
ros2 launch bunker_base bunker_base.launch.py
```
Schritt 3: Steuerung

Tastatur (Bunker): 
```
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

Grafisch (UR16e): 
```
ros2 launch ur_moveit_config ur_moveit.launch.py ur_type:=ur16e robot_ip:=192.168.1.102 launch_rviz:=true
```
