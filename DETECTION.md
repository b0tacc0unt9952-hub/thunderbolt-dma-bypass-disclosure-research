# Detection Methodologies for MITM DMA Devices

## Overview

This document provides technical detection methodologies for PCIe Man-in-the-Middle (MITM) DMA devices like Heino 1.2 and 2.0.

**Core principle:** While the devices can clone PCIe device identities, they cannot hide the architectural requirement for a USB 3.2 exfiltration channel.

---

## Primary Detection Vector: PCIe + USB Correlation

### The Architectural Flaw

**Heino devices MUST use both PCIe and USB 3.2 simultaneously:**

- **PCIe:** For device passthrough and DMA memory access
- **USB 3.2:** For control commands and memory data exfiltration

**Legitimate devices NEVER use both PCIe and USB together.**

This creates a reliably detectable pattern regardless of which device they mirror.

### Detection Algorithm

```python
def detect_mitm_dma_device():
    """
    Primary detection method for MITM DMA hardware
    
    Returns: List of suspicious device pairs with confidence scores
    """
    
    suspicious_devices = []
    
    # Enumerate all active PCIe devices
    pcie_devices = enumerate_pcie_devices()
    
    # Enumerate all active USB devices  
    usb_devices = enumerate_usb_devices()
    
    for pcie in pcie_devices:
        for usb in usb_devices:
            
            confidence = 0
            flags = []
            
            # Check 1: Simultaneous activity
            if are_devices_active_simultaneously(pcie, usb):
                confidence += 20
                flags.append("Simultaneous PCIe + USB activity")
            
            # Check 2: Traffic pattern correlation
            if traffic_patterns_correlated(pcie, usb):
                confidence += 30
                flags.append("Correlated traffic patterns")
            
            # Check 3: USB bandwidth anomaly
            usb_bandwidth = get_device_bandwidth(usb)
            if usb_bandwidth > SUSPICIOUS_THRESHOLD:
                confidence += 25
                flags.append(f"High USB bandwidth: {usb_bandwidth}")
            
            # Check 4: Device type mismatch
            if pcie.type in ["NVMe", "GPU", "Network"] and usb.type == "Bulk Transfer":
                confidence += 15
                flags.append("PCIe storage/GPU with bulk USB transfer")
            
            # Check 5: Activity during gameplay
            if in_game_session() and both_devices_active(pcie, usb):
                confidence += 10
                flags.append("Active during gameplay")
            
            # Flag if confidence threshold exceeded
            if confidence >= 50:
                suspicious_devices.append({
                    'pcie_device': pcie,
                    'usb_device': usb,
                    'confidence': confidence,
                    'flags': flags,
                    'severity': 'CRITICAL' if confidence >= 80 else 'HIGH'
                })
    
    return suspicious_devices


def traffic_patterns_correlated(pcie_device, usb_device, window=1.0):
    """
    Check if PCIe and USB traffic patterns are correlated
    
    Args:
        pcie_device: PCIe device object
        usb_device: USB device object  
        window: Time window in seconds for correlation analysis
        
    Returns: Boolean indicating correlation
    """
    
    pcie_activity = get_activity_timeline(pcie_device, window)
    usb_activity = get_activity_timeline(usb_device, window)
    
    # Calculate correlation coefficient
    correlation = calculate_correlation(pcie_activity, usb_activity)
    
    # High correlation suggests devices are working together
    return correlation > 0.7


def are_devices_active_simultaneously(pcie, usb, tolerance=0.1):
    """
    Check if devices show activity in same time windows
    
    Args:
        pcie: PCIe device
        usb: USB device
        tolerance: Timing tolerance in seconds
        
    Returns: Boolean
    """
    
    pcie_timestamps = get_activity_timestamps(pcie)
    usb_timestamps = get_activity_timestamps(usb)
    
    # Check for overlapping activity windows
    for pcie_time in pcie_timestamps:
        for usb_time in usb_timestamps:
            if abs(pcie_time - usb_time) < tolerance:
                return True
    
    return False
```

---

## Detailed Detection Methods

### Method 1: Device Enumeration Pattern Analysis

**What to look for:**

```python
def analyze_device_enumeration():
    """
    Analyze PCIe device enumeration for anomalies
    """
    
    checks = []
    
    # Check 1: Storage device in unusual PCIe slot
    nvme_devices = get_nvme_devices()
    for device in nvme_devices:
        if device.slot not in EXPECTED_M2_SLOTS:
            checks.append({
                'type': 'Unusual slot assignment',
                'device': device.name,
                'slot': device.slot,
                'severity': 'MEDIUM'
            })
    
    # Check 2: Device with dual interface (PCIe + USB)
    all_devices = get_all_devices()
    for device in all_devices:
        interfaces = device.get_interfaces()
        if 'PCIe' in interfaces and 'USB' in interfaces:
            checks.append({
                'type': 'Dual-interface device',
                'device': device.name,
                'interfaces': interfaces,
                'severity': 'HIGH'
            })
    
    # Check 3: Device enumeration timing
    # MITM devices may show slight delay in enumeration
    for device in all_devices:
        enum_time = device.enumeration_time
        if enum_time > NORMAL_ENUM_TIME_THRESHOLD:
            checks.append({
                'type': 'Slow enumeration',
                'device': device.name,
                'time': enum_time,
                'severity': 'LOW'
            })
    
    return checks
```

### Method 2: USB Bandwidth Profiling

**Legitimate vs Suspicious USB Patterns:**

```python
def profile_usb_bandwidth():
    """
    Profile USB bandwidth to detect memory exfiltration
    """
    
    usb_devices = get_usb_devices()
    
    for device in usb_devices:
        
        # Get bandwidth statistics
        bandwidth = measure_bandwidth(device, duration=10.0)
        
        # Legitimate USB devices have specific patterns:
        # - Mouse/Keyboard: Low, sporadic
        # - External storage: High during file transfer, idle otherwise
        # - Webcam: Consistent moderate bandwidth
        
        # DMA exfiltration pattern:
        # - High constant bandwidth during gameplay
        # - Not typical file transfer pattern
        # - Correlates with game activity
        
        if bandwidth.is_constant and bandwidth.average > 50_000_000:  # 50 MB/s
            if in_game_session():
                flag_suspicious_usb(
                    device=device,
                    reason="High constant USB bandwidth during gameplay",
                    severity="CRITICAL"
                )
```

### Method 3: Latency-Based Detection

**MITM passthrough adds measurable latency:**

```python
def measure_pcie_latency():
    """
    Measure PCIe transaction latency to detect passthrough
    """
    
    nvme_devices = get_nvme_devices()
    
    for device in nvme_devices:
        
        # Measure read latency for small operations
        latencies = []
        for _ in range(1000):
            start = time.perf_counter()
            device.read_small_block()
            end = time.perf_counter()
            latencies.append(end - start)
        
        avg_latency = statistics.mean(latencies)
        baseline_latency = get_baseline_latency(device.model)
        
        # MITM adds ~1-5 microseconds overhead
        if avg_latency > baseline_latency * 1.1:
            flag_device(
                device=device,
                reason=f"Elevated latency: {avg_latency}us vs {baseline_latency}us",
                severity="MEDIUM"
            )
```

### Method 4: Memory Access Pattern Analysis

**Detect unusual DMA access patterns:**

```python
def analyze_dma_patterns():
    """
    Analyze DMA access patterns for anomalies
    """
    
    # Monitor which memory regions are being accessed
    dma_accesses = monitor_dma_activity(duration=60.0)
    
    for access in dma_accesses:
        
        # Check 1: Unexpected memory regions
        if access.address in GAME_CRITICAL_MEMORY:
            if access.source_device not in EXPECTED_DEVICES:
                flag_dma_access(
                    access=access,
                    reason="Unexpected device accessing game memory",
                    severity="CRITICAL"
                )
        
        # Check 2: Read-heavy pattern (no writes)
        # DMA cheats typically only read memory, don't write
        if access.read_count > 1000 and access.write_count == 0:
            flag_dma_access(
                access=access,
                reason="Read-only DMA pattern (typical of cheats)",
                severity="HIGH"
            )
        
        # Check 3: Scattered small reads
        # Normal storage: Large sequential reads
        # DMA cheat: Small scattered reads of player positions, health, etc.
        if access.is_scattered and access.avg_size < 256:
            flag_dma_access(
                access=access,
                reason="Scattered small reads (suspicious pattern)",
                severity="MEDIUM"
            )
```

---

## Implementation Guidance

### For Kernel-Level Anticheats

```c
// Example: Windows kernel driver implementation

NTSTATUS DetectMITMDMA() {
    PDEVICE_OBJECT pcie_devices[MAX_DEVICES];
    PDEVICE_OBJECT usb_devices[MAX_DEVICES];
    
    // Enumerate all PCIe devices
    ULONG pcie_count = EnumeratePCIeDevices(pcie_devices, MAX_DEVICES);
    
    // Enumerate all USB devices
    ULONG usb_count = EnumerateUSBDevices(usb_devices, MAX_DEVICES);
    
    // Check for suspicious pairings
    for (ULONG i = 0; i < pcie_count; i++) {
        for (ULONG j = 0; j < usb_count; j++) {
            
            // Check if both active
            if (IsDeviceActive(pcie_devices[i]) && 
                IsDeviceActive(usb_devices[j])) {
                
                // Check traffic correlation
                if (AreDevicesCorrelated(pcie_devices[i], usb_devices[j])) {
                    
                    // Flag for investigation
                    FlagSuspiciousDevices(pcie_devices[i], usb_devices[j]);
                    
                    // Log to anticheat server
                    ReportToServer(pcie_devices[i], usb_devices[j]);
                }
            }
        }
    }
    
    return STATUS_SUCCESS;
}
```

### For User-Mode Anticheats

```cpp
// Example: User-mode detection using Windows APIs

void ScanForMITMDevices() {
    // Get all PCIe storage devices
    std::vector<DeviceInfo> nvme_devices = EnumerateNVMeDevices();
    
    // Get all USB devices
    std::vector<DeviceInfo> usb_devices = EnumerateUSBDevices();
    
    for (const auto& nvme : nvme_devices) {
        for (const auto& usb : usb_devices) {
            
            // Check for suspicious patterns
            if (BothActive(nvme, usb)) {
                
                float correlation = CalculateCorrelation(nvme, usb);
                
                if (correlation > 0.7f) {
                    // High correlation - suspicious
                    
                    // Measure USB bandwidth
                    uint64_t usb_bandwidth = MeasureBandwidth(usb);
                    
                    if (usb_bandwidth > 50'000'000) {  // 50 MB/s
                        // Very suspicious - report
                        ReportSuspiciousDevices(nvme, usb, correlation);
                    }
                }
            }
        }
    }
}
```

---

## Detection Confidence Scoring

### Confidence Levels

**LOW (10-30 points):**
- Single indicator present
- Could be false positive
- Monitor but don't act

**MEDIUM (30-50 points):**
- Multiple weak indicators
- Warrants investigation
- Collect more data

**HIGH (50-80 points):**
- Strong indicators present
- Likely DMA device
- Flag for manual review

**CRITICAL (80+ points):**
- Multiple strong indicators
- Very high confidence
- Take immediate action

### Scoring System

```python
CONFIDENCE_SCORING = {
    # Primary indicators
    'pcie_usb_correlation': 30,
    'high_usb_bandwidth': 25,
    'simultaneous_activity': 20,
    
    # Secondary indicators
    'unusual_slot_placement': 15,
    'dual_interface_device': 15,
    'elevated_latency': 10,
    'scattered_read_pattern': 10,
    'activity_during_gameplay': 10,
    
    # Weak indicators
    'slow_enumeration': 5,
    'non_standard_device_id': 5,
}

def calculate_confidence(flags):
    """Calculate total confidence score from detected flags"""
    return sum(CONFIDENCE_SCORING.get(flag, 0) for flag in flags)
```

---

## Common False Positives

### Legitimate PCIe + USB Scenarios

**Scenario 1: PCIe WiFi Card + USB Bluetooth**
```
Device: PCIe WiFi adapter with USB Bluetooth dongle
Pattern: Both active simultaneously
Correlation: Low (different traffic types)
Bandwidth: Low USB bandwidth
Verdict: LEGITIMATE - Different device types, no correlation
```

**Scenario 2: Thunderbolt Dock**
```
Device: Thunderbolt dock with PCIe tunneling
Pattern: Multiple devices through single connection
Correlation: High (all via dock)
Bandwidth: Variable
Verdict: LEGITIMATE - Documented Thunderbolt dock, known device ID
```

**Scenario 3: Debug/Development Setup**
```
Device: PCIe device + USB debugger
Pattern: Both active during development
Correlation: Moderate
Bandwidth: Low
Verdict: LEGITIMATE - Developer mode enabled, whitelisted scenario
```

### How to Handle False Positives

```python
def filter_false_positives(suspicious_device):
    """
    Filter out known legitimate configurations
    """
    
    # Whitelist known legitimate combinations
    WHITELISTED_COMBOS = [
        ('PCIe WiFi', 'USB Bluetooth'),
        ('Thunderbolt Dock', 'USB Hub'),
        # Add more as needed
    ]
    
    for pcie_type, usb_type in WHITELISTED_COMBOS:
        if (suspicious_device.pcie.type == pcie_type and
            suspicious_device.usb.type == usb_type):
            return True  # Filter out
    
    # Check for developer mode
    if is_developer_mode_enabled():
        return True  # Filter out in dev mode
    
    # Check device reputation
    if has_good_reputation(suspicious_device.pcie) and \
       has_good_reputation(suspicious_device.usb):
        return True  # Known good devices
    
    return False  # Keep as suspicious
```

---

## Tournament / High-Stakes Environment

### Physical Inspection Protocol

For esports tournaments and high-stakes competitions:

```
Pre-Match Inspection:
1. Open player PC case
2. Visually inspect all PCIe slots
3. Verify no interposer devices present
4. Check all devices plug directly into motherboard
5. No intermediary hardware allowed
6. Photograph configuration for reference

During Match:
1. Monitor for unusual USB devices
2. Run detection software continuously
3. Alert on any suspicious patterns

Post-Match:
1. Re-inspect hardware if suspicious activity detected
2. Compare to pre-match photographs
3. Investigate any discrepancies
```

### Sealed PC Strategy

```
Tournament Setup:
1. Provide standardized tournament PCs
2. Sealed cases with tamper-evident seals
3. Known-good hardware configuration
4. Pre-installed and verified software
5. Players cannot modify hardware
6. Any seal break = disqualification
```

---

## Advanced Detection: Hardware Memory Encryption

### Using Intel TME / AMD SME

The most robust defense against DMA attacks:

```python
def enable_memory_encryption():
    """
    Enable hardware memory encryption (Intel TME / AMD SME)
    
    When enabled:
    - Game-critical memory is encrypted
    - DMA reads return encrypted garbage
    - Only CPU with key can decrypt
    - MITM device cannot read meaningful data
    """
    
    # Check if hardware supports encryption
    if cpu_supports_memory_encryption():
        
        # Enable encryption for game process
        enable_process_encryption(game_process_id)
        
        # Encrypt critical memory regions
        encrypt_memory_region(PLAYER_DATA_ADDRESS)
        encrypt_memory_region(GAME_STATE_ADDRESS)
        encrypt_memory_region(ESP_DATA_ADDRESS)
        
        # DMA attacks now read encrypted data
        # Useless without decryption key
        return True
    
    return False
```

**This is cryptographically sound defense:**
- DMA devices cannot decrypt without CPU key
- Renders MITM attack ineffective
- Requires hardware support (modern CPUs)

---

## Deployment Recommendations

### Phased Rollout

**Phase 1: Monitoring (Weeks 1-2)**
```
- Deploy detection in silent mode
- Collect data on device patterns
- Identify baseline false positive rate
- Tune detection thresholds
```

**Phase 2: Logging (Weeks 3-4)**
```
- Enable server-side logging
- Track flagged devices
- Investigate high-confidence cases
- Build evidence database
```

**Phase 3: Enforcement (Week 5+)**
```
- Enable automated responses
- Ban on CRITICAL confidence (80+)
- Flag for review on HIGH confidence (50-80)
- Continue monitoring MEDIUM/LOW
```

### Success Metrics

```
Target Metrics:
- Detection rate: >90% of Heino devices
- False positive rate: <1% of legitimate configs
- Performance impact: <5% CPU overhead
- Response time: <100ms per scan
```

---

## Research & Testing

### Controlled Testing Environment

For anticheats that want to validate these methods:

```
Test Setup:
1. Acquire Heino 1.2 or 2.0 device ($430-$5,122)
2. Set up isolated test environment
3. Run detection algorithms
4. Measure true positive / false positive rates
5. Tune thresholds based on results
6. Deploy to production with confidence
```

**I offered this as a consulting service in January. The offer still stands.**

---

## Contact for Implementation Assistance

If you need help implementing these detection methods:

**Email:** danielcastle888@proton.me

**Services Available:**
- Detection algorithm implementation
- Custom integration for your anticheat
- Controlled testing and validation
- Ongoing threat intelligence
- Training for security teams

**Note:** Rates reflect urgent/reactive market conditions (products already selling) vs. the proactive rates offered in January.

---

## Conclusion

The MITM DMA architecture has a fundamental flaw: **it requires PCIe + USB simultaneously for exfiltration.**

This is detectable, measurable, and cannot be hidden without fundamentally changing the attack architecture.

**Detection is possible. Implementation is straightforward. Defense is achievable.**

The question is: Will anticheat companies implement it?

---

*This research is provided for defensive security purposes only.*

*Do not use this information to develop or deploy cheating tools.*
