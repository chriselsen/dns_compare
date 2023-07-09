# dns_compare.py

Compare data from a BIND zone file to data returned by an authoritative DNS server.

**If you enjoy this work, please consider sponsoring:**

[![Buy Me A Coffee](https://raw.githubusercontent.com/chriselsen/chriselsen/main/buymeacoffee.png)](https://www.buymeacoffee.com/chriselsen)
[![Support via PayPal](https://raw.githubusercontent.com/chriselsen/chriselsen/main/paypal-donate.png)](https://www.paypal.me/christianelsen)
[![Sponsor on Github](https://raw.githubusercontent.com/chriselsen/chriselsen/main/github-sponsor.png)](https://github.com/sponsors/chriselsen)

## Purpose

Use this tool to verify the data being returned by an authoritative DNS server matches
the data in a zone file.

## Motivation

It is very helpful when migrating from one DNS provider or server to another to be able to
verify that all records imported correctly.

In my case, I used this tool to help me migrate multiple domains from
Google Domains (which export BIND zone files) into Amazon Route 53 DNS service.  
With this tool, I could confidently prove that all records properly imported into Route 53
before changing the whois records for each domain.

UPDATED (6/20/2023): Added support for Python3

## Installation


    pip3 install dnspython
    pip3 install git+http://github.com/chriselsen/dns_compare.git#egg=dns_compare


## AWS CloudShell

This tool can be used with [AWS CloudShell](https://aws.amazon.com/cloudshell/). Just follow the above instructions under *Installation*.  
You can quickly and easily upload our zone file in BIND format to AWS CloudShell via **Action -> Upload file**. 

## Example Usage

Basic operation:

    $ dns_compare -z example.com --file example.com.zone --server 10.1.1.1
    ............................................X
    (MIS-MATCH) query: nss4.example.com.
     Expected:  300 IN A 142.229.40.28
     Received:  900 IN A 142.229.40.28
    ...X
    (MIS-MATCH) query: www.example.com.
     Expected:  200 IN A 91.139.129.128
     Received:  900 IN A 91.139.129.128
    X
    (MIS-MATCH) query: www.example.com.
     Expected:  300 IN AAAA 2001:4800:1078:2256:78C8:1542:FF04:6BCB
     Received:  900 IN AAAA 2001:4800:1078:2256:78c8:1542:ff04:6bcb
    ......................done
    
    Results:
    Matches:      69
    Mis-matches:  3

Verbose:

    $ dns_compare -z example.com --file example.com.zone --server 10.1.1.1 --verbose
	----
	(Match) query: www.example.com. ...
	Expected:  0 IN CNAME example.com.
	Received:  0 IN CNAME example.com.
	----
	(MIS-MATCH) query: example.com. ...
	Expected:  60 IN A 10.0.0.1
	Got     :  60 IN A 10.0.0.20

By default, SOA and NS records are ignored because these records are likely
to change when migrating a zone between DNS services..  Specify `--soa` or `--ns` option,
respectively, to enable checking of SOA and NS records.

Comparing TTLs can be disabled with `-t` option. This is useful when transferring DNS to a provider
that offers only specific TTL values.

## TODO

- Print separate count of NXDOMAIN in results?

## Authors

- [Joe Miller](http://github.com/joemiller)
- [Christian Elsen](https://github.com/chriselsen/)
