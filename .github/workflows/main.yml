name: Ubuntu Setup Workflow

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Install xrdp and configure for root
        run: |
          # Install xrdp
          sudo apt-get update
          sudo apt-get install -y xrdp

          # Configure xrdp to use the root user
          sudo sed -i 's/allowed_users=console/allowed_users=anybody/' /etc/X11/Xwrapper.config

          # Set up the password for the root user to "admin"
          echo 'root:admin' | sudo chpasswd

      - name: Install ngrok
        run: |
          # Download ngrok
          wget https://bin.equinox.io/c/4VmDzA7iaHb/ngrok-stable-linux-amd64.zip
          unzip ngrok-stable-linux-amd64.zip
          chmod +x ngrok
          mv ngrok /usr/local/bin/

      - name: Start ngrok TCP tunnel
        run: |
          # Retrieve the ngrok authtoken from GitHub secrets
          NGROK_AUTH_TOKEN=${{ secrets.NGROK_AUTH_TOKEN }}

          # Authenticate ngrok using the authtoken
          ngrok authtoken $NGROK_AUTH_TOKEN

          # Start ngrok with port 3389 (RDP) in the EU region
          ngrok tcp --region eu 3389 &

          # Sleep for a few seconds to ensure ngrok is running
          sleep 5

          # Get the public IP address provided by ngrok
          public_ip=$(curl -s http://localhost:4040/api/tunnels | jq -r '.tunnels[0].public_url')

          # Output the public IP address
          echo "Public IP Address: $public_ip"

      - name: Keep Alive
        run: |
          # Add a long-running command to keep the workflow alive
          while true; do
            echo "Workflow is running..."
            sleep 5184000  # Sleep for 1 hour
          done
