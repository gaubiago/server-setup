# Server Setup on a Raspberry Pi

* [Ubuntu Tutorial](https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#2-prepare-the-sd-card)

* [Ubuntu 20.04.4 LTS&mdash;Preinstalled Server Image (armhf & arm64)](https://cdimage.ubuntu.com/releases/20.04/release/)

* [Angry IP Scanner](https://angryip.org/) (requires Java Runtime Environment)

* [Generating a new SSH key and adding it to the ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

* Copying SSH public key to a server

  ```sh
  ssh-copy-id -i ~/.ssh/mykey user@host
  ```

* Setting up static IP address on your LAN

  * Install `net-tools` to find out:

    * IP and subnet mask, by running `ifconfig`
    * Gateway, by running `route -n`

  * Set up static IP address either:
  
    * On your router (static DHCP or DHCP reservation); or
    * On your server:

      * If you have a YAML file containing `cloud-init` in `/etc/netplan/`, disable that configuration by creating the file 

        ```sh
        sudo vim /etc/cloud/cloud.cfg.d/99-z-disable-network-config.cfg
        ```

        and appending `network: {config: disabled}` to it.

      * Create the file 

        ```sh
        sudo vim /etc/netplan/51-static-ip-config.yaml
        ```

        and append the following:
        
        ```yaml
        network:
          version: 2
          renderer: networkd
          wifis: # used for wifi adapters; if using an ethernet adapter, change it to `ethernets`
            wlan0:
              dhcp4: no
              addresses: [<ip_address>/<subnet_mask>]
              gateway4: <gateway_ip_address>
              nameservers:
                addresses: [8.8.8.8, 1.1.1.1]
        ```

      * Attempt applying the new configuration:

        ```sh
        sudo netplan try
        ```
      
      > Files in the directories `/etc/cloud/cloud.cfg.d/` and `/etc/netplan/` are read in lexicographical order&mdash;you can pick what names to give to these files.

* Set up port forwarding on your router

* Install and set up Nginx

  * ```sh
    sudo apt update \
    sudo apt upgrade \
    sudo apt install nginx \
    sudo systemctl enable nginx \
    sudo systemctl start nginx \
    nginx -v \
    curl http://127.0.0.1
    ```

  * ```sh
    cd /etc/nginx/sites-available/ \
    sudo cp default gaubert.dev \
    sudo vim gaubert.dev
    ```

  * Change line `server_name _;` to `server_name gaubert.dev www.gaubert.dev;`

  * ```sh
    sudo unlink /etc/nginx/sites-enabled/default \
    sudo ln -s /etc/nginx/sites-available/gaubert.dev /etc/nginx/sites-enabled/gaubert.dev \
    sudo nginx -t
    ```

  * ```sh
    sudo systemctl restart nginx
    ```
  
* Install and set up Certbot

  * ```sh
    sudo apt update \ 
    sudo apt install snapd \
    ```

  * Restart your machine

  * ```sh
    sudo snap install hello-world \
    hello-world \ 
    sudo snap install --classic certbot \
    sudo certbot --nginx
    ```