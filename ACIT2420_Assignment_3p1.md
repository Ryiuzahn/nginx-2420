### Update The System:
{ #Update }
```bash
sudo pacman -Syu
```

### Install Vim and and Nginx (If Not Already On System):
{ #Installation #Vim #Nginx }
```bash
sudo pacman -S vim nginx
```

### Create a new directory `/web/html/nginx-2420` to serve as the project root:
{ #CreateDirectory }
```bash
sudo mkdir -p /web/html/nginx-2420
```

### Navigate to the Nginx configuration directory:
{ #Navigation }
```bash
cd /etc/nginx/
```

### Create a `sites-available` and `sites-enabled` folders if they do not exist:
{ #CreateDirectory }
```bash
sudo mkdir sites-available
sudo mkdir sites-enabled
```

### Open main Nginx file and add the code block below:
{ #Config }
##### Open Nginx file:
```bash
sudo vim nginx.conf
```
##### Add the code below in the http {} block:
```
include /etc/nginx/sites-enabled/*;
```
###### It should look like this:
```
http {
	include      mime.types;
	include /etc/nginx/sites-enabled/*;
	...
}
```

### Create separate new server block configuration file named `nginx-2420.conf`:
{ #CreateFile #Config }
```bash
sudo vim sites-available/nginx-2420.conf
```

### Add the following code block to your new `nginx-2420.conf` file:
{ #Config }
```
server {
    listen 80;
    server_name insert_your_address;

    root /web/html/nginx-2420;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### Save and exit the file by following the instruction below:
{ #Vim }
```
Press "ESC".
Enter ":wq" in the Vim console.
Press "Enter".
```

### Enable the new server block by creating a symbolic link to `sites-enabled`:
{ #SymbolicLink }
```bash
sudo ln -s /etc/nginx/sites-available/nginx-2420.conf /etc/nginx/sites-enabled/
```

### Test the Nginx configuration for syntax errors:
{ #Testing }
```bash
sudo nginx -t
```

### Once the syntax check is complete and shows no errors, reload Nginx to apply the changes:
{ #Systemctl #Reload }
```bash
sudo systemctl reload nginx
```

### Navigate to the project directory we created earlier:
{ #Navigation }
```bash
cd /web/html/nginx-2420
```

### Create a HTML document named `index.html`:
{ #CreateFile #Html }
```bash
sudo vim index.html
```

### You can add the below code block to your `index.html`:
{ #Html }
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2420</title>
    <style>
        * {
            color: #db4b4b;
            background: #16161e;
        }
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            text-align: center;
            font-family: sans-serif;
        }
    </style>
</head>
<body>
    <h1>All your base are belong to us</h1>
</body>
</html>
```

### Save and exit the file by following the instruction below:
{ #Vim }
```
Press "ESC".
Enter ":wq" in the Vim console.
Press "Enter".
```

### Reload and start the `nginx` service:
{ #Reload #StartServer }
```bash
sudo systemctl reload nginx
sudo systemctl start nginx
```

### You can test out the web server by opening a browser and navigating to your server's IP address:
{ #Testing }


























