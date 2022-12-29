# Python_Pinger2.py
#! /usr/bin/env python3

# Installation of Argparse,ipaddress and os modules

# argparse is what obtains the input from command line
import argparse
# ipaddress is used to validate if the IP is in correct format
import ipaddress
# os is used for operating system related tasks
import os

__author__ = "Diarmuid O'Briain"
__copyright__ = "Copyright 2021, Institute of Technology Carlow"
__licence__ = "European Union Public Licence v1.2"
__version__ = "1.0"

def licence() -> str:
    
    # licesne function return the license information( author's name, copyright and licence). 
    try:
        output = f"Author : {__author__}\n" + f"Copyright : {__copyright__}\n" + f"Licence : {__licence__}"
        return output
    except Exception as err:
        print(err)          


def ping(ip: str) -> str:
    
    # ping function where IP is provided to get back the status of the IP.
    
    # validate if the IP is of the correct format
    try:
        ipaddress.ip_address(ip)  
    # Return an IPv4Address or IPv6Address
    # ipaddress.ip_address(ip) raises a ValueError if if the address passed isn't either a IPv4Address or IPv6Address
    except ValueError:
        return f"{ip} - Error: is not a valid IP address"
    except Exception as err:
        return f"{ip} - Error: is not a valid IP address"

    # ping command with a timeout of 1 seconds on Linux/Mac
    # timeout is required to know if an IP is unreachable
    ping_command = f"ping -w 1 {ip}"

    # os.popen runs the command through command-line
    output = os.popen(ping_command)
    # using the read() method on output from os.popen gives us the string that would be printed on terminal
    output = output.read()
    print(output)

    if "100% loss" in output:
        return f"{ip} IS UNREACHABLE"
    else:
        return f"{ip} IS ALIVE"

# the following bit of code is what enables the switches for command-line using argparse
parser = argparse.ArgumentParser(prog = 'ProgramName',
description="program description")
# action="store_true" means the value is defaulted to false, once the switch is used, it turns the value to true
parser.add_argument("-l", "--licence", "--license", action="store_true", help="licence help")
parser.add_argument("-v", "--version", action="version", help="version help", version=__version__)
# nargs="+", means number of arguments can be anything
parser.add_argument("-i", "--ip", nargs="+", help="ip help")
args = parser.parse_args()

# the main function where all the logic happens
def main():
    # namespace.licence is true if the -l or --licence or --license switch are used while starting the program
    if args.licence:
        # return here ensures the program stops after printing out the licence information
        return print(licence())
    # similar idea to namespace.licence
    

    # if -i or --ip is used and some ip values are provided from the command line
    if args.ip:
        # namespace.ip is now a list of IPs, like this
        # ["'8.8.8.8'", "'192.168.1.1'", "'127.0.0.1'"]
        # loop through the list
        for ip in args.ip:
            # as the IP has single quotes around it, remove it from either side using the strip() method
            ip = ip.strip("'")
            # finally call the ping() function created above, passing in the current IP from the list
            status = ping(ip)
            # print the status on the terminal
            print(status)
    # if -i or --ip was not used, ask for an IP within the software
    else:
        # record the IP input into an IP variable
        ip = input("Enter an IP address: ")
        # call ping() function, passing in the IP
        status = ping(ip)
        # print the return of the ping() function
        print(status)

# only run main() if the script is run directly
# this prevents main() from running if the script is imported
if __name__ == "__main__":
    main()
