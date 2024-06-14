
```markdown
# Ultra Low Latency NGINX RTMP Server for GoPro HERO11 BLACK

This tutorial provides step-by-step instructions to set up an ultra-low latency NGINX RTMP server on a Linux Mint system, and includes steps to ensure the server is functioning correctly after a power interruption.

## Prerequisites

- Linux Mint 21.3 Virginia (based on Ubuntu 22.04 Jammy)
- A system with the following specs:
  - Kernel: 5.15.0-107-generic x86_64
  - CPU: 11th Gen Intel Core i7-11700K
  - GPU: NVIDIA GeForce RTX 3070 Ti
  - Memory: 16GB
  - Storage: 1TB NVMe SSD

## Step 1: Install NGINX with RTMP Module

1. Download and extract NGINX source code:

    ```bash
    wget http://nginx.org/download/nginx-1.21.4.tar.gz
    tar -zxvf nginx-1.21.4.tar.gz
    cd nginx-1.21.4
    ```

2. Download and extract NGINX RTMP module:

    ```bash
    wget https://github.com/arut/nginx-rtmp-module/archive/refs/heads/master.zip
    unzip master.zip
    ```

3. Install dependencies:

    ```bash
    sudo apt update
    sudo apt install build-essential libpcre3 libpcre3-dev libssl-dev zlib1g zlib1g-dev -y
    ```

4. Configure and install NGINX with RTMP module:

    ```bash
    ./configure --with-http_ssl_module --add-module=../nginx-rtmp-module-master
    make
    sudo make install
    ```

## Step 2: Configure NGINX RTMP Module

1. Edit the NGINX configuration file:

    ```bash
    sudo nano /usr/local/nginx/conf/nginx.conf
    ```

2. Add the following RTMP configuration to the `nginx.conf` file:

    ```nginx
    rtmp {
        server {
            listen 1935;
            chunk_size 4096;

            application live {
                live on;
                record off;
            }
        }
    }

    http {
        # Existing HTTP configuration...
    }
    ```

3. Save and exit the editor (in `nano`, press `CTRL+O` to save and `CTRL+X` to exit).

## Step 3: Start NGINX

1. Start NGINX:

    ```bash
    sudo /usr/local/nginx/sbin/nginx
    ```

2. If you encounter an error about a duplicate `http` directive, remove the duplicate entry from the `nginx.conf` file.

    ```bash
    sudo nano /usr/local/nginx/conf/nginx.conf
    ```

    Remove or comment out the duplicate `http { ... }` block.

## Step 4: Verify NGINX Installation

1. Check if NGINX is running:

    ```bash
    ps aux | grep nginx
    ```

2. Review NGINX logs for any issues:

    ```bash
    sudo tail -n 50 /usr/local/nginx/logs/error.log
    ```

3. Test NGINX configuration:

    ```bash
    sudo /usr/local/nginx/sbin/nginx -t
    ```

## Step 5: Ensure NGINX Functionality After Power Interruption

In case of a power interruption, follow these steps to check and ensure NGINX functionality:

1. **Check NGINX Status**:

    ```bash
    ps aux | grep nginx
    ```

2. **Review NGINX Logs**:

    ```bash
    sudo tail -n 50 /usr/local/nginx/logs/error.log
    ```

3. **Test NGINX Configuration**:

    ```bash
    sudo /usr/local/nginx/sbin/nginx -t
    ```

4. **Restart NGINX**:

    ```bash
    sudo /usr/local/nginx/sbin/nginx -s reload
    ```

5. **Check System Logs**:

    ```bash
    sudo journalctl -xe
    ```

6. **Check Disk Integrity**:
    Power interruptions can sometimes cause filesystem corruption. Check your disk integrity as follows:

    - Unmount the filesystem (if possible):

        ```bash
        sudo umount /dev/nvme0n1p1
        ```

    - Run `fsck` to check and repair the filesystem:

        ```bash
        sudo fsck -f /dev/nvme0n1p1
        ```

    - Remount the filesystem:

        ```bash
        sudo mount /dev/nvme0n1p1 /boot/efi
        ```

By following these steps, you can determine if the power interruption caused any issues with NGINX and take the necessary actions to resolve them.

## Stopping the NGINX Server

To stop the NGINX server, follow these steps:

1. **Stop NGINX using the NGINX binary**:
   If you manually installed NGINX, use this command:

   ```bash
   sudo /usr/local/nginx/sbin/nginx -s stop
   ```

2. **Terminate NGINX Processes**:
   If the above method does not work, you can manually terminate the NGINX processes:

   ```bash
   sudo killall nginx
   ```

   Or, find and kill the processes by their PID:

   ```bash
   ps aux | grep nginx
   sudo kill -QUIT <PID>
   ```

   Replace `<PID>` with the actual process ID of the NGINX process you want to terminate.

## Checking NGINX Status

To check the status of the NGINX server, follow these steps:

1. **Check if NGINX is running**:

    ```bash
    ps aux | grep nginx
    ```

2. **Review NGINX Logs**:
   Check the NGINX logs for any issues:

    ```bash
    sudo tail -n 50 /usr/local/nginx/logs/error.log
    ```

3. **Test NGINX Configuration**:
   Test the NGINX configuration to ensure there are no syntax errors:

    ```bash
    sudo /usr/local/nginx/sbin/nginx -t
    ```

4. **Check System Logs**:
   Check the system logs for any related errors or warnings:

    ```bash
    sudo journalctl -xe
    ```
```
