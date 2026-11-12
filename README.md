# suricata-ruleset

Suricata detection rules and network traffic analysis write-ups for malware, built from publicly available PCAP files and validated against those samples.

> These rules are research and learning material, not a drop-in production ruleset. Rules labelled `HUNTING` are intentionally broad and need tuning against your own traffic before alerting on them.

## Coverage

| Family | Rules file | SIDs | Notes |
|---|---|---|---|
| AMOS (Atomic macOS) Stealer, plaintext HTTP variant | `rules/amos-stealer-v2.rules` | 1000040–1000046 | Beacon JSON, zip exfil upload, archive structure. SID 1000046 (QUIC SNI) is disabled: untested on Suricata 7.0.3 |
| AMOS, `stage=` URL variant (Unit 42 report) | `rules/amos-stealer.rules` | 1000030–1000032 | Does not match the plaintext variant above; kept for the variant it was written for |
| SmartApeSG ClickFix chain | `rules/smartapesg.rules` | 1237232–1237240 | Staging, DNS/SNI, and a behavioural rule for non-TLS traffic on TCP/443 |

## Rule conventions

- `MALWARE`: structural or high-confidence match for the observed sample.
- `HUNTING`: broader signal, expect false positives, tune before use.
- `POLICY`: context only, not an indicator by itself.
- SIDs are unique across the whole repository.

## Usage

```bash
# validate syntax
suricata -T -c /etc/suricata/suricata.yaml -S rules/smartapesg.rules

# run against a PCAP
suricata -S rules/smartapesg.rules -k none -r sample.pcap -l out
grep -oE '\[1:[0-9]+:[0-9]+\] [^[]+' out/fast.log | sort | uniq -c
```

## Testing and limitations

- Tested on Suricata 8.0.6 using open-source PCAP files. 
- Negative testing is still limited: these rules were also run on a small set of unrelated PCAP files, which cannot replace a secure corporate data corpus.
- Indicators may vary between variants within the same family (for example, a field may move from the URL parameter to the JSON body), so each rule specifies the sample from which it was derived.
- Domain and IP indicators are essentially temporary; structural rules are expected to last longer.


Translated with DeepL.com (free version)

## PCAP sources

PCAPs are not included in this repository. They come from [malware-traffic-analysis.net](https://www.malware-traffic-analysis.net/)
