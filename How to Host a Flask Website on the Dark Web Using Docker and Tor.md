
# 🧅 Hosting a Website on the Tor Network Using Docker and Flask

## 📋 Prerequisites

- Docker installed on your system
- Basic understanding of Python and Docker
- Tor Browser installed for testing

---
## 📁 Folder Structure

Suppose your folder is named `my-onion-site/`:

```
my-onion-site/
├── app/
│   └── app.py
├── onion_keys/
│   ├── hostname
│   ├── hs_ed25519_public_key
│   └── hs_ed25519_secret_key
├── tor/
│   └── torrc
├── Dockerfile
└── requirements.txt
```

---
## 🛠️ Step 1: Set Up Your Flask Application

1. **Create a project directory:**

   ```bash
   mkdir tor_flask_app
   cd tor_flask_app
   ```

2. **Create a simple Flask app:**

   - **app.py**

     ```python
		from flask import Flask
		
		app = Flask(__name__)
		
		  
		
		@app.route('/')
		
		def index():
		
		    return "hello this is simple tor site"
		
		  
		
		if __name__ == '__main__':
		
		    app.run(host='127.0.0.1', port=5000)
     ```

   - **requirements.txt**

     ```
     Flask==2.0.1
     ```

---

## 🐳 Step 2: Create the Dockerfile

1. **Create a `Dockerfile` in your project directory:**

```dockerfile
FROM debian:bullseye

  

# Install dependencies

RUN apt-get update && \

    apt-get install -y python3 python3-pip tor && \

    apt-get clean

# Create work directory

WORKDIR /app

# Copy Flask app and requirements

COPY app/ /app

COPY requirements.txt /app

# Install Python packages

RUN pip3 install --no-cache-dir -r requirements.txt


# Copy tor config and hidden service keys

COPY tor/torrc /etc/tor/torrc

COPY onion_keys/ /var/lib/tor/hidden_service/


# Set correct permissions for Tor hidden service keys

RUN chown -R debian-tor:debian-tor /var/lib/tor/hidden_service && \

    chmod 700 /var/lib/tor/hidden_service && \

    chmod 600 /var/lib/tor/hidden_service/hs_ed25519_secret_key && \

    chmod 600 /var/lib/tor/hidden_service/hs_ed25519_public_key

# Expose Flask port (optional, for testing/debugging)

EXPOSE 5000

USER debian-tor

# Start both Flask and Tor in background, keep container running

CMD sh -c "python3 app.py & tor -f /etc/tor/torrc & tail -f /dev/null"
   ```

2. **Create the `torrc` configuration file:**

   - **torrc**

     ```
     SOCKSPort 9050
     ControlPort 9051
	 HiddenServiceDir /var/lib/tor/hidden_service/
	 HiddenServicePort 80 127.0.0.1:5000```

---

## 🔐 Step 3: Build and Run the Docker Container

1. **Build the Docker image:**

   ```bash
   docker build -t tor_flask_image .
   ```

2. **Run the Docker container:**

   ```bash
   docker run -d -p 5000:5000 --name onion_service tor_flask_image
   ```

---

## 🌐 Step 4: Access Your .onion Address

1. **Retrieve the .onion address:**

   ```bash
   docker exec -it tor_flask_container cat /var/lib/tor/hidden_service/hostname
   ```

   This will output your `.onion` address, e.g., `abc123def456ghi7.onion`.
   if you don't know how to generate .onion address read a **host a site on darknet  article**

2. **Open Tor Browser and navigate to your `.onion` address.**

---

## 🧪 Step 5: Test the Flask Application

1. **Ensure the Flask app is running inside the container:**

   ```bash
   docker exec -it tor_flask_container curl http://127.0.0.1:5000
   ```

   You should see the message: `Hello, this is a simple Tor site!`

---

## 📝 Notes

- The `User Debian-tor` directive in the `torrc` file allows Tor to run as the Debian-tor user inside the Docker container.
- The `tail -f /dev/null` command keeps the Docker container running.
- Ensure that the `hidden_service` directory is owned by the correct user and has appropriate permissions.

---

This guide provides a basic setup for hosting a Flask application on the Tor network using Docker.
For more advanced configurations and security considerations, refer to the official Tor Project documentation:
[community.torproject.org](https://community.torproject.org/onion-services/setup/?utm_source=chatgpt.com)
