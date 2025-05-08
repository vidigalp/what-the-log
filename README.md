# WhatTheLog (WTL) 🪵 → <img src="https://www.apache.org/logos/originals/parquet.svg" alt="parquet" width="110">

**WhatTheLog (WTL)** is a blazingly fast ETL (Extract, Transform, Load) tool, written in Rust, designed to convert log files from various formats into the efficient Apache Parquet format. If you've ever found yourself wrestling with mountains of text-based logs and wished for a more structured, queryable, and analytics-friendly format, WTL is here to help!

WTL is heavily inspired by the fantastic [lnav](https://lnav.org) (Log File Navigator), particularly its powerful automatic file format detection and its ability to unpack compressed files on the fly. Like lnav, WTL aims to make log processing intuitive and efficient.

## 🤔 So, What *The Log* Does It Actually Do?

* **Automatic Format Detection:** Throws a log file at WTL, and it will intelligently try to figure out its format. No more manual configuration for common log types!
* **Multi-Format Support:** Handles a variety of log file formats. Don't see yours? WTL is designed to be extensible.
* **Parquet Power:** Converts your messy log data into clean, columnar Parquet files. This means:
    * Significant storage savings.
    * Faster analytical queries (perfect for tools like Apache Spark, Pandas, DuckDB, etc.).
    * Schema enforcement and evolution.
* **On-the-Fly Decompression:** Got `.gz`, `.bz2`, `.zst` or other common compressed logs? WTL unpacks them seamlessly during processing.
* **Extensible Log Formats:** Uses a `formats` directory containing JSON definitions for log file formats, just like lnav. This makes it easy to add support for new or custom log types.
* **Built with Rust:** Enjoy the performance, safety, and concurrency benefits of Rust.

## ✨ Core Features

* **Rust-Powered Performance:** Process large log volumes quickly and efficiently.
* **lnav-Inspired Format Engine:** Leverages a flexible JSON-based format definition system.
* **Automatic Decompression:** Supports common compression formats out-of-the-box.
* **Schema Inference & Typing:** Attempts to infer appropriate data types for Parquet columns based on format definitions.
* **CLI First:** Easy to integrate into your existing shell scripts and data pipelines.

## 📁 Log Format Definitions

WTL determines how to parse log files using JSON format definitions stored in a `formats` directory. Each `.json` file in this directory describes a specific log format, including how to identify it, parse its lines using regular expressions, and map the extracted data to structured fields.

The schema for these format files is inspired by lnav and aims for compatibility where sensible.

**Key elements of a format definition include:**

* `$schema`: A link to the (eventual) WTL or lnav format schema.
* **Format Name (e.g., `glog_log`, `syslog_log`):** A unique identifier for the format.
    * `title`: A human-readable title for the log format.
    * `description`: A brief description of the log format.
    * `url`: A link to documentation or information about the log format.
    * `regex`: Contains named regular expressions to parse log lines.
        * `pattern`: The PCRE-compatible regular expression with named capture groups (e.g., `?<timestamp>`, `?<level>`).
    * `level-field`: The name of the capture group that represents the log level (e.g., "level", "severity").
    * `level`: Maps log level strings/patterns found in the `level-field` to standardized levels (e.g., error, warning, info).
    * `value`: Defines the data type and properties for each captured field (e.g., `kind: "integer"`, `kind: "string"`, `identifier: true`).
    * `sample`: An array of sample log lines that should match this format, useful for testing.

**Example: Snippet from `glog_log.json`**

```json
{
    "$schema": "[https://lnav.org/schemas/format-v1.schema.json](https://lnav.org/schemas/format-v1.schema.json)",
    "glog_log": {
        "title": "Glog",
        "description": "The google glog format.",
        "url": "[https://code.google.com/p/google-glog/](https://code.google.com/p/google-glog/)",
        "regex": {
            "std": {
                "pattern": "^(?<level>[IWECF])(?<timestamp>\\d{4} \\d{2}:\\d{2}:\\d{2}\\.\\d{6}) +(?<thread>\\d+) (?<src_file>[^:]+):(?<src_line>\\d+)\\] (?<body>.*)"
            }
        },
        "level-field": "level",
        "level": {
            "error": "E",
            "warning": "W",
            "info": "I",
            "critical": "C",
            "fatal": "F"
        },
        "value": {
            "thread": {
                "kind": "integer",
                "identifier": true
            },
            "level": {
                "kind": "string"
            },
            "src_file": {
                "kind": "string",
                "identifier": true
            },
            "src_line": {
                "kind": "integer"
            }
        },
        "sample": [
            {
                "line": "E0517 15:04:22.619632 1952452992 logging_unittest.cc:253] Log every 3, iteration 19"
            }
        ]
    }
}
```

**Example: Snippet from `syslog_log.json`**

```json
{
    "$schema": "https://lnav.org/schemas/format-v1.schema.json",
    "syslog_log": {
        "title": "Syslog",
        "description": "The system logger format found on most posix systems.",
        "url": "http://en.wikipedia.org/wiki/Syslog",
        "regex": {
            "std": {
                "pattern": "^(?<timestamp>(?:\\S{3,8}\\s+\\d{1,2} \\d{2}:\\d{2}:\\d{2}|\\d{4}-\\d{2}-\\d{2}T\\d{2}:\\d{2}:\\d{2}(?:\\.\\d{3,6})?(?:Z|(?:\\+|-)\\d{2}:\\d{2})))(?: (?<log_hostname>[a-zA-Z0-9:][^ ]+[a-zA-Z0-9]))?(?: \\[CLOUDINIT\\])?(?:(?: syslogd [\\d\\.]+|(?: (?<log_syslog_tag>(?<log_procname>(?:[^\\[:]+|[^ :]+))(?:\\[(?<log_pid>\\d+)\\](?: \\([^\\)]+\\))?)?))):\\s*(?<body>.*)$|:?(?:(?: ---)? last message repeated \\d+ times?(?: ---)?))"
            },
            "rfc5424": {
                "pattern": "^<(?<log_pri>\\d+)>(?<syslog_version>\\d+) (?<timestamp>\\d{4}-\\d{2}-\\d{2}T\\d{2}:\\d{2}:\\d{2}(?:\\.\\d{6})?(?:[^ ]+)?) (?<log_hostname>[^ ]+|-) (?<log_syslog_tag>(?<log_procname>[^ ]+|-) (?<log_pid>[^ ]+|-) (?<log_msgid>[^ ]+|-)) (?<log_struct>\\[(?:[^\\]\"]|\"(?:\\.|[^\"])+\")*\\]|-|)\\s+(?<body>.*)"
            }
        },
        "level-field": "body",
        "level": {
            "error": "(?:(?:(?<![a-zA-Z]))(?:(?i)error(?:s)?)(?:(?![a-zA-Z]))|failed|failure)",
            "warning": "(?:(?:(?i)warn)|not responding|init: cannot execute)"
        },
        "opid-field": "log_syslog_tag",
        "multiline": true,
        "module-field": "log_procname",
        "value": {
            "log_pri": {
                "kind": "integer",
                "foreign-key": true,
                "description": "The priority level of the message"
            },
            "syslog_version": {
                "kind": "integer",
                "foreign-key": true,
                "description": "The version of the syslog format used for this message"
            },
            "log_hostname": {
                "kind": "string",
                "collate": "ipaddress",
                "identifier": true,
                "description": "The name of the host that generated the message"
            },
            "log_procname": {
                "kind": "string",
                "identifier": true,
                "description": "The name of the process that generated the message"
            },
            "log_pid": {
                "kind": "string",
                "identifier": true,
                "action-list": [
                    "dump_pid"
                ],
                "description": "The ID of the process that generated the message"
            },
            "log_syslog_tag": {
                "kind": "string",
                "identifier": true,
                "description": "The combination of the procname and pid"
            },
            "log_msgid": {
                "kind": "string",
                "identifier": true
            },
            "log_struct": {
                "kind": "struct"
            }
        },
        "action": {
            "dump_pid": {
                "label": "Show Process Info",
                "capture-output": true,
                "cmd": [
                    "dump-pid.sh"
                ]
            }
        },
        "sample": [
            {
                "line": "Apr 28 04:02:03 tstack-centos5 syslogd 1.4.1: restart."
            },
            {
                "line": "Jun 27 01:47:20 Tims-MacBook-Air.local configd[17]: network changed: v4(en0-:192.168.1.8) DNS- Proxy- SMB"
            },
            {
                "line": "Jun 20 17:26:13 ip-10-188-149-5 [CLOUDINIT] util.py[DEBUG]: Restoring selinux mode for /var/lib/cloud (recursive=False)"
            },
            {
                "line": "<46>1 2017-04-27T07:50:47.381967+02:00 logserver rsyslogd - - [origin software=\"rsyslogd\" swVersion=\"8.4.2\" x-pid=\"900\" x-info=\"http://www.rsyslog.com\"] start"
            },
            {
                "line": "<30>1 2017-04-27T07:59:12+02:00 nextcloud dhclient - - -  DHCPREQUEST on eth0 to 192.168.1.1 port 67"
            },
            {
                "line": "<78>1 2017-04-27T08:09:01+02:00 nextcloud CRON 1472 - -  (root) CMD (  [ -x /usr/lib/php5/sessionclean ] && /usr/lib/php5/sessionclean)"
            },
            {
                "line": "Aug  1 00:00:03 Tim-Stacks-iMac com.apple.xpc.launchd[1] (com.apple.mdworker.shared.0C000000-0700-0000-0000-000000000000[50989]): Service exited due to SIGKILL | sent by mds[198]"
            },
            {
                "line": "Jan  4 10:23:26 Tims-MacBook-Air Setup Assistant[1173]: Creating connection"
            }
        ]
    }
}
```

You can add your own custom format definitions to the formats directory to extend WTL's capabilities.

## 🚀 Getting Started
### Installation
(Pre-compiled binaries will be available for common platforms here once released.)

You can also install from source if you have Rust installed (see Building from Source).

```Bash
# Example with cargo (once published to crates.io)
# cargo install wtl
```

#### Usage
The basic command structure is:

```Bash

wtl <input_log_file_or_directory> -o <output_parquet_file_or_directory> [options]
```
#### Examples:

Convert a single log file:

```Bash
wtl /var/log/syslog -o /data/processed/syslog.parquet
```
Convert a gzipped log file and automatically detect its format:

```Bash
wtl access.log.gz -o analytics_data/access_logs.parquet
```

Convert all log files in a directory (outputs one Parquet file per input log file in the specified output directory):

```Bash
wtl /path/to/your/logs/ -o /path/to/parquet_output/
```

Specify a particular log format to use (if auto-detection fails or you want to force it):

```Bash
wtl my_custom_app.log -o my_custom_app.parquet --format custom_format_name
```
(Where `custom_format_name` corresponds to a definition in the `formats` directory).

#### Options:

* `-o, --output <path>`: Specify the output Parquet file or directory.
* `--format <name>`: Force the use of a specific log format definition.

* `--formats-dir <path>`: Specify a custom directory for log format definitions. Defaults to a built-in or user-config location.
* `-v, --verbose`: Enable verbose output.
* `--debug`: Enable debug output (even more verbose).
(More options to be added)
