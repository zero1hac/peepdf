# Execution #
<br />
```
Usage: ./peepdf.py [options] PDF_file

Options:
  -h, --help            show this help message and exit
  -i, --interactive     Sets console mode.
  -s SCRIPTFILE, --load-script=SCRIPTFILE
                        Loads the commands stored in the specified file and
                        execute them.
  -c, --check-vt        Checks the hash of the PDF file on VirusTotal.
  -f, --force-mode      Sets force parsing mode to ignore errors.
  -l, --loose-mode      Sets loose parsing mode to catch malformed objects.
  -m, --manual-analysis
                        Avoids automatic Javascript analysis. Useful with
                        eternal loops like heap spraying.
  -u, --update          Updates peepdf with the latest files from the
                        repository.
  -g, --grinch-mode     Avoids colorized output in the interactive console.
  -v, --version         Shows program's version number.
  -x, --xml             Shows the document information in XML format.

```
<br />
There are four possible ways of execution:

  * [Basic execution](#Basic_execution.md)
  * [Interactive console](#Interactive_console.md)
  * [Batch mode](#Batch_mode.md)
  * [XML Output](#XML_Output.md)
<br />
All of them accept two parameters to ignore errors and continue with the analysis and deal with malformed objects:

  * _-f_: ignores errors and continues with the analysis of the document. It's useful to analyse malicious documents.
  * _-l_: does not search for the _endobj_ tag during the parsing process, so it can be useful when the analysed document is malformed.
<br />
Also, there are three more parameters:

  * _-g_: avoids colorized output.
  * _-c_: checks the hash of the PDF file on VirusTotal.
  * _-m_: avoids performing any automatic Javascript analysis. This can be helpful when the analysis does not end due to a endless loop in the Javascript code.
<br />
Before executing peepdf it is recommended to try to update it, just in case there are new changes in the project. See the next section to know how to do it.
<br />

### Updating peepdf ###
In order to update the tool you can just use the _-u_ parameter:
```
$ ./peepdf.py -u

[-] Checking if there are new updates...
[+] There are new updates!!
[-] Getting paths from the repository...
[+] Done
[-] Getting local files information...
[+] Done
[-] Checking files...
[+] File "JSAnalysis.py" updated successfully
[+] File "PDFConsole.py" updated successfully
[+] File "PDFCore.py" updated successfully
[+] File "PDFCrypto.py" updated successfully
[+] File "PDFFilters.py" updated successfully
[+] File "PDFUtils.py" updated successfully
[+] File "README" updated successfully
[+] File "peepdf.dtd" updated successfully
[+] File "peepdf.py" updated successfully
[+] peepdf updated successfully to v0.2 r191
```
<br />

### Basic execution ###
If we only want to know the information about objects, streams, vulnerabilities, etc, without executing any commands the correct way of execution would be the following:

```
$ ./peepdf.py sample.pdf

File: sample.pdf
MD5: f77cca021c686f1ebe5c98cbfaa8c53a
Size: 1312 bytes
Version: 1.1
Binary: False
Linearized: False
Encrypted: False
Updates: 0
Objects: 8
Streams: 2
Comments: 0
Errors: 0

Version 0:
	Catalog: 2
	Info: 8
	Objects (8): [1, 2, 3, 4, 5, 6, 7, 8]
	Streams (2): [1, 5]
		Encoded (0): []
	Suspicious elements:
		/OpenAction: [2]
		/JS: [6]
		/JavaScript: [6]
```
<br />
### Interactive console ###
The normal way of execution of the interactive console is giving a PDF file to analyse:

```
$ ./peepdf.py -i sample.pdf

File: sample.pdf
MD5: f77cca021c686f1ebe5c98cbfaa8c53a
Size: 1312 bytes
Version: 1.1
Binary: False
Linearized: False
Encrypted: False
Updates: 0
Objects: 8
Streams: 2
Comments: 0
Errors: 0

Version 0:
	Catalog: 2
	Info: 8
	Objects (8): [1, 2, 3, 4, 5, 6, 7, 8]
	Streams (2): [1, 5]
		Encoded (0): []
	Suspicious elements:
		/OpenAction: [2]
		/JS: [6]
		/JavaScript: [6]




PPDF> 

```
<br />
When we see the _PPDF>_ prompt we can launch the different commands (commands documentation [here](Commands.md)):
```
PPDF> help

Documented commands (type help <topic>):
========================================
bytes           exit         js_join           quit          set       
changelog       filters      js_unescape       rawobject     show      
create          hash         js_vars           rawstream     stream    
decode          help         log               references    tree      
decrypt         info         malformed_output  replace       vtcheck   
embed           js_analyse   metadata          reset         xor       
encode          js_beautify  modify            save          xor_search
encode_strings  js_code      object            save_version
encrypt         js_eval      offsets           sctest      
errors          js_jjdecode  open              search        
```
<br />
Also, it's possible to launch the interactive console wihout specifying any PDF file:
```
$ peepdf.py -i                        


PPDF> 
```
<br />
But now we cannot launch all the commands until a PDF file has been opened, but we can execute Javascript and shellcodes, encode/decode contents, etc. The available commands will be the following:
```
create              decode                  encode            exit
help                js_analyse              js_beautify       js_eval
js_jjdecode         js_join                 js_unescape       js_vars
log                 malformed_output        open              quit
replace             reset                   sctest            set
show                vtcheck                 xor               xor_search
```
<br />
### Batch mode ###
Instead of executing peepdf as an interactive console it is also possible to execute it in batch mode in order to perform automatic analysis. So after creating a file with the commands we want to execute:

```
$ cat command_file.txt
tree
offsets
```

we should execute the tool this way:

```
$ ./peepdf.py -s command_file.txt sample.pdf

/Catalog (2)
	/Pages (3)
		/Page (4)
			/Pages (3)
			stream (5)
			/Annot (7)
				stream (1)
	/Action /JavaScript (6)
/Info (8)
	stream (1)


       0 Header
      10
        Object  1 (58)
      67
      70
        Object  2 (67)
     136
     139
        Object  3 (57)
     195
     198
        Object  4 (264)
     461
     464
        Object  5 (139)
     602
     605
        Object  6 (180)
     784
     787
        Object  7 (227)
    1013
    1016
        Object  8 (35)
    1050
    1053
        Xref Section (188)
    1240
    1242
        Trailer (64)
    1305
    1306 EOF
```
<br />
### XML Output ###
Additionaly, it's possible to obtain the same information provided in the [basic execution](#Basic_execution.md) in XML format thanks to the -x option (combined with -fl if needed). The associated DTD can be found [here (peepdf.dtd)](http://peepdf.googlecode.com/svn/trunk/peepdf.dtd).

```
$ ./peepdf.py -flx 517fe6ba9417e6c8b4d0a0b3b9c4c9a9
<peepdf_analysis url="http://peepdf.eternal-todo.com" version="0.1 r92" author="Jose Miguel Esparza">
  <date>2012-03-24 17:09</date>
  <basic>
    <filename>517fe6ba9417e6c8b4d0a0b3b9c4c9a9</filename>
    <md5>517fe6ba9417e6c8b4d0a0b3b9c4c9a9</md5>
    <sha1>abf382f076f8a021dbf82aca425b152d172fa5ca</sha1>
    <sha256>fa32485935ab75d09cae7d52f5251fb6620813205fb45a1b05d91e23b56068a1</sha256>
    <size>80577</size>
    <pdf_version>1.7</pdf_version>
    <binary status="false"/>
    <linearized status="false"/>
    <encrypted status="false"/>
    <updates>0</updates>
    <num_objects>16</num_objects>
    <num_streams>5</num_streams>
    <comments>0</comments>
    <errors num="1">
      <error_message>%%EOF not found</error_message>
    </errors>
  </basic>
  <advanced>
    <version num="0" type="original">
      <catalog/>
      <info/>
      <objects num="16">
        <object id="1" errors="false"/>
        <object id="2" errors="false"/>
        <object id="3" errors="false"/>
        <object id="4" errors="false"/>
        <object id="5" errors="false"/>
        <object id="6" errors="false"/>
        <object id="7" errors="false"/>
        <object id="8" errors="false"/>
        <object id="9" errors="false"/>
        <object id="10" errors="false"/>
        <object id="11" errors="false"/>
        <object id="12" errors="false"/>
        <object id="13" errors="false"/>
        <object id="14" errors="false"/>
        <object id="15" errors="true"/>
        <object id="16" errors="true"/>
      </objects>
      <streams num="5">
        <stream id="2" encoded="true" decoding_errors="false"/>
        <stream id="7" encoded="true" decoding_errors="false"/>
        <stream id="10" encoded="true" decoding_errors="false"/>
        <stream id="16" encoded="true" decoding_errors="true"/>
        <stream id="15" encoded="true" decoding_errors="false"/>
      </streams>
      <js_objects>
        <container_object id="15"/>
      </js_objects>
      <suspicious_elements>
        <triggers>
          <trigger name="/AcroForm">
            <container_object id="4"/>
          </trigger>
          <trigger name="/OpenAction">
            <container_object id="4"/>
          </trigger>
        </triggers>
        <actions>
          <action name="/JS">
            <container_object id="14"/>
          </action>
          <action name="/JavaScript">
            <container_object id="14"/>
          </action>
        </actions>
        <elements>
          <element name="/U3D">
            <cve>CVE-2009-3953</cve>
            <cve>CVE-2009-3959</cve>
            <cve>CVE-2011-2462</cve>
            <container_object id="10"/>
          </element>
        </elements>
      </suspicious_elements>
      <suspicious_urls/>
    </version>
  </advanced>
</peepdf_analysis>
```