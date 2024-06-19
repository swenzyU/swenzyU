import socket
import uuid
import platform
import subprocess
import requests

def get_ip(ip_address=None):
    if ip_address:
        return ip_address
    else:
        try:
            return socket.gethostbyname(socket.gethostname())
        except socket.gaierror:
            return "IPv6 address not supported"

def get_ss_number():
    return uuid.getnode()

def get_upnp_enabled():
    try:
        output = subprocess.check_output(["upnpc", "-l"])
        if b"UPnP is enabled" in output:
            return "Yes"
        else:
            return "No"
    except:
        return "Unknown"

def get_ipv6():
    try:
        output = subprocess.check_output(["ip", "-6", "addr", "show"])
        if output:
            return "Yes"
        else:
            return "No"
    except:
        return "Unknown"

def get_dmz():
    try:
        output = subprocess.check_output(["iptables", "-t", "nat", "-n", "-v", "L"])
        if b"DMZ" in output:
            return "Yes"
        else:
            return "No"
    except:
        return "Unknown"

def get_mac():
    return uuid.UUID(int=uuid.getnode()).hex[-12:]

def get_hostname():
    return platform.node()

def get_geolocation(ip_address):
    response = requests.get(f"http://ip-api.com/json/{ip_address}")
    data = response.json()
    return {
        "country": data["country"],
        "region": data["regionName"],
        "city": data["city"],
        "ISP": data["isp"],
        "ZIP code": data["zip"],
        "latitude": data["lat"],
        "longitude": data["lon"],
        "timezone": data["timezone"]
    }

ip_address = input("IP addressi yaz babusss: ")
geolocation_data = get_geolocation(ip_address)

print("W:", "Windows" if "windows" in platform.system().lower() else "Not Windows")
print("N:", get_ip(ip_address))
print("SS Number:", get_ss_number())
print("UPNP Enabled:", get_upnp_enabled())
print("IPv6:", get_ipv6())
print("DMZ:", get_dmz())
print("MAC:", get_mac())
print("HOSTNAME:", get_hostname())
print("Country:", geolocation_data["country"])
print("Region:", geolocation_data["region"])
print("City:", geolocation_data["city"])
print("ISP:", geolocation_data["ISP"])
print("ZIP code:", geolocation_data["ZIP code"])
print("Latitude:", geolocation_data["latitude"])
print("Longitude:", geolocation_data["longitude"])
print("Timezone:", geolocation_data["timezone"])
