# Path Traversal Vulnerability

## Overview

Path Traversal, also known as Directory Traversal, is a web security vulnerability that allows an attacker to access files and directories outside the intended location on the server. It occurs when an application uses user-supplied input to construct file paths without proper validation.

By manipulating path sequences such as `../`, attackers can traverse the filesystem and access sensitive files containing configuration data, credentials, source code, or other confidential information. In some cases, Path Traversal vulnerabilities may lead to further attacks, including information disclosure and remote code execution.

Proper input validation and path canonicalization are essential to prevent unauthorized file access.
