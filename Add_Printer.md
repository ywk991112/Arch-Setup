# Setting Up a Printer with CUPS on Arch Linux

## 1. Install CUPS and Required Dependencies:

Ensure that you have CUPS and the necessary tools installed.

```bash
sudo pacman -S cups cups-pdf ghostscript gsfonts
```

## 2. Start and Enable CUPS Service:

First, enable CUPS to start upon boot, and then start the service immediately.

```bash
sudo systemctl enable cups.service
sudo systemctl start cups.service
```

## 3. Access CUPS Web Interface:

You can manage printers and jobs using the CUPS web interface. Open it by navigating to:

[http://localhost:631/](http://localhost:631/)

## 4. Add Printer via CUPS Web Interface:

- Navigate to the **Administration** tab.
- Click on **Add Printer**.
- If prompted for authentication, the default username is `root` and the password is your root password.

## 5. Select Protocol:

For printers using RAW protocol on port 9100:

- Under "Other Network Printers", choose the "AppSocket/HP JetDirect" option.

## 6. Enter Connection Details:

Given the IP address and port you've provided, enter the following as the connection:

```plaintext
socket://140.112.19.236:9100
```

## 7. Choose Driver and Complete Setup:

- After entering the connection details, click “Continue” or "Next".
- Provide details for the printer:
  - **Name**: A short name for the printer (no spaces).
  - **Description**: A more descriptive name, which can include spaces.
  - **Location**: Where the printer is physically located.
- Choose a driver that matches your printer model from the list. If there isn't an exact match, you can opt for a generic driver. However, specific drivers are recommended.

## 8. Test the Printer:

After setting up the printer, send a test page to ensure everything works as expected.

---

*Note*: If you encounter issues, consult the error log at `/var/log/cups/error_log` or refer to the printer's documentation.
