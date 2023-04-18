#!/bin/python
# Written by ERIC JAEHWANG KIM
# Version 2; 4/17/2023

import argparse
import csv
import dns.resolver
import ipaddress
import sys
from ping3 import ping

# define a function which checks IP address is valid or not
def is_valid_ip_address(ip_address_str):
    try:
        ipaddress.ip_address(ip_address_str)
        return True
    except ValueError:
        return False

# define argparse arguments
_parser = argparse.ArgumentParser(description='Resolve DNS/IP Address(es) and Ping.')
_parser.add_argument('-i', dest='input_file', type=argparse.FileType('r', encoding='UTF-8'), required=True, help='Filename of DNS/IP address list')
_parser.add_argument('-d', dest='dns_server', type=str, required=False, help='DNS server IP address to utilize')
_args = _parser.parse_args()

# define a DNS resolver with a target server if applicable
_resolver = dns.resolver.Resolver()
if (_args.dns_server): _resolver.nameservers = [_args.dns_server]

# define output headers
_csvwriter = csv.writer(sys.stdout, delimiter=',', lineterminator='\n')
_csvwriter.writerow(["REQUEST","DNS_RESPONSE","PING_RESONSE"])

for _line in _args.input_file:
    _data = _line.strip()
    _responses = []
    _ping = "Unknown"
    _type = "Unknown" # _type is a response record type, A or CNAME

    try:
        if is_valid_ip_address(_data):
            _type = "IP"
            _answers = _resolver.resolve_address(_data)
            _ping  = ping(_data,timeout=2)
            _ping = 'True' if (_ping) else 'False'

            for _answer in _answers:
                _csvwriter.writerow([_data,_answer,_ping])
        else:
            _answers = _resolver.resolve(_data)
            _type = "Name"

            for _answer in _answers:
                _ping = ping(str(_answer),timeout=2)
                _ping = 'True' if (_ping) else 'False'
                _csvwriter.writerow([_data,_answer,_ping])

    except dns.exception.DNSException as _e:
        _ping  = ping(_data,timeout=2)
        _ping = 'True' if (_ping) else 'False'
        _csvwriter.writerow([_data,str(type(_e).__name__),_ping])

    except Exception as _e:
        _csvwriter.writerow([_data,_e,"Unknown"])
