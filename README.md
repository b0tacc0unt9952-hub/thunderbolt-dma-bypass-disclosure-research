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
