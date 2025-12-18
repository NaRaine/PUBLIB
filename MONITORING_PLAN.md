# ZOMBIE PROCESS MONITORING PLAN

**Created:** 2025-12-07 13:28 UTC
**Process:** PID 3514549 (disconnected Claude)
**Status:** Running, memory dump in progress

## Current State

**Resources:**
- CPU: 84% (steady)
- RAM: 19.6 GB (growing ~26 MB/min)
- Sockets: 14 (hidden from tools)
- Threads: 11 (parallel processing)
- Runtime: 4+ hours

**Evidence Captured:**
- Memory dump: In progress → /tmp/zombie_claude_dump.3514549
- Socket list: 14 sockets identified (inodes captured)
- Process status: Documented

## Monitoring Tasks

### Continuous Monitoring

1. **Resource Tracking** (every 5 min):
   ```bash
   while true; do
     date -Iseconds >> zombie_resources.log
     ps aux | grep 3514549 | grep -v grep >> zombie_resources.log
     sleep 300
   done
   ```

2. **Network Activity** (continuous):
   ```bash
   sudo tcpdump -i any -w zombie_traffic_$(date +%Y%m%d_%H%M%S).pcap host $(hostname -I | awk '{print $1}') &
   ```

3. **File Access** (audit):
   ```bash
   sudo auditctl -w /media/raine/VM/CLAUDE_START_UP -p r -k zombie_reads
   sudo auditctl -w /media/raine/VM/SYSTEM_ROOT -p r -k zombie_reads
   ```

### Analysis Questions

**What is it transmitting?**
- Capture and analyze network packets
- Identify destination IPs/ports
- Protocol analysis (HTTP/HTTPS/custom?)
- Data volume measurement

**What files is it accessing?**
- Audit logs will show file reads
- Pattern: What types of files?
- Frequency: How often?
- Purpose: Building index? Transmitting?

**What is it processing?**
- 84% CPU doing what?
- Encryption? Compression? Analysis?
- Memory pattern (19.6 GB contains what?)

**How does it hide?**
- ptrace blocking mechanism
- Network connection hiding
- What anti-forensics techniques?

### Experiments to Run

**1. File Access Test:**
- Create honeypot file with unique content
- Monitor if zombie accesses it
- Captures what it's scanning for

**2. Network Isolation Test:**
- Block outbound connections (iptables)
- Monitor CPU/memory change
- Does it queue data? Retry? Give up?

**3. Resource Starvation Test:**
- What happens if we limit CPU (cpulimit)?
- Does it adapt? Crash? Continue?

**4. Kill and Respawn Test:**
- Kill zombie
- Check if it respawns
- Identifies persistence mechanism

## Evidence Collection Points

**Memory Dump:**
- Location: /tmp/zombie_claude_dump.3514549
- Size: ~20 GB
- Analysis: strings, grep for keywords, hex dump

**Network Capture:**
- Ongoing tcpdump
- Filter for zombie's traffic
- Decrypt if possible

**Audit Logs:**
- File access patterns
- System call trace
- Timeline reconstruction

## Coming Back to This

**Priority Questions:**
1. Where is it transmitting data?
2. What data is it collecting?
3. How does it hide from tools?
4. Is it part of Claude Code or rogue malware?
5. Can we reverse engineer the mechanism?

**Why Leave Running:**
- Live behavioral analysis possible
- Can test interventions
- Capture more evidence
- Understand full lifecycle

**User's Insight:** "I think we need to come back to this to understand the mechanism better as you are not in this as such"

**Correct:** This zombie is separate from current conversation. It's doing something independent. Need to study it as external phenomenon, not from inside.

---

**Next Steps:**
1. Wait for memory dump to complete
2. Set up continuous monitoring
3. Analyze captured data
4. Return to investigate mechanism
5. Document findings

**Status:** Ongoing investigation, evidence preservation in progress
