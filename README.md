# thunderbolt-dma-bypass-disclosure-research
Redacted research on Thunderbolt PCIe DMA bypass via firmware spoofing and commodity adapters. Privately disclosed to Riot Games (HackerOne #35330606) on Jan 29, 2026. Closed ineligible as "physical facilities" after 6 days with no human response. Shared defensively to help anticheat teams strengthen hardware protections against cheating.

# Thunderbolt PCIe DMA Bypass – Defensive Disclosure

Independent hardware security research identifying a platform-level vulnerability allowing undetectable DMA memory reads using Thunderbolt tunneling, firmware identity spoofing, and commodity adapters.

**Disclosure Timeline**
- Jan 29, 2026: Private disclosure to Riot Games (Vanguard) via HackerOne report #35330606  
- Jan 30, 2026: Closed ineligible as "Attacks against physical facilities" after 6 days with no human engagement or triage update  
- Sharing redacted findings publicly to help anticheat developers strengthen defenses against hardware cheating before mass adoption

**Key Vectors (Redacted)**
1. Thunderbolt tunneling for legitimate, undetectable physical connection  
2. Signed driver chain (Intel TB stack) – no unsigned code detection  
3. Firmware spoofing as benign NVMe/eGPU device  
4. DMA reads without CPU attribution  
5. Behavioral detection gap – novel signature, months to train ML models  
6. Multiple paths (USB-C adapters, M.2 enclosures, USB 4 tunneling)  

**Impact**
- Retrofits existing DMA hardware (Screamer M.2, Karbon FPGA, etc.) to stealth mode for $50–$150  
- Universal threat to laptops (70%+ gaming market) and desktops with TB ports  
- No easy patch without breaking eGPUs/docks/SSDs for legitimate users  

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

I am not exploiting or selling this. This is defensive research to help close hardware cheating gaps. If you represent an anticheat vendor and want to discuss under NDA, reach out.

No PoC code included — redacted for responsible disclosure.

Hardware security matters.
