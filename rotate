import sh
import subprocess
import signal
import time
import requests
import os
import stem.control
import sys
from optparse import OptionParser
sys.stdout.flush()

parser = OptionParser()

parser.add_option("-c",dest="command",help="Enter the command to execute")
parser.add_option("-f",dest="filter",help="Filter to rotate the ip address")
parser.add_option("-w",dest="write",help="Write output to a file")
val, args = parser.parse_args()


command = val.command
filter = val.filter


#print(command, filter)
#quit()

#import requests

print("[+] Started!")

def save_output(filename,data):
        with open(filename,'a') as file:
                file.write(data + '\n')
        return True
def rotate_ip():
        print("Rotating IP Address")
        proxies = {
        "http": "socks5h://127.0.0.1:9050",
        "https": "socks5h://127.0.0.1:9050"
        }
        TOR_CONTROL_PORT = 9051
        TOR_PASSWORD = "your_password"  # Replace with the actual password
        try:
                with stem.control.Controller.from_port(port=TOR_CONTROL_PORT) as controller:
                        controller.authenticate(password=TOR_PASSWORD)
                        controller.signal(stem.Signal.NEWNYM)
                        time.sleep(5)  # Wait for Tor to switch IP
        except Exception as e:
                print(f"Error changing IP: {e}")
                quit()
        response = requests.get("http://checkip.amazonaws.com",proxies=proxies).text
        return response

output = sh.bash("-c",command,_iter=True)

for line in output:
        #subprocess.call("sudo service tor restart",shell=True)
        #time.sleep(4)
        if filter in line.strip():
                os.kill(output.pid,signal.SIGSTOP)
                print("[+] WAF Blocked..Bypassing it for you ;)")
                #subprocess.call("sudo service tor restart",shell=True)
                new_ip = rotate_ip()
                print(f"[+] New IP Address: {new_ip}")
                #time.sleep(2)
                os.kill(output.pid,signal.SIGCONT)
        else:
                print(line.strip())
                save_output(val.write,line.strip())
print(1)
