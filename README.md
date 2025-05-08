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
