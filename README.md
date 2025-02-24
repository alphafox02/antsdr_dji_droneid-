# ANTSDR E200 DJI DroneID Firmware with Network Interface Integration

This firmware is designed to enable DJI DroneID detection on the ANTSDR E200 device and seamlessly transmit the results to a network interface and port. With just a few simple steps, users can enhance their ANTSDR E200 capabilities and utilize it as a powerful tool for drone identification and network integration.

## Features:

- **DJI DroneID Detection:** Identify DJI drones within the vicinity using the ANTSDR E200.  
- **Network Interface Integration:** Transmit detection results to a specified network interface and port for further analysis or monitoring.  
- **Easy Installation:** Simply copy the contents of the provided zip file to the root directory of an SD card for the ANTSDR E200 device.  
- **Compatibility with Kismet:** Serve as a capture device for Kismet, allowing users to capture and analyze wireless network data alongside DJI DroneID detection.

## Usage:

1. **Preparation:**
   - Configure your host LAN port to a static IP of `192.168.1.9` to reach thr stock ANTSDR firmware at 192.168.1.10.
   - Firmware for WarDragon Pro requires the host LAN port to be 172.31.100.1 to reach the ANTSDR at 172.31.100.2

2. **Installation:**
   - Download the provided zip file.
   - Extract the contents to the root directory of an SD card.

3. **Execution:**
   - Insert the SD card into the ANTSDR E200 device.
   - Power on the device.

4. **Usage with Kismet (Nightly Releases):**
   - Ensure Kismet is running.
   - Execute the following command:
     ```bash
     kismet_cap_antsdr_droneid --source antsdr-droneid:host=192.168.1.10,port=41030 --connect localhost:3501 --tcp
     ```
   - Ensure the LAN port IP address matches the configuration.

5. **Integration with DroneID and DragonSync (for CoT/TAK):**
   1. **Run `dji_receiver.py`:**  
      After the ANTSDR E200 is running with the new firmware, execute the [dji_receiver.py](https://github.com/alphafox02/DroneID/blob/main/dji_receiver.py) script to capture incoming DroneID data from the ANTSDR E200 on your local machine.
   2. **Run `zmq_decoder.py`:**  
      In the same DroneID repository, execute `zmq_decoder.py` with the following command (substituting IP/ports as needed):  
      ```bash
      python3 zmq_decoder.py --dji 127.0.0.1:4221
      ```
      This listens on port `4221` for incoming data from `dji_receiver.py` and provides the decoded results on port `4224`.
   3. **Run `dragonsync.py`:**  
      From the [DragonSync](https://github.com/alphafox02/DragonSync) repository, run:
      ```bash
      python3 dragonsync.py
      ```
      This connects to the `zmq_decoder.py` output on port `4224`, receiving the decoded drone information and converting it into Cursor-On-Target (CoT) or forwarding it to a TAK server for advanced situational awareness.

## Important Note: Changing the ANTSDR E200 IP Address

If you decide to **change the IP address** that the ANTSDR E200 uses when running this DJI firmware, please be aware of the following:

- You **must also update**:
  - The static IP of your host computer interface.
  - Any **hard-coded IP** references in `dji_receiver.py`.
  - Any **command-line flags** that reference the ANTSDR E200’s IP address (e.g., in Kismet commands).

### Steps to Change the IP:

1. **Power off** the ANTSDR E200.  
2. **Flip the switch** on the ANTSDR E200 to **QSPI mode**.  
3. **Power on** the device and **SSH** into the **current IP** using:
   - **Username:** `root`  
   - **Password:** `analog`  
4. **Set a new IP** by running the following command (replace `NEW_IP_ADDRESS` with your desired IP):
   ```bash
   fw_setenv ipaddr_eth NEW_IP_ADDRESS
5. Power off the ANTSDR E200.
6. Flip the switch back to SD mode.
7. Power on the device again, and now the DJI firmware will be reachable at the new IP.

**Note:** QSPI mode will remain on the new IP firmware, but changing the IP for UHD-based firmware may involve a separate process.
**Note:** Nightly releases of Kismet are required for integration with the ANTSDR E200 device.

## Disclaimer:

Please note that the use of this firmware may be subject to regulations and restrictions in your region. Ensure compliance with local laws and regulations regarding wireless communication and drone detection. Also, I did not personally produce this firmware. It was provided by the manufacturer in order to test the network capabilities. 
