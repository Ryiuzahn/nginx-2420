# Tutorial: Adding A Firewall Using UFW And Reverse Proxy Server With NGINX

---

- **This tutorial extends from the NGINX web server setup from Assignment 3 part 1 and we will modify it by adding a firewall using Uncomplicated Firewall (UFW) and adding a reverse proxy server to our NGINX server block to connect our backend service.**

### Prerequisites

Before you begin, ensure you have completed Assignment 3p1 setup:

- Backend binary (`hello-server`) is placed on your server.

- Service file (`backend.service`) is created and configured to run the `backend` as a service.

- NGINX is installed and configured to serve your domain.

### STEP 1: Setting Up The Firewall With UFW


- To ensure at the system is up to date and at the same time resolve some compatibility issues, we should start by updating the system and kernel:

```bash
sudo pacman -Syu
```


- We should reboot the system to ensure that everything updated and refreshed properly:

```bash
sudo systemctl reboot
```


- **Install UFW (if not already installed):**

```bash
   sudo pacman -S ufw
```

  
2. **Allow SSH and enable UFW:** To make things easier for us, we are going to allow ssh from anywhere.

  
```bash
   sudo ufw allow SSH
```

  
3. **Allow access to specific ports (80 for HTTP):** Since we are creating a web server we also want to allow http connections
  

```bash
   sudo ufw allow 80/tcp
```

  
Now we have defined some rules for our firewall, but we haven't turned it on yet. We can turn our firewall on with this command:

  
```bash
sudo ufw enable
```

  
4. **Verify the firewall settings:**We can check the status of ufw with the command

  
```bash
   sudo ufw status verbose
```

  

### Step 2: Configure NGINX as a Reverse Proxy
  

1. **Edit NGINX Configuration:**

  
Modify your NGINX server block configuration (typically found in `/etc/nginx/sites-enabled/2420-nginx.conf`).

For this assignment we are going to use the server  block configured in Assignment 3 part 1.
  

```bash
sudo vim /etc/nginx/sites-enabled/2420-nginx.conf
```

  
Now Edit  the file and make changes to the server block. The Final result should look similar to the server block below.


```nginx

   server {

       listen 80;
       server_name your_server_ip;

		root /web/html/nginx-2420;
		index index.html;  

		location / {
			try_files $uri $uri/ =404;
		}

       location /api/ {

           proxy_pass http://127.0.0.1:8080;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;

       }

   }

```

  

**Note:** Replace `your_server_ip` with the actual ip address  of your Arch server.


2. **Test NGINX Configuration:**


Ensure the NGINX configuration is valid:
  

```bash
   sudo nginx -t
```


3. **Reload NGINX:**
  

```bash
   sudo systemctl reload nginx
```


### Step 3: Test Backend Connection


1. **Transfer the Binary to Your Server:**


   Use `sftp` (secure copy) or another file transfer method to upload the `hello-server` binary to your server. Replace `<local_path_to_backend>` with the path to your local `backend` binary and `<remote_server_address>` with your server's address:


First connect to your server using sftp

```bash
 scp <local_path_to_backend> username@<remote_server_address>:/path/to/destination/
```

  
   For example:
  

```bash
   scp ./backend username@example.com:/home/user/
```

1. **Verify Backend Service Status:**

  
   Check if the `backend` service is running:

  
   ```bash

   sudo systemctl status backend

   ```


2. **Test Backend Routes:**


   Use `curl` or a similar tool to test the backend routes:


```bash

   curl http://127.0.0.1:8080/hey
   curl http://127.0.0.1:8080/echo -d "Hello, Backend!"

```


   You should receive responses from the backend service.


### Step 4: Access Backend via NGINX

  

1. **Access Backend via NGINX:**

  

   Open a web browser and navigate to `http://your_domain.com`. NGINX should now proxy requests to your backend service.

  

### Screenshots

  

Include screenshots demonstrating:

  

- UFW status (`sudo ufw status verbose`)

- NGINX configuration file (`/etc/nginx/sites-available/your_domain.conf`)

- Successful backend route tests (`curl` commands)

- Browser access to backend via NGINX (`http://your_domain.com`)

  
  
### Step 1: Place Backend Binary on Your System

  

### Step 2: Create a Systemd Service File for the Backend

  

1. **Create the Service File:**

  

   Use a text editor to create a new systemd service file for your backend (`backend.service`). For example:

  

   ```bash

   sudo nano /etc/systemd/system/backend.service

   ```

  

2. **Add Service Configuration:**

  

   Inside the service file, add the following configuration (adjust `WorkingDirectory` and `ExecStart` paths as needed):

  

   ```ini

   [Unit]

   Description=Backend Service

   After=network.target

  

   [Service]

   User=your_username

   Group=your_group

   WorkingDirectory=/path/to/backend/directory

   ExecStart=/path/to/backend/binary

   Restart=always

  

   [Install]

   WantedBy=multi-user.target

   ```

  

   Replace `your_username`, `your_group`, `/path/to/backend/directory`, and `/path/to/backend/binary` with your specific details.

  

3. **Reload Systemd and Start the Service:**

  

   After creating the service file, reload systemd to recognize the new service:

  

   ```bash

   sudo systemctl daemon-reload

   ```

  

   Then, start the backend service:

  

   ```bash

   sudo systemctl start backend

   ```

  

   Check the status to ensure it's running without errors:

  

   ```bash

   sudo systemctl status backend

   ```

  

   Enable the service to start on boot:

  

   ```bash

   sudo systemctl enable backend

   ```

  

### Step 3: Test Connecting to Your Backend

  

1. **Verify Backend Service Status:**

  

   Check the status of your backend service to ensure it's active and running:

  

   ```bash

   sudo systemctl status backend

   ```

  

2. **Test Backend Routes:**

  

   Use `curl` or a similar tool to test the backend routes. Assuming your backend is running on `127.0.0.1:8080`, try accessing the `/hey` and `/echo` routes:

  

   ```bash

   curl http://127.0.0.1:8080/hey
   curl http://127.0.0.1:8080/echo -d "Hello, Backend!"

   ```


   Check and make sure you receive expected responses from these requests.

  
