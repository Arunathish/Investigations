
## Investigation

The investigation was based around multiple suspicious Microsoft Office documents.

The objective is identify the malicious IP addresses and domains are in the documents.

The files were:

- `Employees_Contact_Audit_Oct_2021.docx`
- `Employee_W2_Form.docx`
- `Work_From_Home_Survey.doc`
- `income_tax_and_benefit_return_2021.docx`
## 1. Employees_Contact_Audit_Oct_2021.docx

First, I checked the structure of the `.docx` file using `zipdump.py`
A `.docx` file is basically a ZIP archive, so I used `zipdump.py` to inspect the contents.

  python3 zipdump.py /root/Desktop/ChallengeFiles/Employees_Contact_Audit_Oct_2021.docx

![[Letsdefend/Document analysis/Screenshots/1.png]]

Instead of manually checking each stream, I used the `-D` option to dump all the contents and piped the output into `re-search.py`.

I was specifically looking for IPv4 addresses.

  python3 zipdump.py -D /root/Desktop/ChallengeFiles/Employees_Contact_Audit_Oct_2021.docx | python3 re-search.py -n -u ipv4

![[Letsdefend/Document analysis/Screenshots/2.png]]

This returned an IP address from inside the document. Then i checked the IP reputation in the virus total.

## 2. Employee_W2_Form.docx

This time I was looking for a malicious domain rather than an IP address.

I initially looked at the available URL/domain filters in `re-search.py`.

The standard URL filters did not give me the result I was looking for, so I changed the approach and used the `domaintld` filter.

  python3 zipdump.py -D /root/Desktop/ChallengeFiles/Employee_W2_Form.docx | python3 re-search.py -u -n domaintld

![[Letsdefend/Document analysis/Screenshots/3.png]]

## 3. Work_From_Home_Survey.doc

This sample was different because it was a `.doc` file instead of `.docx`

I started with the same approach and searched the dumped content for domains.

python3 zipdump.py -D /root/Desktop/ChallengeFiles/Work_From_Home_Survey.doc | python3 re-search.py -n -u domaintld

![[Letsdefend/Document analysis/Screenshots/4.png]]

but it wasn't enough to identify the actual malicious domain.

So I needed to look deeper into the individual streams.

I checked the document structure and focused on stream 10.

python3 zipdump.py /root/Desktop/ChallengeFiles/Work_From_Home_Survey.doc -s 10 -d

![[Letsdefend/Document analysis/Screenshots/5.png]]

The output contained a large amount of data.

While going through it, I found a Relationship ID and encoded data that appeared to contain an external reference.

Instead of manually decoding it, I used `numbers-to-string.py`

python3 zipdump.py /root/Desktop/ChallengeFiles/Work_From_Home_Survey.doc -s 10 -d | python3 numbers-to-string.py

![[Letsdefend/Document analysis/Screenshots/6.png]]

Another way to decode this is using cyberchef and use the "From HTML Entity"

![[Letsdefend/Document analysis/Screenshots/7.png]]

This exposed the malicious domain contained within the document.

## 4. income_tax_and_benefit_return_2021.docx

For this document, I was again looking for a malicious domain.

I went back to the same approach used earlier, but this time used the `url-domain` filter.

python3 zipdump.py -D /root/Desktop/ChallengeFiles/income_tax_and_benefit_return_2021.docx | python3 re-search.py -n -u url-domain

This returned a unique URL/domain from the document.

I then checked the indicator on VirusTotal. That gave me additional evidence that the domain was related to the malicious documents. 
## 5. Checking the file hashes

I had extracted multiple IOCs from the documents.

To get more information about the actual samples, I calculated the SHA-256 hash of the files.

```
sha256sum *
```

This returned the SHA-256 value for each file in the `ChallengeFiles` directory.

![[Letsdefend/Document analysis/Screenshots/8.png]]

## 6. Finding the vulnerability

After checking the samples on VirusTotal, I noticed that the malicious documents were associated with the same vulnerability.

CVE-2021-40444

![[Letsdefend/Document analysis/Screenshots/9.png]]

## Tools used

The tools that I have used to analyze the document was Didier Stevens tools
- `zipdump.py`
- `re-search.py`
- `numbers-to-string.py`

Then 
- `sha256sum`
- VirusTotal
