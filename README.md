# check_http_curl

# check_http_curl - Nagios HTTP/HTTPS Check via cURL

`check_http_curl` is a lightweight Bash plugin for Nagios that performs HTTP/HTTPS checks using `curl`, with the ability to **ignore SSL certificate validation** (`curl -k`).  
It serves as a reliable alternative to the native `check_http` plugin, especially on systems where SSL verification cannot be disabled.

---

## Notes
`curl` does not follow redirects by default. To follow redirects, add the `-L` option in the script if needed.

The plugin can be extended to support keyword checks, HTTP headers, authentication, proxy settings,

---

## Features

- Perform HTTP/HTTPS checks on any URL  
- ignore SSL validation (expired, self-signed, invalid certs)  
- Measure total response time  
- Nagios-compatible return codes  
- Simple CLI usage  

---

## Requirements

- **bash**
- **curl**
- **awk**  

---

## Installation

1. copy the script into your Nagios plugin directory:

```bash
cp src/check_http_curl /usr/local/nagios/libexec/
```

2. set execute permissions:
```bash
chmod +x /usr/local/nagios/libexec/check_http_curl
```

3. test the plugin:
```bash
/usr/local/nagios/libexec/check_http_curl -u https://example.com/
```

---

## Usage

```bash
check_http_curl -u <url> [-w warn_seconds] [-c crit_seconds]
```

with these parameters:

| parameter | description |
| -------- | ------- |
| `-u <url>`  | target URL to check (required)    |
| `-w <seconds>`  | warning threshold for response time (default: 1s)    |
| `-c <seconds>`  | critical threshold for response time (default: 3s)    |


The plugin now uses `curl -k`, which skips SSL certificate validation.
This behavior will probably be changed in the next revision.

---

## Examples


1. Basic HTTPS check

```bash
./check_http_curl -u https://mysite.name/
```

2. Custom thresholds

```bash
./check_http_curl -u https://example.com/ -w 2 -c 4
```


Example output:
```bash
OK: HTTP 200 - 0.545766s | time=0.545766s;;;0
```

## Nagios integration

1. command definition in `commands.cfg`
```
define command {
    command_name  check_http_curl
    command_line  /usr/local/nagios/libexec/check_http_curl -u $ARG1$ -w $ARG2$ -c $ARG3$
}
```

2. service definition in `services.cfg`
```
define service {
    use                     generic-service
    host_name               $myserver$
    service_description     HTTP check via curl
    check_command           check_http_curl!https://mysite.name/!2!4
}
```
