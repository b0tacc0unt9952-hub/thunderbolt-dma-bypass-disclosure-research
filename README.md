# PCIe Man-in-the-Middle DMA Bypass - Disclosure & Vindication

## ⚠️ CRITICAL UPDATE - April 11, 2026

**Commercial products implementing the exact architecture I disclosed in January 2026 are now available for purchase.**

## Timeline of Events

### January 2026: Responsible Disclosure
I disclosed a critical PCIe Man-in-the-Middle (MITM) architecture vulnerability to major anticheat vendors:

- **Threat:** PCIe passthrough/MITM device hiding DMA behind legitimate hardware
- **Architecture:** Real device identity mirroring enabling undetectable memory access
- **Timeline Predicted:** 6-12 months to commercial products
- **Value Assessment:** $400k+ in combined vulnerabilities
- **Consulting Offer:** $50k-$150k for detection methodology development and 90-day head start

### Response: Declined

Anticheat vendors chose not to engage with the disclosure.

### April 11, 2026: Market Validation

Commercial DMA products now selling with **the exact architecture I disclosed**:

**Heino 1.2** - $430  
*"PCIe DMA Board with real M2 chip data acquisition. VT-d IOMMU bypass built in."*

**Heino 2.0** - $5,122  
*"Man-in-the-Middle DMA device — requires NO firmware. Completely undetectable by design."*

**Actual timeline:** 3 months (faster than I predicted)

---

## Why This Matters

### I gave the anticheat industry 3 months warning.

### They chose not to engage.

### Now "completely undetectable" DMA products are selling for $5,000+.

**This is why responsible disclosure matters.**  
**This is why security research has value.**  
**This is what happens when companies ignore warnings.**

---

## The Architecture (Confirmed)

### Their Own Marketing Validates My Disclosure

From heinodma.com:

> "As the world's only MITM hardware device, Heino 2.0 breaks free from traditional DMA limitations."

> "Through Man-in-the-Middle attacks, Heino 2.0 enables full functionality of any PCIe device—acting as genuine hardware."

> "If the game is running directly from the H2 hard drive, how could it possibly be identified as a third-party plugin?"

**This is literally the threat model I disclosed in January.**

### How It Works

```
┌─────────────────────────────────────────────────────┐
│ MOTHERBOARD PCIe SLOT                               │
│                                                     │
│  ↓ System sees only legitimate device              │
│                                                     │
│ ┌─────────────────────────────────────┐            │
│ │ HEINO 2.0 MITM CHIP                 │            │
│ │                                     │            │
│ │ • Mirrors device identity           │────USB 3.2─→ Cheat PC
│ │ • Transparent passthrough           │   (exfil)   │
│ │ • DMA memory reads                  │            │
│ └─────────────────────────────────────┘            │
│                                                     │
│  ↓ Real device traffic flows through               │
│                                                     │
│ ┌─────────────────────────────────────┐            │
│ │ REAL PCIe DEVICE                    │            │
│ │ (NVMe SSD, GPU, Network Card, etc.) │            │
│ └─────────────────────────────────────┘            │
└─────────────────────────────────────────────────────┘

System perception: Legitimate Samsung NVMe SSD
Reality: MITM chip reading memory while mirroring device
```

### Why Current Anticheats Can't Detect It

```
✓ Uses real device drivers (Microsoft-signed, legitimate)
✓ Uses real device firmware (Samsung, Intel, etc.)
✓ Device identity perfectly cloned (passes PCIe enumeration)
✓ Can run games from the device (perfect cover story)
✓ No software on target PC (hardware-level attack)
✓ DMA appears as legitimate device memory access
✓ No IOMMU bypass needed (piggybacks on legitimate DMA)
```

### The Architectural Flaw (Detectable)

**They MUST use PCIe + USB 3.2 simultaneously for data exfiltration.**

**Legitimate storage/GPU devices NEVER use both PCIe and USB together.**

This creates a detectable pattern. See [DETECTION.md](DETECTION.md) for implementation details.

---

## What My January Disclosure Predicted

### My Disclosure (January 2026):

✅ PCIe passthrough/MITM architecture  
✅ Legitimate device as cover  
✅ Device identity cloning/mirroring  
✅ Undetectable by current methods  
✅ Perfect plausible use case (run games from device)  
✅ No firmware signatures needed  
✅ Driver signature bypass  
✅ DMA appearing as legitimate device access  
✅ 6-12 month timeline to commercial products  
✅ Premium pricing ($5k+ for advanced versions)  

### Heino Products (April 2026):

✅ "MITM hardware device" (exact quote)  
✅ "Acting as genuine hardware" (device mirroring)  
✅ "Full functionality of any PCIe device" (identity cloning)  
✅ "0% detection rate" (undetectable claim)  
✅ "Game running from H2 hard drive" (perfect cover)  
✅ "Requires NO firmware" (uses real device firmware)  
✅ Uses real device drivers (signed, legitimate)  
✅ DMA via passthrough architecture  
✅ **3 months to market** (faster than predicted)  
✅ $430-$5,122 pricing (premium tier confirmed)  

**Every single prediction validated.**

---

## The Market Evidence

### From Heino's Own Claims:

> "Heino 1.0 generated a $20 million market, and we reinvested half of that revenue into developing Heino 2.0."

**Translation:**
- 50,000+ units potentially sold (at $400/unit)
- $1M+ R&D investment in advanced version
- Massive scale deployment already happening
- Premium DMA market is real and profitable

### What Companies Lost:

**Proactive Engagement (January):**
- 3-month head start on detection methods
- Controlled testing environment
- Detection ready before market launch
- Competitive advantage
- Cost: $50k-$150k consulting

**Reactive Response (April):**
- Products already selling at scale
- "Completely undetectable" marketing active
- Emergency response required
- Playing catch-up to commercial products
- Cost: Millions + reputation damage

---

## Defensive Research (Now Public)

Since commercial products are already available and companies declined to engage, I'm publishing my defensive research to help the security community develop protections.

**Full detection methodologies:** [DETECTION.md](DETECTION.md)

**Key detection vector:** PCIe device + USB 3.2 simultaneous activity with correlated traffic patterns

This architectural flaw exists in both Heino 1.2 and 2.0, and any future MITM-based DMA device that requires an exfiltration channel.

---

## Evidence & Product Links

**Commercial Products:**
- Heino DMA Official Site: https://heinodma.com/
- DMA Kingdom: https://dmakingdom.net/
- Project7.dev: https://project7.dev/product/heino-20
- Ducks Services: https://ducks-services.com/

**Product Specifications:**
- Heino 1.2: M.2 passthrough DMA with IOMMU bypass ($430)
- Heino 2.0: Advanced PCIe MITM device with fan cooling ($5,122)

**Marketing Claims:**
- "World's only MITM hardware device"
- "0% detection rate"
- "Completely undetectable by design"
- "$20 million market generated"

All product claims directly match my January disclosure.

---

## For Anticheat Developers

If you're working on client-side anticheat and need help defending against these commercial products, the research is here. 

I offered to help proactively in January. That offer was declined. 

This defensive research is now freely available.

### Quick Start Detection:

```python
# Primary detection vector
if pcie_storage_device and usb_device_active_simultaneously:
    if traffic_patterns_correlated and usb_bandwidth_high:
        FLAG: "Possible MITM DMA hardware"
```

See [DETECTION.md](DETECTION.md) for complete implementation guidance.

---

## Lessons Learned

### For Security Researchers:
- **Document everything** with timestamps and evidence
- Responsible disclosure is the right approach even when ignored
- Market will prove your research right or wrong
- Public disclosure is appropriate when commercial products already exist
- Your work has value even if companies don't see it immediately

### For Companies:
- **Early warnings have strategic value** - 3-month head start vs emergency response
- **Proactive is cheaper than reactive** - $150k consulting vs $M+ crisis response
- **Ignoring researchers doesn't prevent threats** - products launched anyway
- **Security researchers aren't trying to extort you** - we're trying to help
- **"It won't happen" doesn't mean it can't happen** - it did, exactly as warned

### For The Community:
- **Anticheat is an arms race** - hardware attacks are the new frontier
- **No client-side solution is truly "undetectable-proof"** - detection is possible
- **Demand better from game companies** - they can and should prepare for these threats
- **Support security research** - researchers find problems before criminals scale them

---

## Current Status

**Products:** Available for purchase now on multiple marketplaces  
**Detection Methods:** Published in this repository  
**Consulting:** Available for detection implementation assistance  
**Timeline:** Anticheat companies are 3+ months behind the threat  

---

## Contact

**Consulting on detection implementation:**  
danielcastle888@proton.me

Rates reflect urgent/reactive market conditions vs. the proactive rates offered in January.

---

## Disclosure Ethics Statement

I followed responsible disclosure practices:

1. **Private disclosure** to affected vendors (January 2026)
2. **Reasonable timeline** for response and remediation (90+ days)
3. **Good faith engagement** attempts with detailed technical analysis
4. **Public disclosure** only after commercial products made threat public
5. **Defensive focus** - publishing detection methods, not attack tutorials

The threat became public through commercial product launches, not through my disclosure.

I'm publishing defensive research now because:
- Products are already available to anyone with $430-$5,122
- Companies declined to engage with private disclosure
- Security community needs defenses against active threats
- Detection methodologies help defenders, not attackers

---

## Repository Contents

- **README.md** (this file) - Overview and timeline
- **VINDICATION.md** - Detailed disclosure narrative and what companies lost
- **DETECTION.md** - Technical detection methodologies and implementation
- **EVIDENCE/** - Screenshots, product links, and timeline documentation

---

*I tried to help. They said no. Now it's happening exactly as I warned. This is why security research matters.*

**— Daniel Castellani**  
**Independent Security Researcher**  
**April 11, 2026**
