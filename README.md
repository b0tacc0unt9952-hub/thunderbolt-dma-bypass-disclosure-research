# thunderbolt-dma-bypass-disclosure-research
Redacted research on Thunderbolt PCIe DMA bypass via firmware spoofing and commodity adapters. Privately disclosed to Riot Games (HackerOne #35330606) on Jan 29, 2026. Closed ineligible as "physical facilities" after 6 days with no human response. Shared defensively to help anticheat teams strengthen hardware protections against cheating.

# Thunderbolt PCIe DMA Bypass – Defensive Disclosure

Independent hardware security research identifying a platform-level vulnerability allowing undetectable DMA memory reads using Thunderbolt tunneling, firmware identity spoofing, and commodity adapters.

**Disclosure Timeline**
- Jan 29, 2026: Private disclosure to Riot Games (Vanguard) via HackerOne report #35330606  
- Jan 30, 2026: Closed ineligible as "Attacks against physical facilities" after many days with no human engagement or triage update  

Now- Sharing redacted findings publicly to help anticheat developers strengthen defenses against hardware cheating before mass adoption and gets out of control

**Key Vectors (Redacted)**
1. Thunderbolt tunneling for legitimate, undetectable physical connection  
2. Signed driver chain (Intel TB stack) – no unsigned code detection  
<img width="841" height="1515" alt="4082" src="https://github.com/user-attachments/assets/6f507213-0471-4231-829e-ecf5f2b5f261" />
3. Firmware spoofing as benign NVMe/eGPU device  
4. DMA reads without CPU attribution  
5. Behavioral detection gap – novel signature, months to train ML models  
6. Multiple paths (USB-C adapters, M.2 enclosures, USB 4 tunneling)  

**Impact**
- Retrofits existing DMA hardware (Screamer M.2, Karbon FPGA, etc.) to stealth mode for $50–$150  
- Universal threat to laptops (70%+ gaming market) and desktops with TB ports  
- No easy patch without breaking eGPUs/docks/SSDs for legitimate users anticheats want nothing to do with it? 

## Prevention vs Detection (Important Distinction)
Anti-cheats use prevention (BME bit flip, driver blocks) to stop DMA before it starts — this fails behind TB controllers due to abstraction.Firmware doesn't need to be perfect 1-to-1 to beat prevention — good VID/PID + config space spoof is enough for most ACs (they don't deep-scan every SSD).the chain wins because prevention is broken by TB abstraction — direct card gets BME-flipped and flagged, enclosure + spoofed firmware sneaks past both.Good Firmware (Beats Vanguard fw Most Detection, 80–90% UD)What it is: Partial clone of real SSD/eGPU (Samsung/WD VID/PID, legit class code 01-08-02 for NVMe, basic SMART/Identify Controller data).How it beats Vanguard: Looks 80% legit on scan — config space dumps match real devices, I/O tunable to mimic TRIM. Microsoft drivers load it as "external NVMe SSD" — Vanguard thinks "cool storage, no red flags".Why it might leak DMA bits: If behavioral ML sees "SSD doing DMA to game process at odd times", or if firmware doesn't mimic power negotiation perfectly, it can flag anomaly.Odds: 80–90% UD for months if you throttle reads (burst 1MB every 5–10s). Example: Ekknod multimedia firmware he mentioned — good spoof + behavioral mask.God-Tier Firmware (Beats Everything, 95%+ UD)1-to-1 perfect clone of real SSD/eGPU firmware (full PCIe config space, SMART logs, model/serial, queue depth 1024, max sectors 65535, power states mimic, I/O patterns tuned)How it beats Vanguard: Microsoft drivers see "Samsung 980 Pro SSD" or "Razer Core eGPU" — 100% match on all scans. DMA bits don't "leak" because the device is the legit device from Vanguard's view. No anomalies, no flags.Why it works thru Microsoft drivers: Drivers load the "SSD" and allow DMA (NVMe needs it for I/O). Vanguard can't block without breaking real SSDs for legit players. TB tunneling hides the real DMA card behind the spoof. No "leak" — it's all abstracted.Odds: 95%+ UD for years if you avoid behavioral spikes. Example: Custom FPGA bitstream with full NVMe emulation (open-source NVMe CLI base + covert DMA hook).
   Bottom line: TB setup is not the same as direct plug — direct plug leaks DMA bits on enumeration (PCIe VID/PID visible, BME flip works). TB enclosure abstracts it — the DMA "bits" are tunneled and spoofed, so Microsoft drivers see legit SSD/eGPU, Vanguard snoops but sees nothing sus if firmware is good/god-tier. It's like wearing a mask to a party — bad mask = caught, good mask = party on.
 Edge (Why This Works for anyone)
TB + enclosure = prevention bypassed by default (BME flip fails on abstraction).
Good firmware = detection bypassed (looks 1-to-1 legit).
Microsoft drivers = the enabler — they load the spoofed device and allow DMA (can't block without breaking storage for everyone).this setup is better than direct plug — less leak risk, portable, stealthy. bad firmware is a six-pack in your jacket, TB is hiding it in a hollowed-out watermelon. 😂

Detection (scanning firmware hash, behavioral I/O, anomalous descriptors) can still catch bad spoofs. Good firmware (full VID/PID/config/SMART/power state mimic) survives scans — that's why this works undetected in practice (see cheat community examples).

**Mitigations Proposed**
- Memory access attribution via Intel PT  
- Thunderbolt device behavioral profiling  
- Hardware memory encryption (TME/SME integration)  
- Timing-based DMA signature detection  
- Statistical memory bus traffic analysis  
- Kernel-level DMA allowlisting  

**Photos** (attached):  
- Common Thunderbolt/USB-C to M.2 enclosure internals (JMS583 bridge, key M slot exposed)  
- DMA card fit – repro in 30 mins of assembly with commodity hardware  

## Real-World Confirmation
This bypass method is already being used in the cheat community (as of May 2025) with similar hardware chains (TB enclosure + PCIe adapter + FPGA DMA card). Example from UnknownCheats forum:

[Link to the post or screenshot if you saved it]

Key quote: "Working, abused and undetected... with Thunderbolt NVMe enclosure and PCIe adapter... BME bit flip fails due to Thunderbolt abstraction."

This confirms the retrofit path is deployable today and bypasses prevention mechanisms on VGK, FACEIT, EAC, etc.I am not exploiting or selling this. This is defensive research to help close hardware cheating gaps. If you represent an anticheat vendor and want to discuss under NDA, reach out.

No PoC code included — redacted for responsible disclosure.
## Full Redacted Disclosure

[Download PDF](thunderbolt-dma-bypass-disclosure-redacted.pdf) – complete writeup .reply from hackerone saying security doesnt matter dodging payment on big finding.
Hardware security matters.
<img width="1080" height="1337" alt="4181" src="https://github.com/user-attachments/assets/ce3f5402-5b49-49ab-bbee-be4b343cce2d" />
