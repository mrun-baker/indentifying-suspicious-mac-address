# indentifying-suspicious-mac-address
a SIEM SPLUNK Query to compare DHCP mac address with known recognize vendor mac-address 
Here's a GitHub README for your Splunk search query:

---

# DHCP MAC Address Lookup

This repository contains a Splunk search query to identify and categorize DHCP requests based on MAC address prefixes. The query uses a CSV file (`mac-vendors-export.csv`) to look up MAC address prefixes and determine their vendors.

File is from https://maclookup.app/

## Query Description

The query filters DHCP requests within a specific IP range and performs a lookup to identify the vendor of each MAC address. It flags unknown MAC addresses and excludes certain hostnames.

### Splunk Search Query


index=dhcp \\you can add your own specific range or criteria here
| rex field=dest_mac "(?P<prefix>[^\:]+\:[^\:]+\:[^\:]+)"
| eval prefix=lower(prefix)
| lookup mac-vendors-export.csv "Mac Prefix" as prefix OUTPUT "Mac Prefix" as found_prefix
| where isnull(found_prefix) AND NOT match(nt_host, "^(AP|PC|PF|poly).*")
| eval error=if(isnull(found_prefix), "Not Known Mac Address: Prefix not found", "Lookup successful")
| dedup dest_mac nt_host dest_ip prefix
| table _time dest_mac nt_host dest_ip prefix found_prefix error


### Explanation


1. **Extract MAC Prefix**: The `rex` command extracts the first three segments of the MAC address (prefix).

2. **Convert to Lowercase**: The `eval` command converts the MAC prefix to lowercase.

3. **Lookup Vendor**: The `lookup` command matches the MAC prefix against the `mac-vendors-export.csv` file to find the corresponding vendor.

4. **Filter Unknown Vendors**: The `where` command filters out known vendors and excludes hostnames starting with `AP`, `PC`, `PF`, or `poly`.

5. **Error Evaluation**: The `eval` command adds an error message for unknown MAC addresses.

6. **Remove Duplicates**: The `dedup` command removes duplicate entries based on MAC address, hostname, IP address, and prefix.

7. **Display Results**: The `table` command formats the output to display the time, MAC address, hostname, IP address, prefix, found prefix, and error message.

## CSV File

The `mac-vendors-export.csv` file contains MAC address prefixes and their corresponding vendors. This file is used for the lookup operation in the query.

## Usage

1. **Upload CSV File**: Ensure the `mac-vendors-export.csv` file is uploaded to your Splunk instance.

2. **Run Query**: Copy and paste the provided Splunk search query into your Splunk search bar and run it.

3. **View Results**: The results will display the DHCP requests, their MAC addresses, and vendor information.

## Resources

- MAC Address Lookup

## License

This project is licensed under the MIT License.



Feel free to customize this README further to suit your needs!
