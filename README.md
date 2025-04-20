# First-SIEM-Setup
# My First Wazuh Setup: Server Install & Agent Deployment! ✨

Hey everyone! So, I'm just starting my journey into the Security Operations Center (SOC) world, and one of the coolest tools I've learned about is **Wazuh**. It's an open-source Security Information and Event Management (SIEM) and Extended Detection and Response (XDR) platform. Basically, it helps us keep an eye on our systems for sneaky security stuff!

This is my attempt to document setting it up – keeping it simple!

---

## 1. Getting the Wazuh Server Ready 🏗️

The Wazuh server is the "brain" where all the security data from agents goes. We'll use the handy **All-in-One installation** method, which bundles the Wazuh server, indexer (like a database for events), and dashboard (the pretty web interface) together.

**Prerequisites:**

* A fresh Linux machine (Ubuntu/Debian or CentOS/RHEL work great!). Let's assume Ubuntu for this guide.
* Enough resources (Check the official Wazuh docs for specifics, but maybe 4 CPU cores, 8GB RAM, 50GB disk space to start safe?).
* Internet access on the server.
* `curl`, `bash`, and `sudo`/root access.

**Installation Steps:**

1.  **Download the Assistant:** Grab the Wazuh installation assistant script.
    ```bash
    curl -sO [https://packages.wazuh.com/4.x/wazuh-install.sh](https://packages.wazuh.com/4.x/wazuh-install.sh)
    ```
2.  **Run the Assistant:** Execute the script. It will guide you through the process. We're doing the all-in-one here.
    ```bash
    sudo bash wazuh-install.sh --all-in-one
    ```
    *(This might take a little while, grab a coffee! ☕)*

3.  **IMPORTANT - Save Credentials!** At the end, the script will spit out important stuff, especially the `admin` password for the Wazuh dashboard. **SAVE THIS PASSWORD SECURELY!**

    ```
    INFO: --- Summary ---
    INFO: You can access the web interface https://<YOUR_SERVER_IP>
    INFO: User: admin
    INFO: Password: <VERY_SECRET_PASSWORD_HERE>  <-- Copy this!
    INFO: Installation finished.
    ```

    * **[Placeholder: Screenshot of successful installation output showing credentials]**
        `![Successful Installation Output Placeholder]`

---

## 2. Accessing the Wazuh Dashboard (The Fun Part!) 🖥️

Now let's see if it worked!

1.  Open your web browser.
2.  Go to `https://<YOUR_SERVER_IP>` (replace `<YOUR_SERVER_IP>` with the actual IP address of the machine where you installed Wazuh).
3.  You might get a browser warning about security certificates because it's self-signed. It's okay to proceed for now (usually under "Advanced").
4.  Log in using the `admin` username and the password you saved earlier.

* **[Placeholder: Screenshot of the Wazuh Dashboard login screen]**
    `![Wazuh Dashboard Login Screen Placeholder]`

* **[Placeholder: Screenshot of the main Wazuh Dashboard after login]**
    `![Wazuh Dashboard Main View Placeholder]`

Woohoo! If you see the dashboard, the server is up! 🎉

---

## 3. Deploying Wazuh Agents (Our Eyes and Ears!) 👀

Agents are small programs installed on the computers you want to monitor (endpoints). They collect logs and security data and send it to our Wazuh server.

**Key Info Needed:** The IP address or DNS name of your Wazuh Server.

### On Linux Endpoints (e.g., Ubuntu, CentOS) 🐧

1.  Log in to the Linux endpoint you want to monitor.
2.  Run the installation command, making sure to set the `WAZUH_MANAGER` variable to your server's IP address:
    ```bash
    export WAZUH_MANAGER="<YOUR_SERVER_IP>"
    curl -sO [https://packages.wazuh.com/4.x/agent/wazuh-agent-package.deb](https://packages.wazuh.com/4.x/agent/wazuh-agent-package.deb) && sudo WAZUH_MANAGER=$WAZUH_MANAGER apt install ./wazuh-agent-package.deb -y
    # For CentOS/RHEL use .rpm and yum/dnf instead of .deb and apt
    # curl -sO [https://packages.wazuh.com/4.x/agent/wazuh-agent-package.rpm](https://packages.wazuh.com/4.x/agent/wazuh-agent-package.rpm) && sudo WAZUH_MANAGER=$WAZUH_MANAGER yum install ./wazuh-agent-package.rpm -y
    ```
3.  Enable and start the agent service:
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable wazuh-agent
    sudo systemctl start wazuh-agent
    ```
4.  Check status (optional but good!):
    ```bash
    sudo systemctl status wazuh-agent
    ```

* **[Placeholder: Screenshot of successful agent installation on Linux]**
    `![Linux Agent Installation Placeholder]`

### On Windows Endpoints 🪟

1.  **Download the Agent:** Go to the official Wazuh documentation or repository and download the Windows Agent `.msi` installer.
2.  **Install:** You can double-click the `.msi` and follow the GUI prompts (make sure to enter your Wazuh Server IP when asked!), OR run it via command line (useful for automation later!).
    * Open Command Prompt (cmd) or PowerShell **as Administrator**.
    * Navigate to where you downloaded the `.msi` file.
    * Run the installer command, specifying your server IP:
        ```powershell
        .\wazuh-agent-package.msi /q WAZUH_MANAGER="<YOUR_SERVER_IP>"
        ```
        *(The `/q` makes it a quiet/silent install)*
3.  **Start the Service:** The service usually starts automatically after installation. You can check in `services.msc` (look for "Wazuh Agent") or via PowerShell:
    ```powershell
    Get-Service -Name wazuh
    Start-Service -Name wazuh # If it's not running
    ```

* **[Placeholder: Screenshot of successful agent installation on Windows (GUI or cmd output)]**
    `![Windows Agent Installation Placeholder]`

---

## 4. Checking Agent Connection ✅

Back in the Wazuh Dashboard (`https://<YOUR_SERVER_IP>`):

1.  Navigate to the **Agents** section (usually found in the main menu).
2.  You should see your newly added agents listed here! It might take a minute or two for them to show up and change from "Never connected" or "Pending" to "Active".

* **[Placeholder: Screenshot showing agents listed in the Wazuh Dashboard]**
    `![Agents List in Wazuh Dashboard Placeholder]`

If they show up as "Active", you've done it! Your Wazuh server is receiving data from your agents. Pretty cool, right?

---

That's the basic setup! From here, we can start exploring the alerts, learning about rules, and seeing what kind of security events Wazuh picks up. Awesome job getting this far! 💪
