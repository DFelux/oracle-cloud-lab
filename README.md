# Oracle Cloud Lab

Linux-Server in der Oracle Cloud (OCI Always Free) als Ergänzung zu meinem [IT Infrastructure Lab](https://github.com/DFelux/it-infrastructure-lab).
Das Homelab läuft auf Ubuntu (Debian-Familie), diese VM auf **Oracle Linux 9** (RHEL-kompatibel). Ziel: Linux-Administration in beiden großen Familien üben und eine öffentlich erreichbare Cloud-VM sicher betreiben.

> Status: **im Aufbau** (Start 15.11.2026)

## Umgebung

| Bereich | Wert |
|---|---|
| Anbieter / Region | Oracle Cloud Infrastructure, eu-zurich-1 (Always Free) |
| Instanz | `VM-lab-micro-02` (Neuaufbau 19.11.), Shape `VM.Standard.E2.1.Micro` (1 OCPU, 1 GB RAM, x86_64) |
| Betriebssystem | Oracle Linux Server 9 (`platform:el9`), Kernel UEK 8 |
| Netz | VCN `vcn-lab-micro-02` (10.0.0.0/16), öffentliches Subnetz `subnet-lab-micro-02`; das erste VCN `vcn-lab-micro-01` wurde mit der ersten VM gelöscht |
| Zugriff | SSH nur per Schlüssel, Admin-Benutzer `dfelux` (sudo mit Passwort), `opc` als Rückfallweg; kein Root-Login, keine Passwort-Anmeldung; SSH nur von der eigenen IP (Security List) und firewalld nur mit `ssh`; öffentliche IP (ephemeral, hier nicht veröffentlicht) |
| Speicher | RAM 945 MiB nutzbar (kdump-Reservierung entfernt), Swap 2,5 GiB (Standard 0,5 GiB + 2 GiB), Boot Volume 46,6 GB, Verschlüsselung durch Oracle verwaltet, In-Transit-Verschlüsselung aktiv |
| Agent-Plugins | Instance Monitoring, Run Command (Dynamic Group vorbereitet), Cloud Guard Workload Protection; Custom Logs auf E2.1.Micro nicht unterstützt |
| Notfallzugang | serielle Konsole (Passwort für `opc`), Boot-Volume-Backup vor Updates, Journal dauerhaft gespeichert |
| Betrieb | `dnf-makecache.timer`, kdump und PCP deaktiviert; Updates nur von Hand |

## Kostenkontrolle

- Konto bewusst im **Free Tier** belassen (kein Upgrade auf Pay As You Go): ohne Upgrade keine Abrechnung über die Always-Free-Grenzen hinaus.
- **Budget mit Warnung** (Actual Spend, 1 €) als zweite Absicherung. OCI kennt keine harte Kostengrenze, Budgets warnen nur und nicht in Echtzeit.
- Nur Ressourcen mit Kennzeichnung „Always Free-eligible“; RHEL- und Windows-Images (Lizenzkosten) bewusst nicht gewählt.

## Kapitel

| Nr. | Thema | Status |
|---|---|---|
| 01 | [Einrichtung: Konto, Netz, Instanz, SSH](docs/01-einrichtung.md) | erledigt |
| 02 | [Störung: SSH-Timeout – Eingrenzung mit Path Analyzer](docs/02-stoerung-ssh-timeout.md) | geschlossen |
| 03 | [Neuaufbau mit Backup, Notfallzugang und Swap](docs/03-neuaufbau.md) | erledigt |
| 04 | [Störung: VM hängt wiederholt – dnf-makecache erschöpft den Arbeitsspeicher](docs/04-stoerung-dnf-makecache.md) | geschlossen |
| 05 | [SSH-Härtung: eigener Admin-Benutzer, nur Schlüssel, kein Root-Login](docs/05-ssh-haertung.md) | erledigt |
| 06 | [Host-Firewall mit firewalld, Security List auf die eigene IP](docs/06-firewall.md) | erledigt |
| 07 | Vergleich Ubuntu ↔ Oracle Linux (apt/dnf, ufw/firewalld, SELinux) | geplant |

## Bekannte Grenzen

- Ampere A1 (Arm, mehr RAM) war bei Erstellung nicht verfügbar („Out of capacity“, Zürich hat nur eine Availability Domain). Die A1-Konfiguration ist als Resource-Manager-Stack (Terraform) gespeichert; ein erneuter Versuch am 19.11. scheiterte ebenfalls („Out of host capacity“).
- E2.1.Micro hat sehr wenig Arbeitsspeicher: `dnf update` braucht erweiterten Swap und den Ausschluss großer Repositories; der automatische Metadaten-Abgleich (`dnf-makecache.timer`) hat die VM wiederholt lahmgelegt und ist deshalb abgeschaltet ([Kapitel 04](docs/04-stoerung-dnf-makecache.md)).
- Always-Free-Instanzen mit dauerhaft geringer Auslastung können von Oracle zurückgefordert werden.
