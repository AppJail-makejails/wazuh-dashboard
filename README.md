# Wazuh (dashboard)

The Wazuh dashboard is the web user interface for data visualization and analysis. It includes out-of-the-box dashboards for threat hunting, regulatory compliance (e.g., PCI DSS, GDPR, CIS, HIPAA, NIST 800-53), detected vulnerable applications, file integrity monitoring data, configuration assessment results, cloud infrastructure monitoring events, and others. It is also used to manage Wazuh configuration and to monitor its status.

wazuh.com

<img src="https://upload.wikimedia.org/wikipedia/commons/c/c3/Wazuh-Logo-2022.png?20230817165159" width="60%" height="auto" alt="wazuh logo">

## How to use this Makejail

### Deploy using appjail-director

**.env**:

```
DIRECTOR_PROJECT=wazuh-dashboard
```

**appjail-director.yml**:

```yaml
options:
  - virtualnet: ':<random> address:10.0.0.82 default'
  - nat:
  - osversion: '14.3-RELEASE'
  - copydir: 'user-files'
  - file: '/usr/local/etc/pkg/repos/Latest.conf'
services:
  wazuh-dashboard:
    makejail: gh+AppJail-makejails/wazuh-dashboard
    volumes:
      - dashboard-certs: /usr/local/etc/opensearch-dashboards/certs
      - dashboard-data: /usr/local/www/opensearch-dashboards/data
    arguments:
      - opensearch_addr: '10.0.0.81'
      - wazuh_api_url: 'https://10.0.0.80'
      - wazuh_api_password: 'MyS3cr37P450r.*-'
volumes:
  dashboard-certs:
    device: /var/appjail-volumes/wazuh-dashboard/certs
    owner: 0
    group: 0
    mode: 0755
  dashboard-data:
    device: /var/appjail-volumes/wazuh-dashboard/data
    owner: 80
    group: 80
    mode: 0755
```

**user-files/usr/local/etc/pkg/repos/Latest.conf**:

```
FreeBSD: {
  url: "pkg+https://pkg.FreeBSD.org/${ABI}/latest",
  mirror_type: "srv",
  signature_type: "fingerprints",
  fingerprints: "/usr/share/keys/pkg",
  enabled: yes
}
FreeBSD-kmods: {
  enabled: no
}
```

**Console**:

```console
# tree -pug /var/appjail-volumes/wazuh-dashboard/certs/
[drwxr-xr-x root     wheel   ]  /var/appjail-volumes/wazuh-dashboard/certs/
├── [-r-------- www      www     ]  dashboard-key.pem
├── [-r-------- www      www     ]  dashboard.pem
└── [-r-------- www      www     ]  root-ca.pem

1 directory, 3 files
# appjail-director up
Starting Director (project:wazuh-dashboard) ...
Creating wazuh-dashboard (6ecc44c6c6) ... Done.
Finished: wazuh-dashboard
```

### Arguments

* `opensearch_addr` (mandatory)
* `wazuh_api_url` (mandatory)
* `wazuh_api_password` (mandatory)
* `opensearch_port` (default: `9200`)
* `opensearch_username` (default: `admin`)
* `opensearch_password` (default: `admin`)
* `opensearch_conf` (default: `files/opensearch_dashboards.yml`)
* `wazuh_api_port` (default: `55000`)
* `wazuh_api_username` (default: `wazuh-wui`)
* `wazuh_api_conf` (default: `files/wazuh.yml`)
