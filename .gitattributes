import threading
from http.server import BaseHTTPRequestHandler, HTTPServer
from twilio.rest import Client
from pyngrok import ngrok
import uuid
import datetime

# Dictionary to store tracking data: phone_number -> {'identifier': ..., 'ip': ..., 'timestamp': ...}
tracked = {}

# HTTP request handler to log IP addresses
class TrackingHandler(BaseHTTPRequestHandler):
    def do_GET(self):
        if self.path.startswith('/track/'):
            identifier = self.path.split('/')[-1]
            ip = self.client_address[0]
            # Associate IP with phone number based on identifier
            for phone, data in tracked.items():
                if data['identifier'] == identifier:
                    data['ip'] = ip
                    data['timestamp'] = datetime.datetime.now()
                    break
            self.send_response(200)
            self.end_headers()
            self.wfile.write(b"Tracking successful")
        else:
            self.send_response(404)
            self.end_headers()

def main():
    # Prompt for Twilio credentials
    print("Provide your Twilio credentials (get them from twilio.com):")
    account_sid = input("Enter Twilio Account SID: ")
    auth_token = input("Enter Twilio Auth Token: ")
    twilio_number = input("Enter Twilio Phone Number (e.g., +1234567890): ")
    message_template = input("Enter SMS message template (use {url} for the link, e.g., 'Click here: {url}'): ")

    # Start ngrok tunnel to expose local server
    public_url = ngrok.connect(8000).public_url
    print(f"Ngrok tunnel started at {public_url}")

    # Start HTTP server in a background thread
    server = HTTPServer(('localhost', 8000), TrackingHandler)
    server_thread = threading.Thread(target=server.serve_forever)
    server_thread.daemon = True
    server_thread.start()
    print("HTTP server started on localhost:8000")

    # Initialize Twilio client
    client = Client(account_sid, auth_token)

    # Command-line interface loop
    while True:
        cmd = input("\nEnter command (track <phone>, status <phone>, list, exit): ")
        parts = cmd.split()
        if len(parts) >= 1:
            command = parts[0].lower()

            if command == 'track' and len(parts) == 2:
                phone_number = parts[1]
                # Generate unique identifier
                identifier = str(uuid.uuid4())
                tracked[phone_number] = {'identifier': identifier, 'ip': None, 'timestamp': None}
                unique_url = f"{public_url}/track/{identifier}"
                body = message_template.replace('{url}', unique_url)
                try:
                    client.messages.create(body=body, from_=twilio_number, to=phone_number)
                    print(f"SMS sent to {phone_number} with link: {unique_url}")
                except Exception as e:
                    print(f"Error sending SMS: {e}")

            elif command == 'status' and len(parts) == 2:
                phone_number = parts[1]
                if phone_number in tracked:
                    data = tracked[phone_number]
                    if data['ip']:
                        print(f"IP for {phone_number}: {data['ip']} captured at {data['timestamp']}")
                    else:
                        print(f"{phone_number}: IP not captured yet")
                else:
                    print(f"{phone_number} is not being tracked")

            elif command == 'list':
                if tracked:
                    for phone, data in tracked.items():
                        status = f"IP: {data['ip']} at {data['timestamp']}" if data['ip'] else "Waiting for click"
                        print(f"{phone}: {status}")
                else:
                    print("No phone numbers are being tracked")

            elif command == 'exit':
                print("Shutting down...")
                break

            else:
                print("Invalid command. Use: track <phone>, status <phone>, list, exit")
        else:
            print("Invalid command. Use: track <phone>, status <phone>, list, exit")

    # Cleanup
    ngrok.disconnect(public_url)
    server.shutdown()
    server.server_close()
    print("Server and tunnel stopped")

if __name__ == "__main__":
    main()
