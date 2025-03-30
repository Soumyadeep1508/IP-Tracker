# IP-Tracker
 A IP Tracker for Kali Linux
Prerequisites
Before running the tool on Kali Linux, install the required Python libraries:

Twilio: For sending SMS.
pip install twilio
Pyngrok: For creating a public tunnel to the local server.
pip install pyngrok
You’ll also need:

A Twilio account (sign up at twilio.com) to get an Account SID, Auth Token, and Twilio phone number.
Kali Linux with Python 3 installed (typically pre-installed).
How to Use the Tool
Save the Script: Save the code as ip_tracker.py on your Kali Linux system.
Run the Tool: Execute it with Python 3:
python3 ip_tracker.py
Provide Credentials: Enter your Twilio Account SID, Auth Token, Twilio phone number, and a custom SMS message template (e.g., "Check this out: {url}").
Use Commands:
track <phone_number>: Start tracking a phone number (e.g., track +1234567890). Note: Phone numbers should be in E.164 format (e.g., + followed by country code and number).
status <phone_number>: Check if an IP has been captured (e.g., status +1234567890).
list: View all tracked phone numbers and their status.
exit: Stop the tool and clean up.
How It Works
Initialization: The tool starts an HTTP server on port 8000 and uses Ngrok to create a public URL (e.g., https://abc123.ngrok.io).
Tracking: When you run track <phone_number>, it generates a unique URL (e.g., https://abc123.ngrok.io/track/xyz789), sends it via SMS, and waits for a click.
IP Logging: When the link is clicked, the server logs the IP address and associates it with the phone number.
Status Check: Use status or list to see captured IPs and timestamps.
Notes
Ethics and Legality: This tool is designed for educational or penetration testing purposes with consent. Unauthorized use to track individuals may violate privacy laws.
Twilio Costs: Sending SMS requires a Twilio account with credits (trial accounts offer limited free usage).
Phone Number Format: Ensure phone numbers are in E.164 format for Twilio compatibility.
Security: The tool runs a basic server; use it cautiously to avoid unintended exposure.
This tool effectively meets the requirement of finding an IP address using a phone number by combining SMS delivery with IP logging, tailored for a Kali Linux environment.