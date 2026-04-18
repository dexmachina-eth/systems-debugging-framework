# systems-debugging-framework

A comprehensive mental model and technical framework for troubleshooting complex distributed systems. This repository defines a structured approach to identifying, isolating, and resolving system-level failures.

## Debugging Philosophy

1. **Be Methodical, Not Random**: Avoid the "shotgun" approach. Formulate a hypothesis based on evidence before taking action.
2. **Isolate the Fault Domain**: Use a binary search approach (e.g., local vs. remote, frontend vs. backend) to narrow the problem space.
3. **Verify Assumptions**: Always check the simplest possible point of failure (e.g., Is the server on? Is the cable plugged in? Is the service listening?).
4. **Reproduce the Failure**: If you cannot reproduce the failure, you cannot reliably verify the fix.
5. **Blame-Free Root Cause Analysis**: Focus on the technical and procedural failure, not the individual.

## Layered Debugging Model

When troubleshooting, move from the lowest layer to the highest to ensure the foundation is stable.

1. **DNS**: Can the hostname be resolved? (`dig`, `nslookup`)
2. **Network (Connectivity)**: Is the destination reachable on the required port? (`ping`, `nc`, `traceroute`, `telnet`)
3. **Compute (OS/Kernel)**: Is the server responsive? Are resources available? (`df`, `free`, `top`, `dmesg`, `uptime`)
4. **Application (Process)**: Is the process running and listening? (`systemctl`, `ss`, `ps`, `lsof`)
5. **Dependencies (Upstream)**: Are external APIs or databases responding? (`curl`, `sqlcmd`, `telnet`)

## Triage Flow

1. **Detection**: Identify the deviation from baseline behavior.
2. **Scope**: Quantify the impact (e.g., One server? One region? All users?).
3. **Initial Triage**: Rapid checks for "easy wins" (e.g., disk full, service crashed).
4. **Deep Investigation**: Detailed log analysis, tracing, and metric inspection.
5. **Resolution**: Apply a temporary fix (mitigation) or permanent fix (remediation).
6. **Post-Mortem**: Document the incident and implement preventative measures.

## Command Reference (The Toolkit)

### Networking
- `curl -I <url>`: Check HTTP headers and connectivity.
- `nc -zv <host> <port>`: Verify TCP port reachability.
- `dig <hostname> +short`: Fast DNS lookup.
- `ss -tulpn`: List all listening TCP/UDP ports and their processes.

### System Resources
- `df -h`: Check disk space across all partitions.
- `free -h`: Check available system memory.
- `top -b -n 1`: Capture a snapshot of CPU and memory by process.
- `journalctl -u <service> -f`: Real-time log monitoring for a systemd service.

### Process & Files
- `ps aux | grep <name>`: Find a specific running process.
- `lsof -p <pid>`: List all open files (and network sockets) for a process.
- `strace -p <pid>`: Trace system calls of a running process (caution in production).

## How to Isolate Issues

- **Check Local First**: If a service fails externally, test it from the server itself. If it works locally, the issue is likely in the network/firewall layer.
- **Compare Known-Goods**: Compare the configuration and logs of a failing server with a healthy one in the same cluster.
- **Toggle Features**: Use feature flags or configuration toggles to isolate whether a new feature is causing the failure.
- **Binary Search the Pipeline**: Bypass the Load Balancer, then the API Gateway, then call the backend directly to see where the request is dropped.

## Debugging Tools Glossary

- **mtr**: Combination of ping and traceroute for network path analysis.
- **strace**: Traces system calls and signals (for deep process analysis).
- **tcpdump**: Command-line packet analyzer for network troubleshooting.
- **journalctl**: Query and display logs from journald.
